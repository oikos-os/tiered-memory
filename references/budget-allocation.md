# Budget Allocation — Reference Document

## Default Allocation

```
Context Window: 100%
├── Identity (Tier 1):   20%  — RESERVED, non-competitive
├── Core (Tier 2):       15%  — high-priority retrieval
├── Semantic (Tier 3):   50%  — standard retrieval (largest tier)
└── Episodic (Tier 4):   15%  — recent sessions + relevant history
```

## Allocation by Model Context Size

| Model | Context | Identity | Core | Semantic | Episodic |
|---|---|---|---|---|---|
| 7B (4K) | 4,096 | 819 | 614 | 2,048 | 614 |
| 14B (8K) | 8,192 | 1,638 | 1,229 | 4,096 | 1,229 |
| 14B (32K) | 32,768 | 6,554 | 4,915 | 16,384 | 4,915 |
| 70B+ (128K) | 131,072 | 26,214 | 19,661 | 65,536 | 19,661 |

**Note:** Larger context windows don't change the percentages — they change the absolute token counts. Even with 128K context, maintaining the 20% identity reservation ensures the AI never loses itself.

## When to Adjust the Allocation

**Increase identity budget (to 25-30%)** when:
- The AI has a complex identity with many TELOS files
- Identity erosion is observed during testing
- The system serves multiple distinct roles that need to coexist

**Decrease identity budget (to 15%)** when:
- Identity is simple (single mission, few beliefs)
- Context window is very small (4K or less)
- Maximum retrieval capacity is needed

**Increase semantic budget (to 60%)** when:
- The vault is very large (1000+ files)
- Queries span diverse domains
- Retrieval quality is the primary concern

**Increase episodic budget (to 20-25%)** when:
- Multi-turn conversations are the primary use case
- Session context is critical for response quality
- The AI needs to reference recent interactions frequently

## The Non-Competitive Guarantee

The identity budget is not a "suggestion" or a "target." It is a hard reservation enforced by the context compiler.

The implementation pattern:

```
1. Calculate total available tokens
2. Reserve identity_budget = total * 0.20
3. Load identity files into reserved space
4. Calculate remaining = total - tokens_used_by_identity
5. Allocate remaining across core, semantic, episodic
6. Run retrieval to fill each tier's allocation
7. Assemble final prompt: identity + core + semantic + episodic + query
```

If identity files don't fill their full 20%, the unused tokens flow to the semantic tier (more retrieval capacity). But the reservation is always made first.

## Budget Overflow Handling

When a tier has more relevant content than its budget allows:

- **Identity overflow:** This shouldn't happen — identity files should be curated to fit within 20%. If it does, truncate the least critical identity file (usually GOALS.md is less critical than MISSION.md).
- **Core overflow:** Rank by access frequency. Most-accessed core files load first.
- **Semantic overflow:** This is normal — that's what retrieval ranking is for. The top-ranked results fill the budget; everything else is available for future queries.
- **Episodic overflow:** Most recent sessions first. Older sessions only load if they're specifically relevant to the current query.

---

*Part of the oikOS Sovereign Kernel. For the complete architecture, visit oikos-os.com.*
