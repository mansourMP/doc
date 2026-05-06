# Empyralis — AI Operating System

Empyralis is an AI operating system for authorized digital work. It runs on your computer, connects to your apps, and turns intent into execution — with oversight, approvals, and real-world actions.

**Not a chatbot. Not a workflow builder.** An operational system where you state an outcome, the platform plans and routes the work, uses tools and connectors, asks for approval when needed, and ships the result.

## The One-Sentence Pitch

> Your AI operations center. Say what you want done — Empyralis handles the rest.

## Core Thesis

People don't want more AI features. They want outcomes completed. Empyralis helps a person or small team turn intent into execution with oversight, approvals, and real-world actions.

## Architecture At A Glance

Empyralis is built as a **4-layer OS**:

| Layer | Role |
|---|---|
| **Sage** | Your personal AI. The main agent. One identity per user. |
| **Studio Agents** | Deployable specialist workers. Scoped, auditable, policy-bound. |
| **Applications** | Hosted mini-apps with explicit bridge contracts. Safe by default. |
| **Control Plane** | Identity, policy, billing, memory routing, approvals, security. |

Every layer is separated. Sage doesn't control Studio agents. Applications can't read Sage's memory. Cross-layer access requires explicit contracts.

## What Makes It Different

### 1. Durable Local Execution
The agent can open a browser, interact with web pages, pause for a CAPTCHA or login, wait for you, and resume from the exact next action. No other platform has checkpoint/resume at this level.

### 2. Lane-Separated Architecture
Personal channels (your WhatsApp, your Telegram) and business channels (customer bots, Studio agents) are separated at the protocol level. They don't share auth. They don't share memory. One can't leak into the other.

### 3. Safe-By-Default Application Platform
Mini-apps start with zero access. No memory. No Sage access. No specialist access. Everything is explicitly granted through bridge contracts with audit trails.

### 4. Web, Mobile, Desktop — One Platform
Same backend. Same identity. Same memory. Same approvals. Mobile is the daily-use surface. Desktop is the power-user surface. They share one brain.

## Shell Model

| Shell Class | Surfaces |
|---|---|
| **Full Shell** | Web, Mobile (iOS/Android), Desktop (macOS/Windows/Linux) |
| **Channel Shell** | Telegram, WhatsApp, Slack, Discord, Email, Phone, Web Chat |

Channel shells are lightweight conversation surfaces. They share the same captain identity and run engine — but they don't become deep admin surfaces or separate product brains.

## Trust & Security

- **Fail-closed by default.** If policy, placement, sync, entitlement, or approval state is unclear, the system denies the action.
- **Immutable approval binding.** Approved browser and shell plans are hash-bound. If the executed plan differs from the approved plan, execution is rejected.
- **Credential vault with PBKDF2+SHA256+Fernet encryption.** Secrets never become casual client payloads.
- **Prompt injection defense.** External content from channels and web is wrapped in random marker boundaries before reaching the model. Unknown senders get pairing codes, not responses.
- **Tool policy is architectural, not advisory.** If a tool is denied at any policy layer, it's removed — period.

## Quick Facts

| Metric | Detail |
|---|---|
| Backend | Python/FastAPI with PostgreSQL + SQLite checkpoints |
| Frontend | Next.js with Radix UI + Framer Motion |
| Mobile | Expo Router (React Native) |
| Desktop | Tauri shell |
| Gateway | TypeScript Node.js daemon with WSS protocol |
| Supervisor | Rust daemon for device capabilities |
| Connectors | 17 validated connectors (Google, Slack, Telegram, Discord, GitHub, Notion, etc.) |
| Channels | 2 personal channels (Telegram, WhatsApp) + Studio webhook lane |
| State | 45 verified backend systems |
