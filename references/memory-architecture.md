# Memory Architecture — Reference Document

## Why Tiers?

A flat memory system treats everything as equally important. This creates predictable failures at scale:

**Without tiers:** Every piece of stored content competes equally for the context window. A recipe from last week can outrank your AI's mission statement because the embedding vectors happen to be closer to the current query. The AI "forgets" who it is — not because the data was deleted, but because something else took its place in the context window.

**With tiers:** Critical content gets reserved space. The AI's identity is architecturally guaranteed, not dependent on search relevance. Knowledge is organized by persistence and priority, not just by similarity scores.

## The Budget Constraint

Context windows are finite. This is the fundamental constraint that makes tiered memory necessary.

A typical local model (7B-14B parameters) has an 8,192 token context window. Some models support 32K or 128K, but longer contexts come with diminishing quality — the model pays less attention to content in the middle of long contexts ("lost in the middle" problem).

The practical recommendation: **optimize for 8K-16K context windows even if your model supports more.** Tighter budgets force better curation, which produces better responses.

## Non-Competitive Budget Reservation

This is the single most important architectural decision in the tiered model.

**Standard RAG approach:**
```
[Search results fill the entire context window]
[System prompt is included but can be displaced if results are long]
[Identity information is just another search result]
```

**Tiered approach:**
```
[Identity: 20% RESERVED — always present, cannot be displaced]
[Core: 15% — high-priority content]
[Semantic: 50% — search results ranked by relevance]
[Episodic: 15% — recent conversation context]
```

The reservation is enforced by the context compiler, which assembles the final prompt. Identity content is injected first, with a guaranteed token budget. Only then does semantic search fill the remaining space.

## The Two-Level Memory Model

Memory operates at two levels:

**Level 1: Bootstrap Memory**
Content loaded before any search happens. This includes:
- Identity files (always)
- Core patterns (always)
- Session briefing (recent context summary)

Bootstrap memory is deterministic — the same content loads regardless of what the user asks.

**Level 2: Semantic Search Memory**
Content retrieved based on the current query. This includes:
- Knowledge files ranked by hybrid search (BM25 + vector + RRF)
- Historical session content ranked by relevance

Semantic memory is query-dependent — different questions retrieve different content.

The two levels work together: bootstrap provides the stable foundation (who am I, what do I know permanently), semantic provides the dynamic layer (what's relevant right now).

## Consolidation: The Bridge Between Tiers

Conversations are episodic. Insights should be semantic. The consolidation process bridges the gap.

**Without consolidation:** Knowledge generated during conversations stays in session logs and eventually decays. The AI has the same conversation three times because each session starts fresh.

**With consolidation:** An agent scans recent sessions for promotable content — decisions, insights, preferences, corrections — and proposes moving them to the semantic tier as permanent knowledge files. The user approves or rejects each proposal.

The approval step is critical. Automated consolidation without human review leads to vault bloat — every offhand comment becomes "knowledge." The user's judgment about what's worth keeping is the quality filter.

## Decay: The Forgetting Function

Not everything should be remembered forever. Healthy memory systems forget.

**Episodic decay:** Session logs older than a threshold (default: 30 days) are:
1. Summarized into a single digest file
2. The raw logs are pruned
3. The digest is indexed at lower priority

**Semantic decay:** Knowledge files marked `status: ARCHIVED` get lower retrieval priority but aren't deleted. They're still findable if someone asks specifically, but they don't crowd out active content.

**Identity never decays.** This is the permanent layer. Changes to identity are deliberate, not automatic.

## Common Failure Modes

| Failure | Cause | Fix |
|---|---|---|
| AI forgets who it is | Identity budget not reserved | Implement non-competitive budget reservation |
| Irrelevant content in responses | Flat ranking without tier priority | Add tier-based relevance boosting |
| Vault grows uncontrollably | No decay or archival process | Implement episodic pruning + semantic archival |
| Same conversation repeating | No consolidation from episodic to semantic | Implement consolidation agent with approval |
| Old decisions overriding new ones | Stale files with high relevance scores | Date everything, review `updated` timestamps |
| AI responses feel generic despite rich vault | Chunks too large, retrieving irrelevant context | Smaller chunks with overlap, one concept per file |

---

*Part of the oikOS Sovereign Kernel. For the complete architecture, visit oikos-os.com.*
