# Completed

## November 2026

- **Project knowledge:** projects keep public notes in a committed `knowledge/` folder and private ones in gitignored `PRIVATE.*` files, replacing `SCRATCHPAD.md`. `/sync` moves existing projects over, and `/reflect` prunes the notes. This repo moved over first.
- **Shared playbooks and lessons:** upgrade guides and tool gotchas that agents fetch when relevant, seeded from the node-diff3 upgrades (oxlint, TypeScript 7, ESM only, package checks). `/checkup` lists the playbooks that apply.
- **Notes column:** local manifests record deliberate drift from the templates, and the source manifest now defines the local manifest format. The `tsconfig.json` template gained `"types": ["bun"]` for TypeScript 7.
- **Sync fixes:** `/sync` replaces a broken `* text=` line in `.gitattributes`, and CI installs with `--frozen-lockfile`.
- **New skills:** `/bughunt`, `/checkup` and `/triage`. `/commit` stages and commits only, and takes instructions as arguments.
- **License year check** moved into the sync skill's Phase 3, so it runs on every sync.

## October 2026

- **Prompt files became Agent Skills** in `.claude/skills/<name>/SKILL.md`, which both Claude Code and VS Code Copilot find. A legacy shim moves older projects over.

## April 2026

- **Started the repo:** templates synced into downstream projects by a `/sync` prompt.
- **Sync redesign:** metadata moved out of the template files into manifests, and sync split into three phases.
