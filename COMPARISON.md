# Empyralis vs The Market

## Positioning

Empyralis is not a chatbot. Not a workflow builder. Not a developer tool. It's an AI operating system for authorized digital work — a platform where users state outcomes and the system executes with oversight, approvals, and real-world actions.

## Empyralis vs OpenClaw

OpenClaw is the closest comparable platform. Both run locally. Both connect to messaging channels. Both have browser automation. But the architectural philosophy is different.

| Dimension | Empyralis | OpenClaw |
|---|---|---|
| **Target user** | Non-technical users, small business owners | Developers, power users |
| **Setup** | One-click wizard, QR pairing | CLI commands, config files |
| **Main agent** | Sage — one identity, one relationship | Agent workspace with configurable identity |
| **Memory** | 4 structured blocks (SOUL/IDENTITY/HEARTBEAT/CONTEXT) + vector search | Flat Markdown files + vector index |
| **Agent types** | Sage (personal) + Studio (business) + Marketplace (third-party) | Configurable agents with profiles |
| **Channel lanes** | Personal + Studio separated at protocol level | All channels share runtime |
| **Browser** | Electron with checkpoint/resume at action level | Playwright/CDP with session persistence |
| **Durability** | Journal + outbox + checkpoints. Cold boot recovery. | JSONL session persistence. In-memory during operation. |
| **Security audit** | 40+ automated checks, skill scanner, credential change detection | Security audit command, skill scanner |
| **Approval binding** | Immutable plan hash for browser and shell | Immutable argv/cwd/env binding for exec |
| **Gateway** | Dedicated persistent process with 8-phase lifecycle | Agent loop, no separate gateway daemon |
| **Supervisor** | Separate Rust daemon for device capabilities | In-process tools |
| **Supervisor** | Separate Rust daemon (HMAC-signed requests) | In-process tools with sandboxing |
| **Channels** | 2 personal + 7 Studio webhooks | 9+ channel shells |
| **Channel polish** | Pairing codes, outbox delivery. Typing/draft/status planned. | Typing indicators, draft streaming, status reactions, read receipts |
| **MCP** | Planned via mcporter bridge | Via mcporter skill (not native) |
| **Skills** | 9 curated skills with OS/bin/env gating | 53 bundled skills with gating + ClawHub marketplace |
| **Marketplace** | Governed distribution with verification tiers, review states, monetization | ClawHub for skills (separate CLI) |
| **Mini-apps** | Hosted iframe apps with bridge contracts, denied-by-default memory | Not applicable |
| **Mobile** | Expo Router (React Native) — separate app | Command/control node via WebSocket |
| **Desktop** | Tauri shell with native window controls | Menubar app (macOS) |
| **Deployment** | Web + mobile + desktop + cloud + hybrid | CLI + menubar + mobile control node |

### Where Empyralis Wins
1. **Durable local execution** — checkpoint/resume at the action level. Browser tasks pause and resume from the exact next step.
2. **Lane-separated architecture** — personal channels and business channels are architecturally isolated. OpenClaw mixes them.
3. **Non-technical user experience** — designed for people who have never used a terminal. QR pairing. One-click setup. Conversational bootstrap.
4. **Governed marketplace** — verification tiers, review states, policy postures, monetization models. Built for third-party distribution from day one.
5. **Mini-application platform** — safe-by-default hosted apps with explicit bridge contracts. No other AI agent platform has this.
6. **Device trust lifecycle** — 8-phase model with explicit revocation. Revoked sessions stop immediately on reconnect.

### Where OpenClaw Wins
1. **Channel breadth** — 9+ channels with native features (typing indicators, draft streaming, status reactions).
2. **Skill ecosystem** — 53 bundled skills. ClawHub distribution. Mature plugin system.
3. **Battle-tested** — longer in production. More users. More edge cases resolved.
4. **E2E test harness** — programmatic gateway spawning. Automated multi-instance testing.

## Empyralis vs ChatGPT / Claude

| Dimension | Empyralis | ChatGPT / Claude |
|---|---|---|
| **Execution** | Runs on your computer. Browser, shell, files, apps. | Cloud-only. Cannot touch your machine. |
| **Memory** | 4 structured blocks. You own and edit the files. | Limited memory. Opaque. Cannot edit directly. |
| **Channels** | WhatsApp, Telegram, Slack, Discord, email | None |
| **Approvals** | Action-level gating. Immutable plan binding. | None |
| **Agents** | Build and deploy your own specialists | GPTs (limited) |
| **Marketplace** | Governed third-party distribution | GPT Store |
| **Privacy** | Local execution. RED facts never leave your machine. | All data goes to cloud. |

## Empyralis vs AI Agent Frameworks (CrewAI, LangChain, AutoGPT)

| Dimension | Empyralis | Agent Frameworks |
|---|---|---|
| **Target user** | Non-technical | Developers |
| **Interface** | Web, mobile, desktop, chat | Python/TypeScript code |
| **Setup** | One-click wizard | pip install + write code |
| **Channels** | Built-in | Must build yourself |
| **Security** | Architectural (tool policy, vault, lane separation) | Your responsibility |
| **Deployment** | Click "Deploy" | Docker, Kubernetes, cloud infra |
| **Multi-agent** | Studio + delegation | Code-level orchestration |

## Empyralis vs Chatbot Platforms (Manychat, Respond.io, WATI)

| Dimension | Empyralis | Chatbot Platforms |
|---|---|---|
| **AI depth** | Full agent with memory, reasoning, tools | Flow-based automation + basic AI |
| **Customization** | Build your own agents with full tool access | Template-based flows |
| **Local execution** | Yes — browser, shell, files | No |
| **Pricing** | Hosted credits + BYOK | Per-contact, per-message |
| **Target** | Business operators who need real AI workers | Marketing teams who need automation |

## The Empyralis Moat

1. **Durable local execution with checkpoint/resume.** Nobody else has this. Not OpenClaw. Not ChatGPT. It's a genuine technical advantage that would take competitors months to replicate.

2. **Lane-separated architecture.** Personal and business channels are isolated at the protocol level. This is enterprise-grade architecture before having enterprise customers.

3. **Safe-by-default application platform.** Mini-apps with explicit bridge contracts, denied-by-default memory, and full audit trails. This is how platforms become ecosystems.

4. **Governed marketplace from day one.** Verification tiers, review states, policy postures, monetization. Built for third-party distribution, not bolted on later.

5. **Cost structure advantage.** Local execution means zero cloud compute cost for browser, shell, and file operations. BYOK means users own their AI spending. The business model scales without linearly scaling cloud costs.
