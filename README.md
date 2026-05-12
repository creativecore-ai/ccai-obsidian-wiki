# ccai-obsidian-wiki

> Smart retrieval for your Obsidian vault (or any folder of markdown notes). Tiered scanning saves 5-10x tokens vs. naive "read all my notes" approaches.

**Slash command:** `/ccai-obsidian-wiki`
**Status:** v0.1 · Tier B (works without external services) · works with Claude Code

---

## What it does

Tells Claude how to read your second brain efficiently. The naive way pulls every note into context (200 notes ≈ 100K+ tokens). This skill uses a tiered approach:

1. **Tier 1 (index scan):** read just the auto-generated `_VAULT_INDEX.md` with titles + headings of every note (~5-10K tokens)
2. **Tier 2 (targeted fetch):** read only the 1-5 candidate notes that look relevant
3. **Tier 3 (cross-ref):** if a candidate links to another note, fetch the relevant section only

Typical query: 20-40K tokens. Naive: 200K+. **5-10x cheaper, faster, and longer-lasting context.**

## Why this matters for business owners

If you've built a Notion / Obsidian / second-brain over years, that knowledge is one of your biggest assets — but Claude alone can't access it without help. This skill is the access layer.

## What you need

- A folder of `.md` files (Obsidian vault, Logseq graph, or just a `notes/` folder)
- Claude Code installed
- No Obsidian REST API required (pro version uses it for live sync)

## Install

```bash
git clone https://github.com/cory-dot/ccai-obsidian-wiki ~/.claude/skills/ccai-obsidian-wiki
```

## Usage

First run (builds index):
```
/ccai-obsidian-wiki setup [path-to-vault]
```

Query mode:
```
/ccai-obsidian-wiki "what have I written about cold outreach?"
```

Write mode (Claude writes to vault):
```
/ccai-obsidian-wiki write "summary of today's strategy call"
```

## Pro version

`ccai-obsidian-wiki-pro` adds:
- Bidirectional Obsidian REST API sync
- Auto-tagging suggestions
- Backlink graph analysis
- Local semantic embeddings (no API)

## License

MIT.
