# Vault Structure Template
# Copy this directory structure as the starting point for your tiered memory system.

```
vault/
├── identity/                    # TIER 1: The Soul (20% reserved budget)
│   ├── MISSION.md               # Why the AI exists. Core purpose.
│   ├── BELIEFS.md               # What the AI holds true. Operating principles.
│   ├── GOALS.md                 # What the AI is working toward.
│   ├── PROTOCOLS.md             # How the AI behaves. Communication rules.
│   └── README.md                # Guide for maintaining identity files.
│
├── patterns/                    # TIER 2: Core (15% budget)
│   └── sovereign/
│       └── system.md            # The sovereign system prompt. Loaded every session.
│
├── knowledge/                   # TIER 3: Semantic (50% budget)
│   ├── TOPIC_ONE.md             # One file per concept. YAML frontmatter required.
│   ├── TOPIC_TWO.md             # Retrieved per query by relevance scoring.
│   ├── DECISIONS_LOG.md         # Key decisions with dates and rationale.
│   └── ...                      # Add files as knowledge grows.
│
└── logs/                        # TIER 4: Episodic (15% budget, decaying)
    └── sessions/
        ├── 2026-03-05_001.jsonl # Session logs. Auto-pruned after threshold.
        └── ...
```

## Frontmatter Template

Every file in `knowledge/` should start with:

```yaml
---
tier: SEMANTIC
domain: TECHNICAL        # TECHNICAL | PRODUCT | PROCESS | PERSONAL | EMPIRE
project: YOUR_PROJECT
status: ACTIVE           # ACTIVE | COMPLETE | ARCHIVED
updated: 2026-03-05
---
```

## Rules
- Identity files are ALWAYS loaded. They don't need frontmatter — they're loaded by path, not by search.
- Knowledge files are retrieved by hybrid search. Frontmatter helps with filtering and maintenance.
- Session logs are ephemeral. They get summarized and pruned on a schedule.
- One file per concept. Don't create mega-documents — they hurt retrieval precision.
