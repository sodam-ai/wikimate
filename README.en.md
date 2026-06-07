# Wikimate

> Tell an AI agent "organize this," and it tidies scattered materials into **Obsidian notes (the source of truth)** and indexes them into **Notion (the index)**. A **Claude Code plugin + portable MCP core**.

**🌐 [한국어 README](./README.md)**

---

## What is Wikimate? (one-line analogy)
A secretary that turns the stuff scattered across your browser tabs, downloads folder, and AI chats into **personal knowledge notes you (and AI) can find and re-read later**.

## Current status (development stage)
- **Phase 1a (now)**: MCP core + collect tool (`wikimate_collect`). Turns materials into Obsidian notes (.md). Includes **safety gate, dedup, and injection defense**. The server is **zero-dependency**, so it just works once installed.
- Next: auto-classify & Notion index (1b) → auto-trigger skills (2) → marketplace release (3, after verification).

## Architecture at a glance
```
Obsidian  = source / long-term memory (.md files)
Notion    = index / dashboard           (Phase 1b)
MCP core  = one organizing logic        (shared by Claude Code & Codex = model-agnostic)
Safety    = analyze -> report -> human approval -> execute
```

---

## Install (Claude Code) — step by step for beginners

### Regular users — install from GitHub (recommended)
In the Claude Code prompt:
```
/plugin marketplace add sodam-ai/wikimate
/plugin install wikimate@wikimate-marketplace
```
→ **Restart** Claude Code → type `/mcp` → if you see `wikimate_collect`, it works.

> ⚠️ **The repository must be "public" for others to install it.** It is currently private, so **public distribution comes after verification (Phase 3)**. (No separate `npm install` is needed — the server is zero-dependency.)

### Developers / self-test — from a local folder
```
/plugin marketplace add <path to this folder>
/plugin install wikimate@wikimate-marketplace
```
→ Restart → check `/mcp`.
> The local path exists **only on your PC**, so it is not for sharing (dev/test only).

---

## Usage
Just ask in chat. Example:
```
Use wikimate_collect to turn this into a note.
title="What is MCP?", text="...body...", vault_path="<my vault path>", dry_run=true
```
- **`dry_run=true` (default)**: reports the plan only, writes nothing. Set `false` to actually create.
- Re-submitting the same material is **blocked by `source_hash`** (dedup).
- The note gets frontmatter (title, source, date, summary, tags, importance) + body.

## Environment variables
| Variable | Description |
|---|---|
| `OBSIDIAN_VAULT_PATH` | Absolute path to your Obsidian vault. If unset, pass `vault_path` per call |

> Notion variables come in Phase 1b. Keep real values in `.env` and **never commit them**. See `.env.example`.

## For developers (local verification)
```bash
npm install        # dependencies for verification only (not needed for the plugin to run)
npm run verify     # verify collect logic
npm start          # run the MCP server (stdio) — zero-dependency, needs only Node
```
> The plugin's MCP server has **no external dependencies.** `npm install` is only needed for the `smoke-server` check (official SDK client).

## Troubleshooting
| Symptom | Cause | Fix |
|---|---|---|
| Tool not shown in `/mcp` | No restart after install | Restart Claude Code |
| "vault_path required" error | Vault path not set | Pass `vault_path` in the call or set `OBSIDIAN_VAULT_PATH` |
| GitHub install fails | Repository is private | Make the repo public (after verification) or use the local method |

---

## Safety & security
- Writes/deletes go through a **human approval gate** (`dry_run` is the default).
- Instructions inside external materials are treated as **data, not commands** — **prompt-injection defense**.
- API keys, tokens, and personal data are never stored in notes or the repo. `.env` is git-excluded.

## References
| Tool | Role |
|---|---|
| [Yakitrak/notesmd-cli](https://github.com/Yakitrak/notesmd-cli) | Obsidian CLI (headless) |
| [MarkusPfundstein/mcp-obsidian](https://github.com/MarkusPfundstein/mcp-obsidian) | Obsidian MCP |
| [makenotion/notion-mcp-server](https://github.com/makenotion/notion-mcp-server) | Notion MCP (official) |
| [Notion `ntn` CLI](https://developers.notion.com/cli/get-started/overview) | Notion CLI (official) |

## Known limitations
- Notion indexing and auto-trigger skills are upcoming phases.
- Codex adapter behavior is unverified.
- Public distribution (many users) requires making the repo public + verification (Phase 3).

## License
Apache-2.0 (provisional — to be finalized before public release).
