---
name: ccai-obsidian-wiki
description: Turns Claude Code into a knowledge worker for your Obsidian vault (or any folder of markdown notes). Reads, indexes, queries, summarizes, and updates your notes — with tiered retrieval that scans titles + headings first, fetches full content only when needed. Cuts token usage 5-10x vs. naive "read all my notes" approaches. Use when the user has an Obsidian vault (or any markdown notes folder) and wants Claude to draw from it intelligently.
when_to_use: User mentions Obsidian, Zettelkasten, second brain, knowledge management, "search my notes," "what have I written about X," personal wiki, vault, or asks Claude to draw from a notes folder.
argument-hint: "[path to vault, or query]"
---

# CCAI Obsidian Wiki

Smart retrieval for any folder of markdown notes — Obsidian, Logseq, plain notes folder. Tiered scanning saves 5-10x tokens vs. naive approaches.

## Output contract

Maintains in the vault root:
- `_VAULT_INDEX.md` — auto-generated index of all note titles + headings (tier 1 retrieval)
- `_VAULT_QUERIES.md` — log of queries run + which notes were retrieved

When the user adds new content to Claude Code (via summarize / link / draft), the skill can also write back to the vault.

## The tiered retrieval strategy

Naive approach: "read all my notes" pulls every full file into context. 200 notes = 100K+ tokens, slow + expensive.

This skill's approach:

**Tier 1 — Index scan** (cheap, fast)
Read `_VAULT_INDEX.md` only (titles + H1/H2 headings of every note). 5-10K tokens.
Identifies which 1-5 notes are *probably* relevant.

**Tier 2 — Targeted fetch** (medium cost)
Only read the full content of the 1-5 candidate notes. Another 10-30K tokens.

**Tier 3 — Cross-reference** (when needed)
If the candidate notes link to other notes (`[[wikilink]]`), fetch those too — but only the relevant section, not the whole note.

Total typical query cost: 20-40K tokens vs. 200K+ naive. 5-10x savings.

## Process

### Step 1 — Setup (first run)
- Ask for vault path
- Scan all `.md` files recursively
- Build `_VAULT_INDEX.md` — entry per note with: title, top-3 headings, last-modified date, tag list
- Set up `.claude/skills/` ignore patterns so the skill doesn't index itself

### Step 2 — Query mode
When user asks a question:
1. Read `_VAULT_INDEX.md`
2. Identify 1-5 candidate notes based on title/heading match
3. Show user the candidates: "I think these are relevant: [list]. Want me to fetch them?"
4. On confirmation, fetch full content
5. Answer the question using the fetched notes — with verbatim quotes + file paths as citations
6. Log query + retrieval to `_VAULT_QUERIES.md`

### Step 3 — Update mode
When user wants Claude to write to the vault:
1. Confirm path + filename
2. If file exists: ask whether to append or replace
3. Write the content
4. Update `_VAULT_INDEX.md` to include the new note's headings
5. Suggest 2-3 wikilinks to existing notes that the new content references

### Step 4 — Reindex (periodic)
Periodically (when ≥10 notes added since last index): re-scan the vault and rebuild `_VAULT_INDEX.md`.

## Hard rules

- **Always cite the file path.** Every answer that draws from the vault must include `[note-title.md]` references.
- **Tier 1 first, no shortcuts.** Even if the user says "just read everything," push back — explain the token cost, suggest the smarter path.
- **Never delete vault notes.** Only append, replace (with explicit confirm), or create new.
- **Respect Obsidian conventions.** Use `[[wikilinks]]` for cross-references. Honor frontmatter. Don't mangle aliases.

## Pro version differences

`ccai-obsidian-wiki-pro` (planned):
- Bidirectional Obsidian sync via the Obsidian REST API (if Obsidian is running)
- Auto-tagging suggestions
- Backlink graph analysis
- Semantic search via local embeddings (no API needed — uses local model)

## Reference files
- `templates/_VAULT_INDEX.md` — schema for the index file
- `examples/sample-vault-index.md` — filled example for a 25-note personal vault
