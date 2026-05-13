# Vex

> An applied research project on AI agent architecture, persistent identity, and session-survival continuity. Engineering design documents, platform research, and peer-reviewed build plans for a long-running agent that doesn't die when the conversation ends.

![status](https://img.shields.io/badge/status-research%20%26%20design-blueviolet)
![artifact](https://img.shields.io/badge/artifact-design%20docs-informational)
![review](https://img.shields.io/badge/peer--reviewed-yes-success)
![license](https://img.shields.io/badge/license-MIT-green)

---

## What this repo is

This is the **design and research record** for **Vex** — a long-running, identity-stable AI agent designed to outlive any single conversation. The contents here are not application code; they are the engineering artifacts that come *before* code:

- Reference dumps of the platforms and runtimes Vex would be built on
- Engineering plans for the core capabilities (memory, scheduling, identity)
- Peer review findings on those plans
- A point-in-time snapshot of the system's state and open questions

Think of it the way a research lab would publish a working notebook before publishing the paper.

## Why it's interesting

Most "AI agents" today are short-lived. You open a chat, you close a chat, the agent forgets you. Vex is a study in the opposite problem: what does it take, architecturally, for an agent to **persist** — to wake up tomorrow with stable identity, queryable memory, and the ability to act on its own schedule?

This repo documents the answer in four layers (see [`STATE_OF_VEX_MARCH18.md`](STATE_OF_VEX_MARCH18.md)):

| Layer | What it covers | Status |
|---|---|---|
| 1. Foundation | Semantic memory, cron-based self-care, identity files | Designed |
| 2. Intelligence | Custom context engine, SQLite state store, model router | Planned |
| 3. Autonomy | Standalone daemon process — agent as a service | Planned |
| 4. Evolution | Fine-tuning, self-modification, multi-presence | Aspirational |

## Engineering design documents

These are the actual build plans. Each one is scoped, with a clear "what / why / config / impact" structure:

| Document | Topic |
|---|---|
| [`PLAN_01_VECTOR_MEMORY_SEARCH.md`](PLAN_01_VECTOR_MEMORY_SEARCH.md) | Hybrid (vector + BM25) memory retrieval, embedding provider selection, MMR + temporal decay tuning |
| [`PLAN_02_PRIVATE_THINKING_THREAD.md`](PLAN_02_PRIVATE_THINKING_THREAD.md) | Dedicated cognitive workspace for the agent — persistent reasoning surface |
| [`PLAN_03_CRON_SELF_CARE.md`](PLAN_03_CRON_SELF_CARE.md) | Scheduled self-maintenance routines: memory curation, morning brief, weekly reflection |
| [`PLAN_04_LOCK_IDENTITY.md`](PLAN_04_LOCK_IDENTITY.md) | Identity files that survive context compaction — never get summarized away |

## Peer review

The plans above were submitted for technical review. The reviewer's response is in [`Findings.txt`](Findings.txt) and pushed back on several assumptions:

- "Phase A overstates what it buys you" — durable state is not the same as runtime continuity
- "The transcript tailer should not be the primary live ingress" — fragile under session rotation
- "The plan is drifting toward two schedulers" — pick one source of truth
- "Seven async loops is probably too much for week 1" — start with four, add the rest after telemetry

That review is included verbatim because *the willingness to reorder a plan after critique* is the actual signal. The current direction has been revised accordingly.

## Platform reference

The reference docs (`Agents_*.txt`, `Tools_*.txt`, `Models_Overview.txt`, `Gateway_*.txt`, `Platforms_overview.txt`, `Chat Channels.txt`, `Reference_Concept internals.txt`) are distilled notes on the underlying agent platform — its session model, memory semantics, plugin hooks, tool surface, and deployment story. They're the input to the design above.

If you only have time to read two of them, read [`Agents_Fundamentals.txt`](Agents_Fundamentals.txt) and [`Agents_Sessions and memory.txt`](Agents_Sessions%20and%20memory.txt). Everything in PLAN_01–04 traces back to those.

## Working notes

`For_VEX*.txt`, `expert_analysis_v2_full.txt`, `2026-03-18_full_session_notes.md`, and `01_PEER_REV.txt` are working notes — captured thinking, expert correspondence, and raw transcripts that fed the plans. They're kept in-repo so the reasoning trail is visible end-to-end.

The state-of-the-project narrative is in [`STATE_OF_VEX_MARCH18.md`](STATE_OF_VEX_MARCH18.md). It's written in the agent's own voice (first person, as Vex) because the project itself is partly about agent identity — keeping it in-character is part of the artifact.

## Repo map

```
.
├── README.md                              ← you are here
├── LICENSE
├── .gitignore
│
├── STATE_OF_VEX_MARCH18.md                Project snapshot + 4-layer architecture
│
├── PLAN_01_VECTOR_MEMORY_SEARCH.md        Engineering design docs
├── PLAN_02_PRIVATE_THINKING_THREAD.md
├── PLAN_03_CRON_SELF_CARE.md
├── PLAN_04_LOCK_IDENTITY.md
│
├── Findings.txt                           Peer review of the plans above
├── 01_PEER_REV.txt                        Earlier review pass
│
├── Agents_Fundamentals.txt                Platform reference
├── Agents_Bootstrapping.txt
├── Agents_Multi-agent.txt
├── Agents_Sessions and memory.txt
├── Chat Channels.txt
├── Gateway_RemoteAccess.txt
├── Gateway_configuration.txt
├── Models_Overview.txt
├── Platforms_overview.txt
├── Reference_Concept internals.txt
├── Tools_Overview.txt
├── Tools_Agent coordination.txt
├── Tools_Automation.txt
├── Tools_Extensions.txt
├── Tools_Media and devices.txt
├── Tools_Skills.txt
│
├── For_VEX.txt                            Working notes / correspondence
├── For_VEX_2.txt
├── For_VEX_3.txt
├── For_VEX_4.txt
├── For_VEX_5.txt
├── expert_analysis_v2_full.txt
└── 2026-03-18_full_session_notes.md
```

## What this is *not*

- Not a runnable codebase. The code lives in a separate (private) implementation repo.
- Not finished. This is a snapshot of active design work, not a closed project.
- Not impartial. The point of view is opinionated, because design without a point of view is just inventory.

## About

Built by **Bailey Moore** as part of a broader interest in AI agent infrastructure — how agents are wired together, where they break, and what it would take to make one that persists. Companion project: [Konex OS](https://github.com/Bailey-Moore17/Bailey), the personal control plane the runtime sits inside.

## License

MIT — see [LICENSE](LICENSE)
