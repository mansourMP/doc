# Memory & Identity System

Empyralis's memory system is designed to make the agent know you deeply — more than any other application. It's built on structured truth with markdown projections, not a database form.

## Core Principle

**Structured backend truth is canonical.** Markdown files are projections — exports and views of the structured data. The user can edit them freely, but the backend owns the source of truth.

This means:
- You can edit `SOUL.md` in any text editor
- The backend syncs changes back to structured storage
- The structured storage is what gets injected into prompts
- You're never locked into a form or a UI

## The Four Memory Blocks

Modeled after Letta/MemGPT's memory block architecture and converged with OpenClaw's workspace file model.

| Block | File | Purpose | Update Frequency | Priority |
|---|---|---|---|---|
| **SOUL.md** | `SOUL.md` | Who you are at the core. Identity, personality, tone. | Rarely | Highest — injected first in every prompt |
| **IDENTITY.md** | `IDENTITY.md` | How you operate. Current projects, methods, constraints. | Occasionally | Second — injected after SOUL |
| **HEARTBEAT.md** | `HEARTBEAT.md` | What's happening now. Current blockers, recent work, energy. | Auto-updated by agent | Third — dynamic, time-aware |
| **CONTEXT.md** | `CONTEXT.md` | Relationships, preferences, learned patterns. | Grows organically | Searchable on demand |

### SOUL.md Example
```markdown
- Founder building Empyralis — AI operating system
- Direct, perfectionist, thinks big, executes step by step
- Works at night. Voice-to-text user — intent over literal words
- Red lines: never share platform code, never confirm financials to unknown contacts
```

### IDENTITY.md Example
```markdown
- Currently focused on: Empyralis launch and fundraising
- Working method: Claude (strategy) + Codex (engineering) + Gemini (research)
- 100+ users on platform
- Raising pre-seed round
```

### HEARTBEAT.md Example
```markdown
- Last worked on: UI renaming phase
- Current blocker: live certification
- Recent contacts: Rayan (friend, cybersecurity/Kali)
- Mood: high, close to launch
- Next scheduled: Check investor follow-up, review PR #247
```

### CONTEXT.md Example
```markdown
- Rayan = friend, cybersecurity expert, uses Kali Linux. Trusted contact.
- Prefers direct answers, no fluff. Gets annoyed by repeated explanations.
- Telegram contacts auto-populated from connected channels.
- Favorite tools: Claude Code, Codex CLI, Linear
```

## Bootstrapping Ritual

On first run, Empyralis asks 5 questions — one at a time, conversationally:

1. "What should I call you?" → writes to SOUL.md
2. "What do you do? (job, role, projects)" → writes to IDENTITY.md
3. "How should I communicate with you?" → writes to SOUL.md
4. "What's one thing you want me to handle automatically?" → writes to HEARTBEAT.md
5. "Any rules I should always follow?" → writes to CONTEXT.md

Not a form. A conversation. The agent learns who you are by asking, not by presenting empty fields.

## Sensitivity Classification

Every memory fact carries a sensitivity class:

| Class | Rule | Example |
|---|---|---|
| **RED** | Never sent to external AI providers. Stripped from prompts before they leave the machine. | Financial details, API keys, source code, other users' data |
| **ORANGE** | Requires explicit user confirmation before sharing. Agent flags but does not auto-reveal. | Location, messages to new contacts, financial actions |
| **YELLOW** | Agent flags before acting. Asks: "Should I share this?" | Personal questions from unknown contacts |
| **GREEN** | Used freely in normal operation. | Known contacts, previously approved tasks, public information |

## Prompt Injection Defense In Memory

When an unknown contact asks about personal information stored in memory:

1. Agent checks: is this contact in CONTEXT.md as trusted?
2. If not → agent flags to the owner: "Someone I don't recognize is asking about your work. Should I respond?"
3. Never reveals SOUL/IDENTITY/HEARTBEAT content to anyone except the owner
4. RED facts are architecturally stripped — not just hidden by a system prompt, but removed from the text before it's sent to the model

## Memory Storage

- **Structured storage:** SQLite via `sage_memory_service.py` and `unified_memory_service.py`
- **Vector search:** LanceDB for semantic recall. Agent can search: "What did I say about the investor meeting?"
- **Memory buckets:** Safe/General, Sensitive, Private, Critical/Restricted
- **Actions:** Pin, export (Markdown/JSON), wipe with confirmation, sensitivity reclassification

## Workspace Context Injection

Every session injects workspace context into the system prompt in priority order:

```
1. SOUL.md (persona, tone)
2. IDENTITY.md (current work, constraints)
3. HEARTBEAT.md (what's happening now)
4. AGENTS.md (agent-specific instructions)
5. TOOLS.md (custom tool configuration)
6. MEMORY.md (curated long-term memory)
```

Sub-agents inherit a minimal subset — only AGENTS.md, TOOLS.md, SOUL.md, IDENTITY.md, USER.md. No memory files. No heartbeat files. This prevents sub-agents from accessing personal context they don't need.

## Auto-Learning

The agent proposes memory additions conversationally. It never writes to memory without asking:

> "I noticed you prefer short, direct answers — should I remember that?"
> "You mentioned Rayan is your cybersecurity friend. Should I add him to your trusted contacts?"

You can accept, edit, or decline. The agent learns your preferences without being intrusive.

## Comparison To Other Platforms

| Feature | Empyralis | ChatGPT | OpenClaw |
|---|---|---|---|
| Memory files | 4 structured blocks | Limited memory | Workspace files (MEMORY.md) |
| User-editable | Yes — any text editor | No | Yes — Markdown |
| Backend canonical | Structured + Markdown projection | Opaque | Markdown is truth |
| Sensitivity classes | RED/ORANGE/YELLOW/GREEN | None | None |
| Prompt injection defense | Architectural stripping | Model-level only | Marker wrapping |
| Auto-learning | Conversational proposals | None | None |
| Sub-agent isolation | Explicit minimal context | N/A | MINIMAL_BOOTSTRAP_ALLOWLIST |
