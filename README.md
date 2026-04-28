
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


## Sync Metadata Comment

Each sync-able file carries a trailing comment at the end of the file.  The comment starts with the string 'sync:',
followed by attributes:
- `version`: version number, as an increasing integer
- `source`: canonical source URL (which points back here to this repo)
- `instructions`: optional instructions to apply when syncing the file

For example:

```markdown
<!-- sync:
version=1
source=https://github.com/rapideditor/agent-practices/blob/main/templates/AGENTS.md
instructions="preserve any local sections that have no counterpart in the template"
-->
```

```sh
# sync:
# version=1
# source=https://github.com/rapideditor/agent-practices/blob/main/templates/.gitignore
# instructions="merge only: add entries absent locally; never remove local-only entries"
```

The sync prompt treats an existing file with no metadata comment as version `0`.

> [!TIP]
> Any file format that supports comments can be synced through this process!


### License

Available under the [ISC License](https://opensource.org/licenses/ISC). See [LICENSE.md](LICENSE.md).
