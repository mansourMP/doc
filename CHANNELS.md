# Channel System

Empyralis operates across multiple surfaces — web, mobile, desktop, and messaging channels. The channel system is built on a strict lane separation: personal channels for you, business channels for your deployed agents.

## Channel Architecture

```
                    Cloud Control Plane
                           │
            ┌──────────────┼──────────────┐
            │              │              │
     ┌──────▼──────┐ ┌─────▼─────┐ ┌──────▼──────┐
     │  Personal   │ │  Studio   │ │   Channel   │
     │  Gateway    │ │  Webhook  │ │   Shells    │
     │  Lane       │ │  Lane     │ │  (Web/Mobile│
     │             │ │           │ │   /Desktop) │
     └──────┬──────┘ └─────┬─────┘ └─────────────┘
            │              │
     ┌──────▼──────┐ ┌─────▼─────┐
     │  WhatsApp   │ │  Slack    │
     │  (personal) │ │  Discord  │
     │  Telegram   │ │  GitHub   │
     │  (personal) │ │  Twilio   │
     │             │ │  (WhatsApp│
     │             │ │  Business)│
     └─────────────┘ └───────────┘
```

## Lane Separation

| Personal Lane | Studio Lane |
|---|---|
| Owned by the user | Owned by the business |
| Runs on user's device (gateway) | Runs in cloud (webhook connectors) |
| Personal WhatsApp, personal Telegram | Business WhatsApp API, Telegram Bot API |
| User's memory and identity | Agent's memory and identity |
| Gateway-authenticated | Webhook-authenticated (HMAC, signing secret) |

**Why this matters:** A customer messaging your business bot on WhatsApp and a friend messaging your personal WhatsApp are completely different security contexts. They must never share auth, session state, or memory. The lane separation enforces this at the protocol level.

## Channel Pairing

Every channel uses a human-friendly pairing flow:

1. Unknown sender messages the agent
2. Agent issues an 8-character pairing code (alphabet: `ABCDEFGHJKLMNPQRSTUVWXYZ23456789` — no confusing characters)
3. Agent replies: "Your code is EMP-XXXX-XXXX. Ask the owner to approve this."
4. Owner approves via web UI or mobile app
5. Sender is added to allowlist
6. Sender is notified: "You're approved. Go ahead."

**Pairing constraints:**
- Codes expire after 1 hour (configurable, max 1 hour)
- Max 3 pending pairing requests per channel
- Pairing requests are persisted and survive restarts

## Per-Channel Policies

Every channel supports independent security policies:

| Policy | Options | Default |
|---|---|---|
| **DM Policy** | `pairing`, `allowlist`, `open`, `disabled` | `pairing` |
| **Group Policy** | `allowlist`, `open`, `disabled` | `allowlist` |
| **Mention Required** | `true`, `false` | `true` (WhatsApp, Telegram groups) |
| **Command Gating** | Per-user, per-role | Owner-only for `/model`, `/config` |

## Channel Runtimes (Personal Lane)

### WhatsApp Personal
- **Protocol:** Baileys (WhatsApp Web)
- **Pairing:** QR code scan or 6-digit code
- **Session:** Persistent across restarts
- **Authentication:** Phone number + linked device
- **Recommendation:** Separate phone number + eSIM for production

### Telegram Personal
- **Protocol:** MTProto (Telegram client)
- **Authentication:** Phone number + login code + 2FA password
- **Session:** String persistence on disk
- **Features:** Bot commands, inline queries, media handling

### Planned Personal Channels
- **Discord** — Bot API with gateway intents
- **Signal** — signal-cli linked device
- **iMessage** — BlueBubbles bridge (macOS)

## Channel Webhooks (Studio Lane)

Studio/business channels use cloud webhook connectors:

| Channel | Verification | Protocol |
|---|---|---|
| **Slack** | Signing secret | Socket Mode |
| **Discord** | Signature + timestamp | Bot API |
| **GitHub** | HMAC-SHA256 (`X-Hub-Signature-256`) | Webhook |
| **WhatsApp Business** | Twilio webhook secret | Twilio API |
| **Telegram Bot** | Bot token | Bot API |
| **Google Chat** | Google Workspace | Chat API |
| **Microsoft Teams** | Bot Framework | Teams SDK |

All webhooks are verified before payload parsing, dispatch, or run creation. Unverified webhooks are rejected at the ingress boundary.

## Channel-Native Features (In Development)

Features that make channels feel like native messaging apps:

| Feature | Status | Description |
|---|---|---|
| **Typing indicators** | Planned | 3s keepalive, 60s max TTL. Agent shows as "typing..." while processing. |
| **Draft streaming** | Planned | Edit message in-place as the agent composes its reply, rather than waiting for the final message. |
| **Status reactions** | Planned | Emoji progress: 👀 (queued) → 🤔 (thinking) → 🔥 (using tools) → 👍 (done) |
| **Read receipts** | Planned | Auto-remove ack reaction after the agent replies. |
| **Inline commands** | Planned | Native bot command menus (Telegram `setMyCommands`, Discord slash commands). |

## Channel Lifecycle

Every channel follows the same lifecycle:

```
configure → connect → online ⇄ (disconnect → reconnect)* → stop
```

- **Configure:** User provides credentials (QR scan, bot token, API key)
- **Connect:** Gateway opens the channel connection
- **Online:** Channel is receiving and sending messages
- **Disconnect:** Connection drops (network, rate limit, auth expiry)
- **Reconnect:** Gateway reconnects with exponential backoff
- **Stop:** User explicitly disables or removes the channel

The gateway handles all reconnect logic. The cloud handles all pairing and identity. The user never sees connection state management — they see "online" or "offline."

## Message Auditing

Every channel message is tracked:

- **Channel event journal** — Inbound/outbound direction, actor, status, payload metadata
- **Gateway outbox** — Every outbound message queued with idempotency key, retry count, and delivery status
- **Activity ledger** — Channel sends appear in the activity timeline with action class `channel_send` and risk level `critical`
- **Credential change detection** — `.configure` actions on personal channels are classified as `credential_change` with risk level `high`

## Comparison To OpenClaw

| Feature | Empyralis | OpenClaw |
|---|---|---|
| Channel count | 2 personal + 7 Studio webhooks | 9+ channel shells |
| Lane separation | Architecture-enforced | Mixed runtime |
| Pairing | 8-char codes + QR | 8-char codes |
| Personal WhatsApp | Baileys (own number) | Baileys or WhatsApp Web |
| Personal Telegram | MTProto client | Bot API + grammY |
| Discord | Studio webhook (planned personal) | Full bot + gateway intents |
| Signal | Planned | signal-cli linked device |
| iMessage | Planned | BlueBubbles bridge |
| Typing indicators | Planned | Implemented |
| Draft streaming | Planned | Implemented |
| Status reactions | Planned | Implemented |
| Message outbox | Full journal + outbox + checkpoints | JSONL session persistence |

Empyralis's channel architecture is more secure (lane separation) and more durable (outbox/checkpoints). OpenClaw has more channels and more polished native features. The convergence path is clear: add the channels, add the native features, keep the security advantage.
