# Gateway & Local Runtime

The gateway is Empyralis's persistent local runtime edge. It connects your physical computer to the cloud brain through a secure, durable WebSocket connection. It owns personal channel sessions, browser automation, device capabilities, and local tool execution.

## Why A Gateway?

Most AI platforms run entirely in the cloud. They can't touch your files, open your browser, run shell commands, or capture screenshots. The ones that CAN run locally are developer tools — they assume you're comfortable with a terminal.

Empyralis needs both: the reliability and identity of a cloud brain, plus the power and privacy of local execution. The gateway is the bridge.

## Architecture

```
Cloud Control Plane ←─WSS─→ empyralis-gateway ←─loopback─→ empyralis-supervisor
(identity, policy,         (TypeScript daemon,           (Rust daemon,
 pairing, runs,             persistent process,           privileged device
 memory, approvals)         journal/outbox/checkpoints,   capabilities)
                            personal channels,
                            browser runtime)
```

**Three components, three responsibilities:**

| Component | Language | Role | Must Not |
|---|---|---|---|
| Cloud Control Plane | Python/FastAPI | Identity authority, pairing, run truth, policy, memory, billing | Own personal session files on the device |
| empyralis-gateway | TypeScript/Node.js | Persistent local process, WSS client, channel sessions, journal/outbox, browser, local routing | Become a second product brain or separate auth system |
| empyralis-supervisor | Rust | Narrow capability daemon: screenshot, OCR, mouse, keyboard, clipboard, app launch, AppleScript | Own channel sessions, cloud sockets, identity, or durable messaging state |

## 8-Phase Device Lifecycle

```
Pair → Register → Connect → Heartbeat → Operate → Degrade → Recover → Revoke
```

1. **Pair** — User initiates pairing from the web UI or mobile app. Cloud issues a scoped pairing grant.
2. **Register** — Gateway creates local persistent state. Cloud registers `device_id` and `gateway_id`.
3. **Connect** — Gateway opens one outbound WSS connection to cloud. Cloud binds the live socket to the paired identity tuple.
4. **Heartbeat** — Gateway sends regular liveness, capability, and health updates. Cloud tracks online/offline/degraded.
5. **Operate** — Cloud sends tool work, approvals, or channel messages through the gateway session. Gateway fans out locally.
6. **Degrade** — If the socket drops, gateway records local journal state. Work may be paused, retried, or held per policy.
7. **Recover** — Gateway reconnects. Cloud and gateway resume from acknowledged sequence and checkpoint state.
8. **Revoke** — Cloud can revoke the gateway or device. Revoked sessions must stop working immediately on reconnect.

## Protocol

**Transport:** Single outbound WSS connection from gateway to cloud. Cloud never opens inbound sockets to the device.

**Identity tuple on every frame:** `tenant_id + workspace_id + user_id + device_id + gateway_id`

**Frame types:**
```json
// Request (gateway → cloud or cloud → gateway)
{ "kind": "request", "id": "req_123", "type": "gateway.connect",
  "ts": "2026-05-02T12:00:00Z", "scope": { ... }, "payload": {} }

// Response
{ "kind": "response", "id": "req_123", "ok": true,
  "ts": "2026-05-02T12:00:01Z", "payload": {} }

// Event (bidirectional)
{ "kind": "event", "type": "gateway.presence", "seq": 42, "ack": 41,
  "ts": "2026-05-02T12:00:02Z", "scope": { ... }, "payload": {} }
```

**Ordering & replay:** Every event carries a monotonically increasing `seq`. The peer may include `ack` for the highest durably processed sequence. Replay is allowed from the last durable ack point. Handlers must be idempotent.

**Implemented message families:**
- `gateway.connect`, `gateway.hello`, `gateway.heartbeat`, `gateway.presence`, `gateway.disconnect`, `gateway.state.update`
- `tool.invoke`, `tool.interrupt`
- `channel.inbound`, `channel.outbound`
- Reserved for future: `approval.request`, `approval.result`, `checkpoint.update`

## Durability Primitives

### Journal
Immutable append-only event log stored as NDJSON. Every gateway lifecycle event, every state transition, every reconnect is journaled with a monotonically increasing cursor. The journal survives process restarts.

### Outbox
Replayable message queue. Every outbound message is stored with status tracking:
- `pending` → `acknowledged` (delivered)
- `pending` → `failed` (non-replayable error)
- `pending` → `pending` (replayable error, retry on reconnect)

On reconnect, all replayable pending messages are replayed in creation order.

### Checkpoints
Session state snapshot: session ID, expiry, last acknowledged sequence, health state, pending outbox count, recovery readiness. Written atomically on every state change. Used for cold boot recovery.

## Device Capabilities

The gateway aggregates capabilities from three sources:

| Source | Capabilities |
|---|---|
| **Browser runtime** | `browser.session.start`, `browser.session.action`, `browser.session.resume`, `browser.session.interrupt`, `browser.session.takeover` |
| **Supervisor** | `screenshot.capture`, `computer_control.click`, `computer_control.type`, `computer_control.move`, `computer_control.key`, `computer_control.ocr`, `computer_control.clipboard_read`, `computer_control.clipboard_write`, `computer_control.list_windows`, `computer_control.list_apps`, `computer_control.launch`, `computer_control.launch_app`, `computer_control.notify`, `computer_control.applescript`, `computer_control.speak`, `filesystem.read_write`, `shell.execute` |
| **Channel runtimes** | `channel.whatsapp.personal`, `channel.whatsapp.personal.inbound`, `channel.whatsapp.personal.outbound`, `channel.telegram.personal`, `channel.telegram.personal.inbound`, `channel.telegram.personal.outbound` |

All 35 capabilities are declared in the gateway's capability manifest at registration. The cloud control plane decides which capabilities are available to which agent based on policy.

## Browser Runtime

The gateway owns a persistent browser runtime. Unlike one-shot browser automation, Empyralis's browser supports:

- **Persistent sessions** — Chrome/Electron profile on disk. Cookies, logins, and storage survive restarts.
- **Checkpoint/resume** — Browser can pause mid-task (CAPTCHA, login, human confirmation), save its state (current URL, last completed action, next action, open tabs, console logs, network failures, accessibility snapshot), and resume from the exact next action.
- **Multi-tab state** — Checkpoint includes all open tabs with URLs and active state.
- **Approval binding** — Browser plan hash is recomputed immediately before spawning the browser process. If the approved plan and the executed plan differ, execution is rejected.
- **Headless support** — Can run without a visible window for automated tasks.

## Channel Runtimes

Personal channel runtimes live inside the gateway process:

### WhatsApp Personal Runtime
- Uses Baileys (WhatsApp Web protocol)
- QR code pairing or 6-digit code pairing
- Persistent session across restarts
- Inbound/outbound message routing through cloud
- Coming: typing indicators, draft streaming, pairing codes

### Telegram Personal Runtime
- Uses MTProto (Telegram client protocol)
- Phone number + login code authentication
- Session string persistence
- Inbound/outbound message routing through cloud
- Coming: typing indicators, draft streaming, pairing codes

### Channel Lifecycle
Both runtimes follow the same lifecycle:
```
configure → connect → online → (disconnect → reconnect)* → stop
```
The gateway handles all reconnect logic. The cloud control plane handles all pairing and identity.

## Reconnect

The gateway runs an infinite reconnect loop:
1. Connect (open WSS, send `gateway.connect`, receive `gateway.hello`)
2. Operate (heartbeat, handle requests, publish events)
3. On socket close → classify error as retryable or non-retryable
4. If retryable → exponential backoff with jitter → goto 1
5. If non-retryable (401, 403, revoked, credentials invalid, scope mismatch) → exit

Heartbeat failures after 2 consecutive misses trigger health degradation. Successful heartbeats trigger health recovery.

## Startup & Cold Boot

On startup:
1. Acquire process lock (prevents duplicate gateways for the same state directory)
2. Initialize state DB, journal, outbox, checkpoints
3. Load or create device identity
4. Register with cloud (if pairing token present) or load stored gateway token
5. Start channel runtimes
6. Connect WSS and begin operating

On cold boot (process was killed), the gateway restores from its last checkpoint. Pending outbox messages are replayed. The cloud resynchronizes from the last acknowledged sequence.

## Security Model

- All sessions tied to the primary tenant/workspace/user model
- Gateway tokens are revocable and time-bounded
- Cloud rejects mismatched identity tuple on any frame
- Protocol cannot downgrade into anonymous local runtime trust
- Sensitive channel auth material stays on the local device
- Supervisor runs as a separate process with HMAC-signed capability requests
