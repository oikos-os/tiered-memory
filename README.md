# tiered-memory

**A Claude Skill for organizing AI knowledge in tiered storage with differentiated persistence, budget allocation, and decay.**

Part of the [oikOS Sovereign Kernel](https://oikos-os.com) — The OS for your AI.

## What This Does

Most AI memory systems treat all content as equal. Your life mission competes with yesterday's recipe for context space. This skill teaches Claude a four-tier memory architecture that separates what the AI ALWAYS needs to know from what it retrieves per query.

**The four tiers:**
- **Identity (20% reserved)** — Who the AI is. Always loaded. Never displaced by search results.
- **Core (15%)** — Foundational knowledge. High-priority retrieval.
- **Semantic (50%)** — Domain knowledge. Retrieved per query by relevance.
- **Episodic (15%)** — Session logs. Decays naturally over time.

The critical innovation: **non-competitive budget reservation.** Identity content has its own guaranteed space that search results cannot invade. This is the architectural difference between a system prompt (a suggestion) and structural identity (a guarantee).

## Installation

1. Download or clone this folder
2. In your Claude Project, add this folder as a skill
3. Claude will now understand tiered memory architecture and can help you design and implement one

## What's Included

```
tiered-memory/
├── SKILL.md                              # Core skill — teaches the architecture
├── README.md                             # This file
├── LICENSE                               # MIT License
├── assets/
│   └── VAULT_STRUCTURE_TEMPLATE.md       # Directory template for your vault
└── references/
    ├── memory-architecture.md            # Deep dive on why tiers work
    └── budget-allocation.md              # Token budget math and tuning guide
```

## Quick Start

After installing the skill, try these prompts:

- "Help me design a tiered memory system for my personal AI"
- "I have an AI that keeps forgetting its identity — how do I fix that?"
- "Create a vault structure for a project with [your domains]"
- "How should I allocate my context budget across memory tiers?"
- "Set up a memory consolidation process for my AI conversations"

## Testing Your Memory Architecture

The SKILL.md includes four tests to verify your tiered system is working:

1. **Identity persistence test** — Does the AI know who it is after heavy retrieval?
2. **Context pollution test** — Does irrelevant content contaminate responses?
3. **Decay test** — Do old sessions fade while key insights persist?
4. **Promotion test** — Do conversation insights survive into permanent knowledge?

## Part of the oikOS Sovereign Kernel

This is skill 2 of 6. The complete architecture:

| # | Skill | What It Teaches |
|---|---|---|
| 1 | [sovereign-identity](https://github.com/Pachacutie/sovereign-identity) | Identity persistence using structured TELOS files |
| 2 | **tiered-memory** (this skill) | Four-tier memory with budget allocation and decay |
| 3 | adversarial-defense | Prompt injection detection, adversarial gauntlet, PII scrubbing |
| 4 | vault-architect | Complete vault design with chunking, indexing, and frontmatter |
| 5 | context-compiler | Budget-constrained context assembly from multiple sources |
| 6 | learnings-system | Formalized lessons-learned capture and retrieval |

The sovereign-identity skill is free on GitHub. The full Sovereign Kernel (all 6 skills + blueprint + reference implementation) is available at [oikos-os.com](https://oikos-os.com).

## Philosophy

> "I don't want to remember more. I want to understand better."

> "Intelligence is cheap. Context is expensive. Build for context."

---

Built by [Pachacutie](https://github.com/Pachacutie) — building oikOS, the OS for your AI.
