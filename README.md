
# agent-practices

A canonical seed file repository for agent file scaffolding.

## How it works

1. Place common files here
2. In another project, save the `.github/prompts/sync.prompt.md` file and run `/sync` prompt
3. Your agent will look here for the "best" versions of the files and apply updates to the local copy.

The sync is version-aware — files are only updated when the canonical version is higher than the local copy.


## Repository layout

`templates/` is the single source of truth.

```sh
templates/            # ← everything under here will be synced to consumer projects
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
> Note that `.github/prompts/` is a symlink to `templates/.github/prompts/` so Copilot finds the prompts at the standard location without duplication.


## Sync Metadata

Every sync-ready file carries a trailing comment with metadata:
- `source`: canonical source URL (which points back here to this repo)
- `version`: version number, as an increasing integer

```markdown
<!-- sync: source=https://github.com/rapideditor/agent-practices/blob/main/templates/AGENTS.md version=1 -->
```

```sh
# sync: source=https://github.com/rapideditor/agent-practices/blob/main/templates/.gitignore version=1
```

The sync prompt treats a missing metadata comment as version `0` and only updates when the canonical version is strictly higher.

> [!TIP]
> Any file format that supports comments can be synced through this process!


### License

Available under the [ISC License](https://opensource.org/licenses/ISC). See [LICENSE.md](LICENSE.md).
