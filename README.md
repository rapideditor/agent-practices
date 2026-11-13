
# agent-practices

Shared agent guidelines and scaffold files that stay in sync across projects.

## How it works

1. Common files live under `templates/` in this repo.
2. The file [`templates/.sync.manifest.md`](templates/.sync.manifest.md) lists every syncable file, its current canonical version, and any per-file instructions.
3. In a downstream project, save [`templates/.claude/skills/sync/SKILL.md`](templates/.claude/skills/sync/SKILL.md) as `.claude/skills/sync/SKILL.md` and run `/sync`.
4. The skill fetches the manifest, compares versions against the project's local `.sync.manifest.md`, and applies updates.

The sync is version-aware — files are only updated when the canonical version listed in the source manifest is higher than the version recorded in the project's local manifest.


## Skills

Each skill lives at [`templates/.claude/skills/<name>/SKILL.md`](templates/.claude/skills/) and syncs to downstream projects like any other file. Skills marked "you" only run when you type the command; the others can also be started by the agent when the task matches.

| Skill | What it does | Edits files? | Invoked by |
|-------|--------------|--------------|------------|
| [`/bughunt`](templates/.claude/skills/bughunt/SKILL.md) | Hunts for logic bugs in the current batch of work, using parallel reviewers and randomized invariant tests | No, report only | you |
| [`/checkup`](templates/.claude/skills/checkup/SKILL.md) | Checks a repo after time away: baseline build and tests, security alerts, outdated dependencies, sync drift, and what a newer model would do differently | No, report only | you or the agent |
| [`/commit`](templates/.claude/skills/commit/SKILL.md) | Stages and commits all changes with a summarized message (doesn't push) | Commits | you |
| [`/reflect`](templates/.claude/skills/reflect/SKILL.md) | Brings docs, agent instructions, and working notes up to date with recent work | Yes, docs | you or the agent |
| [`/release`](templates/.claude/skills/release/SKILL.md) | Prepares a release: validates the version, updates `CHANGELOG.md`, bumps `package.json` | Yes | you |
| [`/suggest`](templates/.claude/skills/suggest/SKILL.md) | Reviews the codebase and suggests improvements as a numbered list | No, report only | you or the agent |
| [`/sync`](templates/.claude/skills/sync/SKILL.md) | Syncs scaffold files from this repo into a downstream project | Yes | you |
| [`/triage`](templates/.claude/skills/triage/SKILL.md) | Triages open issues and PRs: already fixed, duplicates, stale, ready to merge, quick wins, with draft replies | No, report only | you or the agent |


## Shared knowledge

Alongside the synced templates, this repo keeps knowledge that applies across projects. It's never synced: agents fetch it when it's relevant, and the synced `AGENTS.md` tells them where to find it.

- [`playbooks/`](playbooks/README.md): step-by-step guides for one-time upgrades, like switching from ESLint to oxlint or moving to TypeScript 7. `/checkup` lists the ones that apply to a project.
- [`lessons/`](lessons/README.md): gotchas with the tools we use, grouped by topic (Bun, TypeScript, packaging, git, shell).

Each folder's `README.md` is the index, plus the format for new entries. New playbooks and lessons come from real work in downstream projects: `/reflect` suggests additions, and they land here as PRs.

So there are three layers:

| Layer | Where | How it gets to a project |
|-------|-------|--------------------------|
| Templates: steady-state files and skills | `templates/` | Synced by `/sync`, versioned in the manifest |
| Shared knowledge: playbooks and lessons | `playbooks/`, `lessons/` | Fetched on demand, not versioned |
| Project memory | Each project's own notes (see its `AGENTS.md`) | Stays in the project |


## Repository layout

`templates/` is the single source of truth for synced files.

```sh
playbooks/            # ← one-time upgrade guides, fetched on demand (not synced)
  README.md           # ← index and format
  …
lessons/              # ← cross-project gotchas by topic, fetched on demand (not synced)
  README.md           # ← index and format
  …
templates/            # ← everything under here will be synced to consumer projects
  .sync.manifest.md   # ← THE source of truth: file list, versions, instructions
  .gitattributes
  .gitignore
  AGENTS.md
  CHANGELOG.md
  CONTRIBUTING.md
  …
  .claude/
    skills/           # ← Agent Skills, found by Claude Code and VS Code Copilot
      sync/
        SKILL.md
      …
  .github/
    prompts/
      sync.prompt.md  # ← legacy shim that moves older projects over to the skills
```

> [!NOTE]
> `.claude/skills/` at the repo root is a symlink to `templates/.claude/skills/`, so agents working in this repo find the skills at the standard location without duplication.


## Sync metadata

All sync metadata — file paths, versions, per-file instructions — lives in [`templates/.sync.manifest.md`](templates/.sync.manifest.md). Template files themselves carry no sync metadata; they're clean copies ready to drop into a downstream project.

Downstream projects keep a `.sync.manifest.md` at their repo root that records which versions of which files they've received. The `/sync` skill updates this file as part of every run; no other file is modified for bookkeeping.

Synced files are starting points, and downstream copies are expected to drift. The local manifest's Notes column records deliberate differences (e.g. a library adding `isolatedDeclarations` to its `tsconfig.json`), so later syncs keep them instead of flagging them again.


### License

Available under the [ISC License](https://opensource.org/licenses/ISC). See [LICENSE.md](LICENSE.md).
