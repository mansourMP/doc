# Empyralis — Master Execution Plan
# Research-backed. Code-verified. Investor-ready.

## Platform State: 75% Complete

34 gateway files. 34 backend gateway modules. 24 frontend panes. 10 supervisor capabilities. 7 security layers (19/22 done). 4-block memory system. 8-step agent studio wizard. Marketplace infrastructure. Lane queue. Heartbeat scheduler. 17 connectors.

## Investor Closeout Gates

These gates override feature breadth until the next investor demo. The platform should be sold as a serious AI worker system with strong isolation and governed runtime power, not as a finished "100% secure" system.

### Non-Negotiable Truth

- Do not claim "100% secure." Claim strong lane isolation, sensitivity classification, policy gating, audit trails, and named remaining hardening work.
- The demo must show one business outcome, not architecture. A user should see a customer/job handled end to end before hearing about channels, providers, memory classes, or gateway internals.
- Commercial proof outranks platform breadth until 3-5 businesses are paying.
- Progressive disclosure is mandatory: Sage first, business outcome second, configuration only when needed.

### 10-Day Investor Rescue

| Window | Gate | Exit condition |
|---|---|---|
| Day 1 | Security and reliability hard stops | Vault secrets are never exposed through process arguments; login, chat, and workspace bootstrap no longer fall into 500/warm-up loops under normal local/prod load |
| Days 2-3 | Chat-first product path | User lands in Sage, can type within 3 seconds, sees one useful response, and never sees `USER.md`, `IDENTITY.md`, provider internals, or setup machinery |
| Days 4-6 | One vertical proof agent | One shop/clinic/restaurant agent answers real customer questions from live catalog/FAQ data with governed channel setup |
| Days 7-8 | Marketplace proof | At least one seed package is installable, not `preview_only`, with demo data, tests, pricing, and analytics |
| Days 9-10 | Investor narrative | Demo script shows login -> Sage -> business agent -> live customer channel -> Activity proof -> billing path |

### Commercial Proof Gate

The next investment-readiness metric is not more architecture. It is 3-5 paying customers at $500+ MRR each, less than 5% monthly churn, and at least one marketplace package that is not preview-only.

### Engineering Maintainability Gate

`frontend/lib/workspace/workstation-chat-pane.tsx` must be split before adding more visible chat complexity. Extract the type definitions, composer state, stream/run state, provider status, memory/profile loaders, local-tool state, and activity/timeline projection into feature modules. This is a maintainability gate, not polish.

### Security Hard Stop: Vault Secrets

`server_modules/vault_store.py` must not call OpenSSL with `-pass pass:{passphrase}`. Replace the subprocess fallback with in-process `cryptography` primitives or a passphrase path that never appears in `ps`, shell history, crash logs, or process arguments. Exit gate: encrypt/decrypt can run while `ps` cannot reveal a vault passphrase.

### Certification Status: Security and Marketplace Proof (2026-05-07)

This section is proof status, not a marketing claim. Empyralis should be presented as strongly isolated and actively hardened, not "100% secure."

| Gate | Status | Evidence |
|---|---|---|
| Vault CLI secret exposure | PASS | `server_modules/vault_store.py` disables legacy OpenSSL CLI paths and uses in-process vault crypto; `server_modules/tests/test_vault_store.py` asserts no `subprocess`, `-pass`, or `pass:` path remains. |
| RED memory stripping | PASS | `server_modules/workspace_context_memory_adapter.py` strips RED-classified context files, runtime memory, and recent logs before external model context; `server_modules/tests/test_sage_memory_red_stripping.py` covers prompt-context redaction. |
| Cross-agent memory isolation | PASS | `server_modules/tests/test_cross_agent_memory_isolation.py` proves one agent cannot read another agent's private memory by default. |
| Marketplace installability | PASS | `studio-proof-shop-assistant` is the first governed installable seed package, exposes proof metadata, and installs into the `template_catalog` surface. |
| Remaining hardening | OPEN | Behavioral anomaly detection, semantic prompt-injection scoring, output sanitization, and per-session tool rate limiting remain launch-hardening work. |

## What Makes Empyralis Different (The Moat)

### 1. Durable Local Execution With Checkpoint/Resume
No other platform has this. The agent opens a browser, fills a form, hits a CAPTCHA, pauses, waits for you, and resumes from the exact next action. OpenClaw doesn't have this. ChatGPT doesn't have this. 

Implementation: `empyralis-gateway/src/browser/runtime.ts`, `server_modules/runtime_policy.py:263-349`

### 2. Lane-Separated Architecture
Personal channels (your WhatsApp, your Telegram) and business channels (customer bots, Studio agents) are separated at the protocol level. They don't share auth. They don't share memory. One can't leak into the other.

Implementation: `server_modules/channel_lane_contract_service.py`

### 3. Safe-By-Default Application Platform
Mini-apps start with zero access. No memory. No Sage access. No specialist access. Everything is explicitly granted through bridge contracts with audit trails.

Implementation: `frontend/lib/workspace/hosted-mini-apps-pane.tsx`, `frontend/lib/workspace/hosted-mini-app-surface.tsx`

### 4. Governed Marketplace From Day One
Verification tiers (unverified/partner/verified), review states (pending/approved/restricted), policy postures (governed/restricted), monetization models (free/metered/subscription/revenue_share).

Implementation: `frontend/lib/marketplace/marketplace-pane.tsx`

---

## Agent Studio, Marketplace, and the Business Model

### Core Product Loop

Empyralis is strongest when everything serves one commercial loop:

```
Build an agent in Studio → connect data/tools/channels → deploy to customers → agent handles work → usage and revenue are measured
```

The investor-relevant product is not "many people chatting with an AI." The investor-relevant product is a working AI labor platform where a shop, clinic, hospital, restaurant, real-estate office, support team, or developer can deploy a useful worker that saves labor, closes transactions, or handles customer operations.

### Agent Studio Doctrine

Agent Studio is an agent factory, not a vertical-bot catalog.

- Templates are starting points, not cages.
- A "Dental Receptionist" template should not hard-limit the agent to dental tools forever.
- Users should be able to start from a template, then add/remove tools, skills, channels, memory, policies, data sources, and runtime power.
- The platform sells flexibility: "build your own AI worker," with good defaults for speed.
- Default templates exist to reduce setup friction and prove business value, not to define hard product boundaries.

The correct abstraction is:

```
Agent = persona + knowledge + memory + tools + skills + data sources + channels + runtime + policy + billing + analytics
```

### What Agent Studio Actually Builds

| Studio Step | What It Produces | Business Example |
|---|---|---|
| Overview | Name, role, persona, instructions, language | "Shop Assistant — Mandarin/English, knows inventory, polite but direct" |
| Knowledge | Documents, URLs, spreadsheets, FAQs, product catalogs | Price list, product PDF, return policy |
| Tools | Connectors, skills, browser, API tools, MCP bridge | Google Sheets, SMTP, Shopify, custom REST endpoint |
| Channels | Where customers reach it | WhatsApp, Telegram, Discord bot, website embed, email |
| Memory | Customer history, preferences, repeat requests | "This customer usually orders size 42" |
| Safety | Approval policy, action classes, spending/send limits | Ask before refunding, sending invoice, or charging card |
| Test | Preview, red-team, golden examples | "Do you have Nike Air Max size 42?" |
| Deploy | Runtime target, channel credentials, billing | Cloud always-on agent for a shop |

### Agent Runtime Power Tiers

| Tier | Name | Where It Runs | Capabilities | Commercial Use |
|---|---|---|---|---|
| Free | Cloud Lite | Empyralis cloud | Chat, memory, retrieval, simple APIs, drafting | Testing and low-risk setup |
| 1 | Cloud Agent | Empyralis cloud | Connectors, channels, skills, MCP bridge, hosted credits | Shop/dentist/customer-support always-on agent |
| 2 | Local Agent | User laptop/gateway | Browser, shell, files, screenshots, personal sessions | Developer/personal automation |
| 3 | Dedicated Agent | User Mac mini or dedicated runtime | Always-on local power, owned device, persistent sessions | Serious business ops, private workflows |
| 4 | Virtual Computer | Empyralis-provisioned VM | Full OS, browser, shell, persistent state, metered runtime | Legacy software, heavy browser/desktop automation |

Routing principle:

- Product questions, booking FAQs, order status → Cloud Agent.
- Browser form filling, screenshots, local files, personal sessions → Local Agent or Dedicated Agent.
- 24/7 machine labor with persistent state → Dedicated Agent or Virtual Computer.
- High-risk actions like payments, refunds, mass messaging, credential changes, or medical/legal/customer-impacting decisions → approval boundary regardless of tier.

### Marketplace Revenue Engine

The Marketplace should distribute governed business assets, not become a noisy plugin bazaar.

| Marketplace Asset | Example | Buyer | Monetization |
|---|---|---|---|
| Agent template | Shop Assistant, Dental Receptionist, Restaurant Order Taker | Business owner | Monthly, one-time, or usage-based |
| Connector package | Kingdee/Yonyou ERP, Shopify, Airtable, Google Sheets | Business owner/developer | One-time, subscription, or enterprise |
| Skill pack | Payments, inventory ops, appointment scheduling, medical terminology | Agent builder | One-time or subscription |
| Provider config | DeepSeek support profile, Mandarin prompt pack | Agent builder | Free, metered, or premium |
| Mini-app | Appointment calendar, order dashboard, intake form | Business owner | Monthly |
| Tool bundle | Stripe, PayPal, WeChat Pay, Alipay, UnionPay | Business owner | Revenue share or transaction fee |

Revenue paths:

- Hosted credits for cloud reasoning and execution.
- Per-agent monthly plans for deployed workers.
- Usage-based pricing for messages, runs, runtime minutes, and virtual computers.
- Marketplace revenue share for third-party templates/connectors/skills.
- Enterprise deployment for regulated or high-volume customers.

### Business Proof Agents

Investor proof should come from real agents that do measurable work:

| Industry | Agent | Channels | Tools/Data | What It Proves |
|---|---|---|---|---|
| Retail shop | Shop Assistant | WhatsApp, Telegram, web embed | Sheets/Excel inventory, SMTP, payment links | Handles 50-100 daily customer questions and order flow |
| Dental clinic | Receptionist | WhatsApp, web widget, SMS/email | Calendar, FAQ, intake forms | Reduces front-desk load and books appointments |
| Restaurant | Order Taker | WhatsApp, Telegram, web | Menu DB, payments, delivery status | Turns conversations into orders |
| Customer support | Support Agent | Discord, Telegram, email, web | Knowledge base, ticketing, CRM | Deflects tickets and escalates cleanly |
| Real estate | Property Agent | WhatsApp, web, email | CRM, Drive, calendar | Qualifies leads and schedules viewings |
| Hospital/clinic | Triage Router | WhatsApp/SMS/web | Calendar, policy docs, escalation rules | Routes safely without pretending to diagnose |
| Law firm | Intake Clerk | Web, email | Forms, document templates, CRM | Collects structured intake and schedules humans |

These should be seed packages with real configuration, tests, demo data, analytics, and clear safety boundaries. Preview stubs are not enough.

### Chinese-Market Path

The most coherent China/MENA-market wedge is practical business automation, not generic AI chat:

- Mandarin-first customer-service templates.
- DeepSeek as the default cost-effective hosted model path.
- WeChat Mini Program / WeChat Work connector path.
- Kingdee/Yonyou/Excel/Sheets inventory and ERP connectors.
- WeChat Pay, Alipay, UnionPay payment tools behind approval gates.
- Shop, restaurant, clinic, hotel, tutoring, and real-estate templates.

### What Makes a Studio Agent Powerful

To compete with the latest agent platforms, a Studio agent must be able to combine:

- Skills: curated, dependency-gated capabilities such as 1Password, Apple Notes, reminders, tmux, MCP bridge, payment tools, and data operations.
- Channels: WhatsApp, Telegram, Discord bot, email, web embed, later Signal/iMessage where legally and technically stable.
- Live data: Sheets, Excel, Airtable, SQL read-only, REST APIs, Shopify/ERP/CRM.
- Runtime: cloud, local gateway, dedicated device, or virtual computer.
- Memory: scoped customer/business memory, not shared with personal Sage unless explicitly bridged.
- Governance: action classes, approval boundaries, audit, usage tracking, prompt-injection wrapping.
- Analytics: customers served, revenue influenced, messages handled, response time, satisfaction, escalation rate.

This is the difference between "chatbot builder" and "AI worker operating system."

---

## Competitive Research — What The Best Platforms Do

### Memory Architecture (Researched from Letta/MemGPT, Mem0, CrewAI, LangChain, Anthropic)

The industry converges on a 4-tier memory model:

| Tier | Letta/MemGPT | Empyralis | Purpose |
|---|---|---|---|
| Persona | Persona block | SOUL.md | Who you are — never changes, highest priority |
| Human | Human block | IDENTITY.md | How you operate — updated occasionally |
| Working | Working memory | HEARTBEAT.md | What's happening now — auto-updated by agent |
| Archival | Archival memory | CONTEXT.md | Everything else — searchable on demand |

Key finding from Letta: "Memory blocks have a context hierarchy. Persona is always loaded first. Human second. Working memory third. Archival memory is searched on demand." Empyralis already implements this correctly in `workspace_context_memory_adapter.py`.

Sensitivity classification (RED/ORANGE/YELLOW/GREEN) is unique to Empyralis. No other platform has architectural memory sensitivity that strips facts from prompts before they leave the machine.

### Prompt Injection Defense (Researched from OpenClaw source code)

OpenClaw's 7-layer defense model at `src/security/`:

| Layer | OpenClaw | Empyralis | Gap |
|---|---|---|---|
| 1. Identity Gates | DM pairing, allowlists, channel policies | ✅ Same | None |
| 2. Content Sanitization | Marker wrapping, homoglyph defense, pattern detection | ✅ Ported in `external_content_guard.py` | None |
| 3. Instruction Hierarchy | System prompt priority, tool output deprioritization | ✅ Same | None |
| 4. Tool Policy | Deny-wins, sub-agent depth, owner-only | ✅ Same | None |
| 5. Runtime Sandbox | Docker, file mounts, shell safety, browser binding | ✅ Same (without Docker) | Docker sandbox not implemented |
| 6. Loop Detection | Tool call signature tracking | ✅ Same | None |
| 7. Audit | 40+ checks, skill scanner, credential change detection | ✅ 19/22 done | Command gating, RED stripping |

Key finding from OpenClaw's docs: "There is no 'perfectly secure' setup. The goal is to be deliberate about: who can talk to your bot, where the bot is allowed to act, and what the bot can touch."

### Channel Architecture (Researched from OpenClaw source code at `src/discord/`, `src/signal/`, `src/imessage/`)

OpenClaw uses bot tokens for Discord (NOT self-bots). `src/discord/client.ts:24` — `DISCORD_BOT_TOKEN`. OpenClaw uses signal-cli JSON-RPC daemon for Signal. OpenClaw uses BlueBubbles REST bridge for iMessage. Empyralis now follows the exact same architecture.

### Skills System (Researched from OpenClaw `skills/` directory, `src/agents/skills/`)

OpenClaw ships 53 skills. Each skill is a directory with a SKILL.md file (YAML frontmatter + Markdown body). Skills are gated by OS, required binaries, required env vars. Hidden when unavailable. ClawHub distributes them via npm.

Empyralis has 7 active skills. The gating system works. Missing: volume (need 15-20 more) and distribution (need marketplace).

### Tool Policy (Researched from OpenClaw `src/agents/tool-catalog.ts`, `src/agents/tool-policy.ts`)

OpenClaw resolves tools through 9 priority layers: Profile → Global allow/deny → Provider allow/deny → Agent allow/deny → Channel/Group allow/deny → Sandbox → Sub-agent depth → Loop Detection → Abort Signal.

Key rule: `deny` always wins. If `allow` is set and non-empty → "only allow what's listed." Plugin tools require explicit allowlist entries.

Empyralis implements the same layered deny-wins model in `runtime_policy.py`. Sub-agent depth reduction works. Owner-only tools work. Missing: channel/group-level tool policies.

### UI/UX Patterns (Researched from 21st.dev, Linear, Apple HIG)

21st.dev uses: shadcn/ui + Radix UI + Tailwind CSS + Framer Motion + Inter font. Key patterns: `scrollbar-gutter:stable`, `active:scale-[0.97]` pressed states, `group-hover` parent-child transitions, `focus-visible:ring-2` consistent focus rings.

Linear uses: CSS Modules, persistent sidebar, shallow navigation layers, display options surfaced in-place (not buried in settings).

Apple HIG principles: progressive disclosure, feedback for every action, forgiveness (undo), clear hierarchy.

Empyralis has: Radix primitives, Framer Motion, DM Sans + Fraunces fonts, CSS variables. Missing: shadcn/ui integration, skeleton loading states, scrollbar-gutter, consistent animation tokens.

---

## Remaining Implementation — 22 Items + Investor Gates

### Security (3 launch-blocking items)

**0. Vault Passphrase Removal**
- Remove any encryption/decryption path that sends passphrases through command-line arguments
- Delete the OpenSSL subprocess fallback or replace it with in-process `cryptography`
- Exit gate: `ps` cannot reveal vault secrets during vault operations
- Files: `server_modules/vault_store.py`

**1. Command Gating**
OpenClaw reference: `src/channels/command-gating.ts`
- Per-user authorization for control commands (`/model`, `/config`, `/system`)
- Access groups: owner, admin, member, viewer
- Default: owner-only for destructive commands
- Files: `server_modules/channel_blocking_policy_service.py`, `server_modules/routes_personal_channels.py`

**2. RED Facts Stripping**
- Before any prompt is sent to an external AI provider, strip all RED-classified memory facts
- RED = financial details, API keys, source code, other users' data
- This is architectural stripping — not a system prompt saying "don't share"
- Files: `server_modules/sage_memory_service.py`, `server_modules/workspace_context_memory_adapter.py`

### Platform Power (6 items)

**3. More Skills (15-20 additional)**
Port from OpenClaw's 53. Priority order:
1. `1password` — secret access from CLI
2. `apple-notes` — create/read/search Apple Notes
3. `apple-reminders` — manage reminders
4. `spotify-player` — control Spotify
5. `peekaboo` — macOS UI automation
6. `tmux` — terminal session control
7. `bear-notes` — read/write Bear notes
8. `blucli` — BluOS speaker control
9. `camsnap` — camera capture
10. `imsg` — iMessage via Messages.app
11. `eightctl` — Eight Sleep pod control
12. `openhue` — Philips Hue control
13. `sonoscli` — Sonos control
14. `things-mac` — Things 3 task management
15. `mcporter` — MCP client for external tools

**4. Skill Marketplace**
OpenClaw reference: ClawHub (`clawhub` npm package)
- GitHub repo as registry (start simple)
- `empyralis skill install <name>` CLI
- `empyralis skill search <query>` — vector search
- `empyralis skill publish` — upload with semver
- Skill scanner runs before install (already built at `skill_scanner.py`)

**5. Real-Time Data Connectors**
- Google Sheets (read/write live data)
- Airtable (read/write)
- PostgreSQL/MySQL (query only — read-only by default)
- Excel files (read/write via openpyxl)
- REST API connector (custom endpoints)

**6. Payment Processing Skills**
- Stripe (create payment links, check status)
- PayPal (create invoices)
- WeChat Pay (QR generation)
- Alipay (payment links)
- All gated behind approval — never auto-charge

**7. Auto-Learning Proposals**
- Agent notices patterns: "I noticed this customer always orders on Fridays"
- Proposes memory addition conversationally: "Should I remember that?"
- User accepts, edits, or declines
- Never writes to memory without asking

**8. Agent-To-Agent Handoff**
- Support agent → Sales agent escalation
- Any agent → Human fallback
- Preserves full conversation context
- Audit trail across handoff boundary

### Frontend (5 items)

**9. shadcn/ui Integration**
- Install: `npx shadcn@latest init` in frontend/
- Replace AppButton → Button, AppInput → Input, AppModal → Dialog, AppDrawer → Sheet
- One-for-one replacement. No logic changes.

**10. Skeleton Loading States**
- Every data-fetching page gets a Skeleton component
- Title placeholder, content placeholder, action placeholder
- No page should flash white while loading

**11. UI Polish**
- `scrollbar-gutter: stable` on html
- `--transition-fast/base/slow` animation tokens
- `active:scale-[0.97]` on all buttons
- `focus-visible:ring-2` consistent focus rings

**12. Mobile Tab Unification**
- Un-hide inbox/index as "Activity" tab
- Remove dead duplicates: today/index.tsx, spaces/index.tsx
- Sync tab names with web: Chat, Build, Discover, Activity, Settings

**13. Agent Analytics Dashboard**
- Customers served (count, trend)
- Revenue generated (if payment connected)
- Messages processed
- Satisfaction rate (thumbs up/down)
- Response time average
- New file: `frontend/lib/workspace/workstation-agent-analytics-pane.tsx`

### Business Infrastructure (4 items)

**14. Marketplace Seed Packages**
5-10 real, verified agent templates:
1. "Shop Assistant" — inventory queries, order status, pricing
2. "Dental Receptionist" — appointments, FAQs, insurance basics
3. "Restaurant Order Taker" — menu, reservations, delivery status
4. "Support Agent" — ticketing, knowledge base, escalation
5. "Real Estate Agent" — property queries, viewing bookings
6. "Hotel Concierge" — room info, amenities, local recommendations
7. "E-commerce Assistant" — order tracking, returns, product questions
8. "Medical Triage" — symptom checker, appointment routing
9. "Legal Intake" — client info collection, document requests
10. "Education Tutor" — subject Q&A, quiz creation, progress tracking

Each seed package must be a customizable starting point, not a hard-limited vertical bot. Every seed should include: default persona, data schema, channel setup, tool/skill bundle, runtime recommendation, approval policy, demo data, golden test conversations, analytics events, and clear monetization model.

**15. Virtual Computer Provisioning**
- One-click cloud VM for agents that need a full OS
- Windows/Linux options
- Persistent storage, browser, shell
- Metered credits
- Backend contract already exists in `runtime_config.py`

**16. White-Label Agent Embed**
- iframe/JS widget with business branding
- Custom colors, logo, welcome message
- Embed on any website
- Bridge contracts for data exchange

**17. Multi-Language Prompt Quality**
- Chinese (Mandarin) — primary target market
- Arabic
- Spanish
- System prompts translated and optimized
- Cultural context awareness per language

### Tests (2 items)

**18. Cross-Agent Memory Isolation Test**
- Create Agent A, write memory "secret_project_x"
- Create Agent B, attempt to read Agent A's memory
- Assert: Agent B gets empty result or permission denied

**19. Gateway E2E Test**
- Start gateway process programmatically
- Pair with mock cloud endpoint
- Send message through a channel runtime
- Verify outbox entry created
- Verify journal entry exists

### Cleanup (3 items — blocked)

**20-22.** Dead route cleanup, bridge/ directory, operator_chat.py. Blocked by compatibility audit. Agent correctly refused blind deletion.

---

## Build Order

```
Phase A (Now — Trust):        Vault passphrase removal → runtime stability cert → command gating → RED facts stripping
Phase B (Now — Demo):         Chat-first Sage path → one vertical proof agent → one non-preview marketplace package
Phase C (Now — Studio ROI):   3 proof agents → live data connectors → analytics → hosted-credit billing
Phase D (Now — Platform):     Skills port → Skill marketplace → Payment skills → Auto-learning → Handoff
Phase E (Now — Frontend):     chat-pane split → shadcn/ui → Skeletons → UI polish → Mobile tabs → Analytics dashboard
Phase F (After — Business):   Marketplace seeds → Virtual computer → White-label embed → Multi-language
Phase G (After — Tests):      Memory isolation → Gateway E2E
Phase H (Gate):               Compatibility audit → Cleanup
```

Phases A, B, C, D, and E can run in parallel across different agents only when file ownership is explicit. Phase A remains the launch trust gate, and Phase B remains the investor demo gate.

---

## Key Research References

### Read From Source Code
- OpenClaw `src/security/external-content.ts` — content wrapping with marker IDs
- OpenClaw `src/security/skill-scanner.ts` — static analysis before install
- OpenClaw `src/channels/command-gating.ts` — per-user command authorization
- OpenClaw `src/discord/client.ts:24` — DISCORD_BOT_TOKEN (not self-bot)
- OpenClaw `src/signal/daemon.ts` — signal-cli JSON-RPC daemon
- OpenClaw `src/channels/typing.ts` — typing keepalive pattern
- OpenClaw `src/channels/draft-stream-controls.ts` — edit-in-place streaming
- OpenClaw `src/agents/tool-catalog.ts` — 25 core tools, 11 sections
- OpenClaw `src/agents/tool-policy.ts` — 9-layer deny-wins pipeline
- OpenClaw `skills/` — 53 skills, SKILL.md format with YAML frontmatter

### Read From Docs
- Letta docs — memory blocks, context hierarchy, archival search
- MCP specification — tools/resources/prompts contract
- OWASP Top 10 for LLM — prompt injection #1, excessive agency #8
- Apple HIG — progressive disclosure, feedback, forgiveness
- Linear docs — sidebar-first IA, display options surfaced in-place
- Discord developer docs — bot token required, self-bots forbidden
- BlueBubbles docs — Mac-local bridge, Private API conditional
- signal-cli docs — unofficial linked device bridge

### Market Research
- Respond.io: $79-279/mo — omnichannel ops, not AI
- Manychat: $29-39/mo — marketing automation, not AI workers
- WATI: WhatsApp-first SMB — weak on Telegram, weak on cross-channel
- OpenClaw: 20+ channels, 53 skills — developer-focused, not business-ready
- Empyralis position: AI workers for business, governed marketplace, local execution moat
