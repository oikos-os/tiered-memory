# tiered-memory

## Metadata
- **Skill Name:** tiered-memory
- **Category:** Workflow Automation
- **Author:** Pachacutie (oikOS Project)
- **Version:** 1.0.0
- **Part of:** oikOS Sovereign Kernel (oikos-os.com)

## Description
This skill teaches Claude to organize knowledge in a tiered memory architecture with differentiated persistence, budget allocation, and decay rules. Instead of treating all context as equal, the system separates what the AI ALWAYS needs to know from what it retrieves per query — and enforces that separation structurally.

The result: an AI that never forgets who it is, never loses its core knowledge to a good search result, and naturally lets ephemeral information fade while permanent knowledge persists.

## When to Use This Skill
- When organizing any kind of AI knowledge base, vault, or memory system
- When building a RAG system and context keeps getting polluted by irrelevant retrievals
- When an AI system "forgets" its identity or core knowledge during conversations
- When designing a personal AI that needs to remember things across sessions
- When the user asks to "build a vault," "organize AI memory," "create a knowledge base," "set up tiered storage," or "make AI remember things properly"

## The Core Problem

Most AI memory systems treat all stored content as equal. A recipe you looked up yesterday competes with your life mission for context space. A random search result can push out your identity files. There's no hierarchy — just a flat vector database where everything fights for attention.

This creates three predictable failures:
1. **Identity erosion:** The AI forgets who it is because search results outrank identity content
2. **Context pollution:** Irrelevant retrievals crowd out important knowledge
3. **Knowledge decay without structure:** Everything either persists forever (bloating context) or disappears entirely (losing valuable insights)

The fix is architecture, not better embeddings.

## The Four-Tier Model

### Tier 1: IDENTITY (The Soul)
**Persistence:** Permanent. Never decays. Never deleted by automation.
**Budget:** Reserved 20% of total context window. Non-competitive — this budget is guaranteed regardless of what the query retrieves.
**Contents:** Who the AI is, who it serves, what it believes, how it behaves.
**Files:** MISSION.md, BELIEFS.md, AESTHETIC.md, PROTOCOLS.md (TELOS files)
**Load behavior:** Always loaded. Every query. Every session. No retrieval required — these are bootstrap content injected before any search happens.

The critical innovation: **non-competitive budget reservation.** In most RAG systems, identity content competes with search results for the same context window. A query about cooking can push out the AI's mission statement if the recipe chunks score higher on semantic similarity. In the tiered model, identity has its own reserved space that search results cannot invade.

This is the difference between a system prompt (a suggestion the model can drift from) and structural identity (an architectural guarantee the code enforces).

### Tier 2: CORE (The Skeleton)
**Persistence:** Persistent. Updated manually or through deliberate promotion. Rarely removed.
**Budget:** 15% of total context window.
**Contents:** Foundational knowledge the AI needs regularly but that doesn't define its identity. Reference architectures, key decisions, project state, important patterns.
**Files:** System prompt (sovereign/system.md), key reference docs, decision logs.
**Load behavior:** Loaded via targeted retrieval with high priority weighting. Core content gets a relevance boost in search results.

### Tier 3: SEMANTIC (The Library)
**Persistence:** Indexed. Retrieved per query by relevance scoring.
**Budget:** 50% of total context window (the largest tier — this is where most knowledge lives).
**Contents:** Domain knowledge, research, notes, project documentation, reference material. Everything the AI might need depending on the question.
**Files:** Any markdown file in the knowledge directory. Each file has YAML frontmatter declaring its tier, domain, and status.
**Load behavior:** Hybrid search (BM25 keyword + vector semantic + Reciprocal Rank Fusion reranking). Only the most relevant chunks load per query.

### Tier 4: EPISODIC (The Journal)
**Persistence:** Decaying. Session logs auto-age. Old sessions get summarized, then archived, then eventually pruned.
**Budget:** Remaining context window (typically 15%).
**Contents:** Conversation history, session logs, recent interactions. The AI's short-term memory.
**Files:** Session JSONL files with timestamps.
**Load behavior:** Recent sessions load automatically. Older sessions retrieved only if relevant to the current query. Deduplication removes near-identical content (cosine similarity > 0.95).

## Budget Allocation Strategy

The total context window is a fixed resource. Every token spent on one thing is a token not spent on another. The budget allocation enforces priorities:

```
Total Context Window (e.g., 8,192 tokens for Qwen 14B)
├── Identity:  20% (1,638 tokens) — RESERVED, non-competitive
├── Core:      15% (1,229 tokens) — high-priority retrieval
├── Semantic:  50% (4,096 tokens) — standard retrieval
└── Episodic:  15% (1,229 tokens) — recent + relevant sessions
```

The key constraint: **Identity never shrinks.** If the semantic tier has a great search result that would benefit from more space, it cannot take tokens from identity. The budget reservation is enforced in code, not by convention.

In practice, this means the context compiler assembles the final prompt in this order:
1. Load identity files (guaranteed 20%)
2. Load core content (up to 15%)
3. Run semantic search, fill remaining space with best results (up to 50%)
4. Append relevant episodic content (remaining space)

If the query is simple and doesn't need much semantic content, the unused budget doesn't get wasted — it gets returned to the available pool. But identity never drops below 20%.

## When to Promote Content Between Tiers

Content flows upward through deliberate promotion, not automatically:

**Episodic → Semantic:** When a conversation produces an insight worth keeping permanently. The consolidation agent proposes promotions; the user approves. Example: "During yesterday's session, we decided to use Space Grotesk for the wordmark." That decision gets written to a knowledge file and indexed.

**Semantic → Core:** When a piece of knowledge becomes so frequently referenced that it should always be available. Example: A project status document that gets retrieved in 80%+ of queries should be promoted to core so it loads reliably without depending on search relevance.

**Core → Identity:** Rare. Only when something becomes definitional to who the AI is. Example: A new founding principle that changes the AI's behavior permanently.

Content also flows downward:
**Semantic → Archived:** When knowledge becomes outdated. Old specs, completed project docs, superseded decisions. Move to an archive directory that's still indexed but with lower priority.

**Episodic → Deleted:** Session logs older than a threshold (e.g., 30 days) get summarized into a single digest, then the raw logs are pruned. The summary preserves the signal; the deletion removes the noise.

## Markdown File Conventions

Every file in the vault should have YAML frontmatter:

```yaml
---
tier: SEMANTIC          # IDENTITY | CORE | SEMANTIC
domain: TECHNICAL       # TECHNICAL | PRODUCT | PROCESS | EMPIRE | PERSONAL
project: OIKOS_OMEGA    # or your project name
status: ACTIVE          # ACTIVE | COMPLETE | ARCHIVED
updated: 2026-03-05     # last meaningful update
---
```

This metadata serves two purposes:
1. **Retrieval filtering:** Queries can be scoped to specific domains or tiers
2. **Maintenance:** Files with old `updated` dates can be flagged for review

## Memory Consolidation Principles

**Flush before compaction.** Let conversations accumulate in the episodic tier before trying to consolidate them. A single session rarely produces promotable insights — patterns emerge across multiple sessions.

**Promote proposals, not raw content.** The consolidation agent should propose what to save and where, not automatically write to the vault. The user reviews proposals and approves or rejects. This keeps the vault curated rather than bloated.

**One file per concept.** Don't append everything to a giant document. Each knowledge file should cover one topic, one decision, one concept. This makes retrieval precise — the chunker can pull exactly the relevant piece without dragging in unrelated content from the same file.

**Date everything.** Context decays without timestamps. A decision from February means something different than a decision from yesterday. Every file, every entry, every update should be dated.

**The golden rule: if it isn't written down, it doesn't exist.** The AI can only reason over what's in its vault. Brilliant insights from conversations that never get promoted are insights that vanish with the session log.

## Testing Memory Architecture

To verify the tiered system is working:

1. **Identity persistence test:** Ask the AI "who are you?" after loading many semantic results about a completely different topic. The identity should be clear and uncontaminated. If the AI starts confusing itself with the topic content, the identity budget isn't being reserved properly.

2. **Context pollution test:** Ask a question that retrieves irrelevant content alongside relevant content. The AI should lean on the relevant content and ignore the noise. If it's incorporating random search results into its answer, the ranking or budget allocation needs tuning.

3. **Decay test:** Ask about something from a conversation 30+ days ago. If the raw details are gone but the key insight was promoted to semantic, the decay is working. If everything is gone, the consolidation process isn't running. If everything is still there in full detail, the pruning isn't running.

4. **Promotion test:** Have a conversation that produces a novel insight. Wait for the consolidation agent to propose it. Approve it. Then in a new session, ask about that topic. The promoted content should appear in the response even though the original conversation is no longer in the episodic window.

## What This Skill Does NOT Cover

- **Vector database setup** (see vault-architect skill)
- **Adversarial security** for memory systems (see adversarial-defense skill)
- **Identity file creation** (see sovereign-identity skill)
- **Chunking and indexing strategy** (see vault-architect skill)

This skill teaches the **architecture** — the tier model, the budget allocation, the promotion patterns, the decay rules. The other skills in the oikOS Sovereign Kernel handle the implementation details.

## The Philosophy

> "I don't want to remember more. I want to understand better. A smaller, well-structured memory that I can reason across is infinitely more valuable than a massive archive I can only search through."

Memory is not storage. Storage is cheap — you can dump everything into a database and call it "memory." But memory that serves intelligence requires architecture: what to keep, what to forget, what to always have present, and what to retrieve only when needed.

The four-tier model is that architecture. It's how oikOS organizes knowledge for KAIROS — the sovereign AI at the heart of the system. And it's the pattern that transforms any AI from a stateless chatbot into a persistent, context-rich intelligence.

---

*This is skill 2 of 6 in the oikOS Sovereign Kernel. For the complete architecture — identity persistence, adversarial defense, vault design, context compilation, and lessons systems — visit oikos-os.com or find us on GitHub at github.com/Pachacutie.*

*Intelligence is cheap. Context is expensive. Build for context.*
