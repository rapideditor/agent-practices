# Package checks

Lint the packed tarball with [publint](https://publint.dev) and [arethetypeswrong](https://arethetypeswrong.github.io) before publishing, without needing Node or npm.

**Applies when:** the project publishes a package to npm and doesn't already run these checks.

**Last verified:** publint 0.3.25, @arethetypeswrong/cli 0.18.5, Bun 1.4.4, 2026-11 ([node-diff3 cbf33db](https://github.com/bhousel/node-diff3/commit/cbf33db))

## Why

They catch `exports` that point at missing files, types that don't resolve for some consumers, and wrong module formats. Checking the tarball tests what users actually install, not the working tree.

## Steps

1. `bun add -d publint @arethetypeswrong/cli`.
2. Add single-purpose scripts, combined with `bun run --sequential` (the project convention, rather than `&&`):
   ```json
   "all": "bun run --sequential clean check build test package",
   "package": "bun run --sequential package:pack package:publint package:attw",
   "package:pack": "bun pm pack --quiet --ignore-scripts --destination ./build",
   "package:publint": "publint --level warning ./build/*.tgz",
   "package:attw": "attw --profile esm-only --no-summary ./build/*.tgz"
   ```
   Use `--profile esm-only` only for an [ESM-only](esm-only.md) package; a dual package uses the default profile.
3. Add `./build` to the folders `scripts/clean.ts` removes, and `build/` to `.gitignore`.

## Gotchas

- **`attw --pack` runs `npm pack`**, so it needs npm. Pack with `bun pm pack` and pass the tarball instead.
- **`bun pm pack`** won't combine `--filename` with `--destination`, and `--filename` doesn't create missing directories. Use `--destination` and a glob.
- **`--ignore-scripts`** keeps packing from running lifecycle scripts like `prepack`; the build has already run.
- **No Node needed.** With `[run] bun = true` in `bunfig.toml`, both tools run under Bun even though their bins start with `#!/usr/bin/env node`. With `[run] shell = "bun"`, the `./build/*.tgz` glob works on Windows too.
- **Don't use `attw --ignore-rules`** to quiet an error. It shows green while hiding real problems; pick the right profile instead. attw still prints a line about the rules a profile ignores, which is expected.
- **`publint --level warning`** hides suggestions, including one to add `engines.node`. For ESM-only packages, that suggestion is deliberately not followed (see [ESM only](esm-only.md)).

## Verify

- `bun run package` passes, and `bun run all` still passes from a clean checkout.
