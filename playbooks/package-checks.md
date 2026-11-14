# Package checks

Lint the packed tarball with [publint](https://publint.dev) and [arethetypeswrong](https://arethetypeswrong.github.io) before publishing, without needing Node or npm.

**Applies when:** the project publishes a package to npm and doesn't already run these checks. Projects that already run them should still do step 4.

**Last verified:** publint 0.3.25, @arethetypeswrong/cli 0.18.5, Bun 1.4.4, 2026-11 ([node-diff3 cbf33db](https://github.com/bhousel/node-diff3/commit/cbf33db), [country-coder#180](https://github.com/rapideditor/country-coder/pull/180), [location-conflation#86](https://github.com/rapideditor/location-conflation/pull/86))

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
4. Check that every package the published `.d.ts` files import from is in `dependencies` (or `peerDependencies`), not `devDependencies`. List them with (adjust `dist` to wherever the build writes declarations):
   ```sh
   grep -rhoE "from ['\"][^./'\"][^'\"]*['\"]" dist --include='*.d.ts' | sort -u
   ```
   For a package without its own types, the dependency is its `@types` package: `from 'geojson'` needs `@types/geojson`.

   The grep only finds imports. Also check that the `.d.ts` files *import* every type they use: some `@types` packages declare a global namespace (`@types/geojson` declares `GeoJSON`), and a `.d.ts` that writes `GeoJSON.Feature` without importing it only works when those types happen to be loaded. TypeScript 7 consumers don't load `@types/*` automatically, so for them it fails. Find candidates with `grep -rln 'GeoJSON\.' dist --include='*.d.ts'`, and fix them in the source with `import type * as GeoJSON from 'geojson'`.

## Gotchas

- **`attw --pack` runs `npm pack`**, so it needs npm. Pack with `bun pm pack` and pass the tarball instead.
- **`bun pm pack`** won't combine `--filename` with `--destination`, and `--filename` doesn't create missing directories. Use `--destination` and a glob.
- **`--ignore-scripts`** keeps packing from running lifecycle scripts like `prepack`; the build has already run.
- **No Node needed.** With `[run] bun = true` in `bunfig.toml`, both tools run under Bun even though their bins start with `#!/usr/bin/env node`. With `[run] shell = "bun"`, the `./build/*.tgz` glob works on Windows too.
- **Don't use `attw --ignore-rules`** to quiet an error. It shows green while hiding real problems; pick the right profile instead. attw still prints a line about the rules a profile ignores, which is expected.
- **Neither tool checks the types' own dependencies.** With `@types/geojson` only in `devDependencies`, publint and attw both pass, but users get "Cannot find module 'geojson'". With `skipLibCheck` on, a common default, those types silently become `any` instead. A scratch-project test misses it too if the scratch project already has the package installed. This happened in both location-conflation and country-coder.
- **`publint --level warning`** hides suggestions, including one to add `engines.node`. For ESM-only packages, that suggestion is deliberately not followed (see [ESM only](esm-only.md)).

## Verify

- `bun run package` passes, and `bun run all` still passes from a clean checkout.
- Every package in the step 4 list is in `dependencies` or `peerDependencies`.
- In a scratch project, install the packed tarball (`bun add ./path/to/pkg.tgz typescript`) and type-check a file that imports the package, with `"types": []` and `"skipLibCheck": false`. This catches types that only resolve by accident, which publint and attw don't.
