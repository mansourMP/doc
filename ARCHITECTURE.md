# Architecture

## The 4-Layer OS Model

Empyralis is structured as four strictly separated layers. No layer collapses into another. Cross-layer access requires explicit contracts.

### Layer 1: Sage — Personal Captain

Sage is the user's main agent. One identity per user. One primary relationship.

**Sage owns:**
- The main relationship with the user
- Personal memory (SOUL, IDENTITY, HEARTBEAT, CONTEXT)
- Provider selection and tools
- Direct chat and personal work sessions

**Sage does not:**
- Control Studio agents
- Inherit Studio memory or execution state
- Support external customer-live mode switching
- Bypass runtime, tool, secret, or policy brokers

### Layer 2: Studio Agents — Specialist Workers

Studio agents are deployable, scoped, independent workers. Each agent has its own memory, tools, connectors, artifact history, runtime policy, and activity stream.

**Operating modes:**
- `owner_edit` — authoring mode, allows prompt/config edits
- `owner_test` — owner preview mode, no config edits
- `customer_live` — external-audience mode, no config edits

**Isolation contract:**
Studio agents do not share memory with Sage. They do not automatically inherit each other's memory. The only shared layer is auth, billing, and channel infrastructure.

### Layer 3: Applications — Product Modules

Applications are hosted mini-apps that run in an embedded browser surface. They can run workflows, call models, and perform structured backend actions — but only through explicit bridge contracts.

**Applications can (with explicit grants):**
- Call specialists or Sage through typed bridge contracts (`app_to_sage`, `app_to_specialist`)
- Access scoped documents and payloads
- Read app-owned history and user-selected inputs

**Applications cannot (by default):**
- Read Sage memory
- Read specialist memory
- Read private user context without an explicit contract
- Access the local machine or gateway

### Layer 4: Platform Control Plane

The control plane governs all three layers above. It owns: tenant and workspace identity, runtime profiles and attachments, entitlements and quotas, memory routing rules, tool and secret brokering, activity ledgering, approvals, security controls, and hybrid sync and placement policy.

## Turn Architecture

Every request enters through one door. There is no parallel execution path.

```
User Request → agent_turn.py → "Is this a question or a task?"
    │
    ├─ Lightweight → direct_chat → SSE stream → reply
    │  (question words: "what", "why", "how", "tell me")
    │
    └─ Serious → durable run → runs_delegation → specialist agents
       (task markers: "send", "draft", "create", "analyze")
```

The decision is made by `server_modules/agent_turn.py` using explicit keyword matching. Lightweight questions get fast answers via streaming. Tasks with side effects enter the durable execution path with approvals and audit trails.

**Canonical paths:**

```
Web Turn Ingress:
  server.py → /turn → agent_turn → turn_runtime → direct chat OR durable execution

Durable Run Start:
  caller → turn_runtime → run_service → agent_turn → execute_durable_turn_request

Channel Ingress:
  connector → agent_channel_router → build_channel_turn → agent_turn → turn_runtime

Memory:
  agent_channel_router → deployed_agent_memory_service → build_channel_turn
```

## Runtime Trust Zones

Three trust zones determine where and how work executes:

| Zone | What Runs Here | Default Policy |
|---|---|---|
| `shared_cloud` | Cloud-hosted execution | Reads, search, drafting auto-run. Writes and sends gate. |
| `user_owned_local` | Your laptop, Mac Mini, home server | Broader auto-run in Full Access. Still gates email sends, purchases, credential changes. |
| `owned_dedicated_runtime` | Dedicated hardware for business workloads | Can default to Full Access for internal work. Gates external actions. |

## Execution Modes

| Mode | Description |
|---|---|
| **Default** | Uses low-risk tools. Asks before risky actions. |
| **Full Access** | Elevated device capabilities for a session. Time-bounded, auditable, per-task. |

Elevated mode is always: per-task, time-bounded, runtime-aware, auditable, and action-class-aware.

## Lane Queue

All work flows through a 4-lane command queue:

| Lane | Purpose | Default Concurrency |
|---|---|---|
| Main | User-initiated tasks | 1 |
| Cron | Scheduled/recurring work | 1 |
| Subagent | Delegated specialist work | 1 |
| System | Internal platform operations | 1 |

Max total concurrency: 4. Draining on shutdown. Full queue state visibility per lane.

## Gateway Architecture

The gateway is the persistent local runtime edge. It connects your computer to the cloud brain.

```
Cloud Control Plane ←─WSS─→ empyralis-gateway ←─loopback─→ empyralis-supervisor
(identity, policy,         (persistent local           (screenshot, OCR,
 pairing, runs)             process, journal,           clipboard, app launch,
                            outbox, checkpoints,        browser, shell, files)
                            personal channels)
```

**8-phase lifecycle:** Pair → Register → Connect → Heartbeat → Operate → Degrade → Recover → Revoke

**Protocol:** WSS (WebSocket Secure). JSON frames with three types: `request`, `response`, `event`. Every event carries monotonically increasing `seq` and `ack` for ordering and replay.

**Identity tuple:** `tenant_id + workspace_id + user_id + device_id + gateway_id`

**Durability primitives:**
- **Journal** — NDJSON immutable event log with cursor
- **Outbox** — Replayable message queue with retry and acknowledgment
- **Checkpoints** — Session state, health, seq/ack sync, recovery cursor

## Hybrid Deployment

Empyralis supports three deployment modes with explicit placement policy:

| Mode | Description |
|---|---|
| `local_only` | Work stays on the device. No cloud egress. |
| `sync_allowed` | Work runs locally, summaries sync to cloud. |
| `summary_bridge_only` | Only safe summary payloads cross the boundary. |

Placement priority: privacy class → required capabilities → data dependency → availability → user preference. The system fails closed if a placement or sync rule cannot be satisfied.
