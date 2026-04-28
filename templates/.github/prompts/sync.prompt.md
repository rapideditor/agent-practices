---
description: Sync scaffold files in this project against the canonical agent-practices repo
---

You are doing a scaffold sync against the canonical source repo: **https://github.com/rapideditor/agent-practices**

For each file discovered in the canonical `templates/` directory, fetch it, compare it to the local version, and **create or update the local file** — substituting any source-specific details with this project's equivalent. The goal is to carry the source's structure and generic content forward while keeping this project's identity intact.

## Setup

Before doing anything else:
1. Read this project's `package.json` (or equivalent manifest) to learn: project name, description, repo URL, license, author(s), and language/runtime.
2. The canonical raw content base URL is: `https://raw.githubusercontent.com/rapideditor/agent-practices/main`

---

## How to find files to sync

Fetch the repository file tree to discover what needs syncing:
```
https://api.github.com/repos/rapideditor/agent-practices/git/trees/main?recursive=1
```
Filter to items where `type` is `blob` and `path` starts with `templates/`. Strip the `templates/` prefix to get the destination path in this project.

**This file listing is the complete manifest** — no hardcoded list needed. When new files are added to `templates/` in the canonical repo, they'll be picked up automatically on the next `/sync`.

---

## Version checking

Each synced file carries a version marker in a trailing comment:
- **Markdown and prompt files**: `<!-- sync: source=... version=N -->`
- **Config files** (`.gitignore`, `.gitattributes`, etc.): `# sync: source=... version=N`

Treat a missing marker as version `0`. **Skip the file if the local version is equal to or greater than the canonical version.** Only update when the canonical version is strictly higher.

---

## Per-file guidance

Most files can be synced as-is after substituting project-specific values. Exceptions:

- `.github/prompts/release.prompt.md` — also adapt the release workflow to this project's process (don't just string-replace; rethink steps if the release tooling differs)
- `AGENTS.md` — preserve any local sections that have no counterpart in the template
- `CONTRIBUTING.md` — adapt tooling and runtime references; keep the template's structural sections
- `.gitattributes` — adapt file-type entries to this project's actual file types; add missing entries without removing local-only ones
- `.gitignore` — merge only: add entries absent locally; never remove local-only entries
- `CHANGELOG.md` — **create only if missing**; never overwrite an existing changelog

---

## Steps

For each file discovered in the manifest:

1. Fetch the raw canonical content from `{raw_base_url}/templates/{file_path}`
2. Check whether the file exists locally at `{file_path}`
3. Version check (see above) — skip if local is already at canonical version
4. Identify all source-specific values: repo name, org, URLs, package names, author names, version numbers, tool names — anything that belongs to the canonical project rather than the template structure
5. Replace each source-specific value with the corresponding value from this project (from `package.json` or existing local files)
6. Create or update the local file with the adapted content

---

## How to report

After processing all files, produce a summary table:

| File | Status | Notes |
|------|--------|-------|
| `.github/prompts/commit.prompt.md` | ✅ In sync / 🔄 Updated / ✨ Created / ⏭️ Skipped | … |
| … | … | … |

For each **🔄 Updated** or **✨ Created** file: briefly describe what was substituted or what structural changes were adopted.

For **⏭️ Skipped** files: one line explaining why (e.g. "already at canonical version" or "CHANGELOG already exists").

For files that were **✅ In sync**: one line is enough.

<!-- sync: source=https://github.com/rapideditor/agent-practices/blob/main/templates/.github/prompts/sync.prompt.md version=1 -->
