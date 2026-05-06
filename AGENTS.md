# Agents — Sage, Studio & Marketplace

Empyralis has three kinds of agents. They serve different purposes, have different scopes, and are strictly separated.

## Agent Types

| Type | Who Builds It | Where It Runs | Tools & Access | Example |
|---|---|---|---|---|
| **Sage** | Empyralis (built-in) | Your laptop or cloud | Full — browser, shell, files, connectors, channels | "Handle my email and summarize my day" |
| **Studio Agent** | You (via Agent Studio) | Your laptop, server, or cloud | Scoped — configured per agent | "Customer support bot for my shop" |
| **Marketplace Agent** | Third-party developers | User's infrastructure | Governed — verified, rated, policy-bound | "Restaurant reservation agent" |

## Sage — The Main Agent

Sage is your personal AI. One identity. One relationship. It's the only agent you talk to directly.

**What makes Sage different from generic AI assistants:**
- It knows you through SOUL/IDENTITY/HEARTBEAT/CONTEXT memory blocks
- It has access to your local computer (browser, shell, files, screenshots)
- It connects to your personal channels (WhatsApp, Telegram)
- It delegates work to Studio agents invisibly — you see one agent, not a team
- It never shares your memory with sub-agents or applications

**Sage's sub-agent rule:** When Sage delegates a task to a specialist, the specialist gets minimal context — only what's needed for that specific task. Not your full memory. Not your identity. Not your other projects.

## Studio Agents — Specialists You Build

Studio agents are deployable workers for specific business tasks. Built through an 8-step wizard in the Agent Studio.

### Agent Roles

| Role | What It Does | Keyword Match |
|---|---|---|
| **Support** | Customer service, inbox management, replies | "customer", "feedback", "reply", "inbox", "complaint" |
| **Sales** | Lead handling, booking, follow-ups, pipeline | "sales", "lead", "booking", "appointment", "pipeline" |
| **Research** | Analysis, summaries, briefs, study plans | "research", "study", "brief", "summary", "analyze" |
| **Finance** | Spreadsheets, budgets, invoices, reports | "finance", "sheet", "budget", "invoice", "expense" |
| **Builder** | Code, automation, workflows, platform work | "build", "code", "debug", "automation", "script" |
| **Private Assistant** | Personal reminders, habits, notes, travel | "personal", "reminder", "habit", "routine", "travel" |

### Agent Creation Wizard (8 Steps)

1. **Overview** — Name, avatar, persona, system prompt
2. **Knowledge** — What the agent knows (documents, URLs, manual entry)
3. **Tools** — Which tools the agent can use (connectors, local, browser, shell)
4. **Channels** — Where the agent operates (Telegram, WhatsApp, web)
5. **Memory** — Memory enabled, context budget, compaction policy
6. **Safety** — Approval mode, trust mode, execution mode, allowed action classes
7. **Test** — Preview conversations before going live
8. **Deploy** — Go live with channel pairing and runtime configuration

### Operating Modes

| Mode | Purpose | Config Edits |
|---|---|---|
| `owner_edit` | You're building and testing the agent | Allowed |
| `owner_test` | You're previewing how it behaves | Not allowed |
| `customer_live` | Real customers are interacting with it | Not allowed |

### Agent Capabilities

Each Studio agent has:
- Install-scoped short-term and long-term memory
- Its own tool scope (connectors, local tools, browser)
- Its own connector scope (Gmail, Slack, Notion, etc.)
- Its own artifact history
- Its own runtime policy (approval mode, allowed actions)
- Its own activity stream
- Its own model selection

### Isolation Contract

Studio agents do not share memory with Sage. They do not inherit each other's memory. The only shared layer is auth, billing, and channel infrastructure. This is enforced at the protocol level — Studio deployment IDs are blocked from personal lane session contexts.

### Deploy & Channels

After creation, agents are deployed to channels:
- **Telegram Bot** — Customers message the bot on Telegram
- **WhatsApp Business** — Customers message on WhatsApp
- **Web Chat** — Embedded chat widget
- **API** — Programmatic access via REST endpoints

## Marketplace Agents — Third-Party Distribution

Marketplace is Empyralis's governed distribution platform. Third-party developers build and publish agents, tools, providers, connectors, and mini-apps.

### Trust & Verification

| Tier | Meaning |
|---|---|
| **Unverified** | Submitted but not yet reviewed |
| **Partner** | Reviewed and approved by Empyralis |
| **Verified** | Independently audited, highest trust |

### Review States

| State | Meaning |
|---|---|
| **Pending** | Awaiting review |
| **Approved** | Passed review, available for install |
| **Restricted** | Limited availability (geographic, plan-tier, or compliance) |

### Policy Postures

| Posture | Meaning |
|---|---|
| **Governed** | Standard platform rules apply. Tool policy, approval, audit. |
| **Restricted** | Additional constraints. Limited tools, mandatory human approval, data residency. |

### Monetization

| Model | Description |
|---|---|
| **Free** | No cost to install or use |
| **Metered** | Pay per use (per task, per token, per API call) |
| **Subscription** | Monthly or annual recurring |
| **Revenue Share** | Developer sets `revenueShareBps` (basis points). Platform takes commission. |

### What Marketplace Distributes

- **Agents** — Complete deployable specialists with prompts, tools, memory config
- **Providers** — AI model provider configurations (OpenAI, Anthropic, Gemini, local)
- **Connectors** — Integration packages (Gmail, Slack, Notion, custom APIs)
- **Mini-Apps** — Hosted applications with bridge contracts
- **Templates** — Agent templates and workflow templates
- **Skills** — Installable capability packages with OS/bin/env gating

### Security For Marketplace Agents

- Pre-install static analysis scan for malicious patterns
- Denied-by-default memory access (no Sage memory, no specialist memory)
- Bridge contracts are explicit allowlists, not implicit grants
- Every bridge call is audited with `auditId`, `runId`, `threadId`
- Developers set permissions; users approve them at install time
- Platform can revoke or restrict packages remotely
