
# agent-practices

Shared agent guidelines and scaffold files that stay in sync across projects.

## How it works

1. Common files live under `templates/` in this repo.
2. The file [`templates/.sync.manifest.md`](templates/.sync.manifest.md) lists every syncable file, its current canonical version, and any per-file instructions.
3. In a downstream project, save [`templates/.github/prompts/sync.prompt.md`](templates/.github/prompts/sync.prompt.md) as `.github/prompts/sync.prompt.md` and run `/sync`.
4. The prompt fetches the manifest, compares versions against the project's local `.sync.manifest.md`, and applies updates.

The sync is version-aware — files are only updated when the canonical version listed in the source manifest is higher than the version recorded in the project's local manifest.


## Repository layout

`templates/` is the single source of truth.

```sh
templates/            # ← everything under here will be synced to consumer projects
  .sync.manifest.md   # ← THE source of truth: file list, versions, instructions
  .gitattributes
  .gitignore
  AGENTS.md
  CHANGELOG.md
  CONTRIBUTING.md
  …
  .github/
    prompts/
      sync.prompt.md
      …
```

> [!NOTE]
> `.github/prompts/` at the repo root is a symlink to `templates/.github/prompts/` so Copilot finds the prompts at the standard location without duplication.


## Sync metadata

All sync metadata — file paths, versions, per-file instructions — lives in [`templates/.sync.manifest.md`](templates/.sync.manifest.md). Template files themselves carry no sync metadata; they're clean copies ready to drop into a downstream project.

Downstream projects keep a `.sync.manifest.md` at their repo root that records which versions of which files they've received. The `/sync` prompt updates this file as part of every run; no other file is modified for bookkeeping.


### License

Available under the [ISC License](https://opensource.org/licenses/ISC). See [LICENSE.md](LICENSE.md).
