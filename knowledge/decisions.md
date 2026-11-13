# Decisions

## Sync metadata lives only in the manifests

Versions and per-file instructions live in [`templates/.sync.manifest.md`](../templates/.sync.manifest.md), never in the template files themselves. Downstream projects keep their own `.sync.manifest.md` at the repo root, which records what `/sync` last wrote. A missing row means version 0, and `/sync` updates a file only when the canonical version is strictly higher.

Consequence: anything that must happen on *every* run, not just when a file's version changes, has to live in the sync skill itself. The license year check was the first case. It used to live only in the `LICENSE.md` per-file rule, so it never ran again once a project had that version. It now runs in Phase 3 of the sync skill (v4, Nov 2026).

## `/sync` runs in three phases

This avoids the "rewrite my own instructions mid-run" problem.

1. **Bootstrap.** Update the sync skill and `AGENTS.md` if their canonical versions are higher. If the sync skill changed, stop and ask the user to re-run, because the skill in memory is now stale.
2. **Full sync.** For each file in the source manifest: check versions, fetch, substitute project-specific values, write with file-edit tools, update the local manifest row.
3. **Hygiene.** Check `package.json` `license` and `repository`, check the license year (every run), and flag orphaned files.

The critical safety rules (no `sed`, preserve comments, use file-edit tools) are written into the sync skill itself, so a fresh repo with an empty `AGENTS.md` still syncs safely.

Bumping the sync skill makes every downstream project's next `/sync` stop after Phase 1. So migration steps go in per-file instructions where possible (e.g. moving `SCRATCHPAD.md` lives under `knowledge/README.md`), not in the sync skill.

## What's shared and what stays in the project

| Shared (goes in `templates/`) | Project-specific (stays local) |
|---|---|
| Generic workflow steps | Project name, repo URL, license |
| Tool invocation patterns | Language/runtime specifics |
| Commit message format | Release tooling (npm vs. cargo vs. etc.) |
| Skill structure and conventions | Layer names, file paths |

## Synced files are starting points (Nov 2026)

Downstream copies are expected to drift. The local manifest's Notes column records deliberate differences, so `/sync` keeps them instead of flagging them again on every version bump. ([#6](https://github.com/rapideditor/agent-practices/pull/6))

## Shared playbooks and lessons are fetched, not synced (Nov 2026)

`playbooks/` and `lessons/` are read when relevant, and only the entries that apply. Syncing them would copy every lesson into every project and make each update a version bump. The synced `AGENTS.md` points at their indexes, and `/reflect` suggests additions without editing them. ([#7](https://github.com/rapideditor/agent-practices/pull/7))

## Project knowledge is committed and public (Nov 2026)

Each project keeps its notes in a committed `knowledge/` folder, replacing the gitignored `SCRATCHPAD.md`. Rapid ran this for months (as `.scratchpad/`) and it worked well: everyone starts with the same context, and the notes are reviewed like any other change.
- `knowledge/` rather than `.scratchpad/`: a dot folder reads as hidden tooling, and this is meant to be read.
- Private notes go in gitignored `PRIVATE.*` files, in any folder. One pattern covers `.md`, `.json` and anything later.
- `design/` stays a separate, optional folder: design docs are longer-lived, and written for people as much as agents.
- No `.gitattributes` line for `knowledge/`: `*.md linguist-documentation` already covers it.
- Size budgets are in KB, not lines. Rapid's `completed.md` was 130 lines but 46 KB, because each entry was one long paragraph. The numbers: `current.md` is read every session, so about 8 KB (2k tokens). Other files are read on demand, so about 40 KB (10k tokens) each, which Rapid's larger files showed is still usable in one read. About 100 KB is the hard ceiling, since Claude Code's Read tool refuses files over about 25k tokens. Past 40 KB, split by topic rather than only pruning, so each read stays targeted.
