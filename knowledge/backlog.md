# Backlog

- `templates/bunfig.toml` `minimumReleaseAgeExcludes` lists rapideditor-specific packages. Fine while every downstream project is rapideditor's; otherwise make it a placeholder.
- `templates/.gitignore` has `coverage/` under the `# Editor` heading.
- A `bun test` that checks the manifest: every version-table row has a per-file section, and every path exists under `templates/`. It could also check that the root copies match their templates.
- Pin each `/sync` run to one commit SHA instead of fetching from `main` file by file (see the caching quirk in `quirks.md`). That would also fill the local manifest's Canonical Commit column.
- Dependabot `cooldown` to match `bunfig.toml`'s `minimumReleaseAge`.
- Drop the legacy `templates/.github/prompts/sync.prompt.md` shim once no downstream project is still on the old Copilot prompt files (sync v2 and earlier).
- The suggest skill has TypeScript/Bun-specific categories. Its per-file instructions say to adapt them per project, but the template itself could be more generic.
- Skills live in this repo for now. Split them into a separate repo if this one gets unwieldy.
- Maybe: a hash column in the local manifest, so `/sync` can warn about hand edits since the last sync. Skipped so far; the Notes column covers deliberate edits.
