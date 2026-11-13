# TypeScript 7

Upgrade `typescript` to 7.x, the native compiler.

**Applies when:** `typescript` in `devDependencies` is below 7. If the project uses typescript-eslint, do [ESLint → oxlint](eslint-to-oxlint.md) first: typescript-eslint doesn't support TypeScript 7 yet.

**Last verified:** TypeScript 7.0.2, Bun 1.4.4, 2026-11 ([node-diff3#93](https://github.com/bhousel/node-diff3/pull/93), [node-diff3#98](https://github.com/bhousel/node-diff3/pull/98))

## Why

Much faster type-checking and builds, and it's where TypeScript development happens now. Dependabot will propose the bump anyway.

## Steps

1. `bun add -d typescript@^7`.
2. In `tsconfig.json`, set `"types"` to the runtime's types, e.g. `"types": ["bun"]`, and check that `@types/bun` is in `devDependencies`. TypeScript 7 no longer loads `@types/*` packages automatically.
3. Run `tsc --noEmit`. TypeScript 7 rejects these options outright, with error TS5102 or TS5108 naming each one:
   - `moduleResolution`: `node` / `node10` and `classic` (use `bundler` or `nodenext`)
   - `target: "es5"`, `module`: `amd` / `umd` / `system`
   - `baseUrl` (use `paths`), `outFile`, `downlevelIteration`
   - `esModuleInterop: false`, `allowSyntheticDefaultImports: false`, `alwaysStrict: false`
   - Options removed earlier (`importsNotUsedAsValues`, `preserveValueImports`, `suppressImplicitAnyIndexErrors`, `keyofStringsOnly`, `charset`, `out`) are now unknown options.
4. If `tsconfig.json` didn't set `strict`, expect new errors: it's on by default now.
5. Run the build and diff any generated `.d.ts` files against the previous build. If the build writes files (`outDir` or `declarationDir`) from sources in a subfolder like `src/`, set `"rootDir"` to that folder (e.g. `"rootDir": "src"`). Without it the build fails with error TS5011, which `tsc --noEmit` doesn't report. The output layout stays the same.

## Gotchas

- **The `Bun` global can type-check by accident.** In node-diff3 it worked only because one script imported from `'bun'`, which pulled the types in for every file. Checked on its own, `scripts/build_js.ts` failed with "Cannot find name 'Bun'" until `types` was set.
- **New defaults** (seen with only `module: "Preserve"` set): `strict` and `noUncheckedSideEffectImports` are on, `bundler` resolution and `resolveJsonModule` are implied, and ES2025 library APIs type-check. Leave existing explicit settings in place: they still document intent, and TypeScript 5 doesn't share these defaults.
- **Declarations generated from JavaScript get looser.** With `allowJs` + `declaration`, TypeScript 7 inferred `any[]` for most of node-diff3's return types. If the project ships declarations generated from JS, compare them, or move the source to TypeScript.

## Verify

- `bun run check` and `bun run build` pass.
- Type-check each script and test on its own, or grep for `Bun.` in files that don't import from `'bun'`, to catch globals that only resolved by accident.
- Generated `.d.ts` files are unchanged, or the changes are intended.
