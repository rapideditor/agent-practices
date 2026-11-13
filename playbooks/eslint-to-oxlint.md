# ESLint → oxlint

Replace ESLint and typescript-eslint with [oxlint](https://oxc.rs/docs/guide/usage/linter.html), keeping the same rules wherever oxlint has them.

**Applies when:** `eslint` is in `devDependencies`. Do this before [TypeScript 7](typescript-7.md), because typescript-eslint doesn't support TypeScript 7 yet.

**Last verified:** oxlint 1.92.0, Bun 1.4.4, 2026-11 ([node-diff3#96](https://github.com/bhousel/node-diff3/pull/96))

## Why

- oxlint is one standalone package and doesn't use the TypeScript compiler. In node-diff3 the dependency tree went from 104 packages to 25, which cleared all 11 open `bun audit` advisories (they all came in through ESLint's dependencies).
- `check:lint` went from about 1 s to about 20 ms.
- It uses ESLint's rule names, so most of the config carries over directly.

## Steps

1. Read `eslint.config.js`, and note which shared configs it extends (`@eslint/js` recommended, typescript-eslint recommended/stylistic). Those turn on rules that aren't written out in the file.
2. `bun remove eslint @eslint/js globals typescript-eslint` (plus any other ESLint plugins), then `bun add -d oxlint`.
3. Write `.oxlintrc.json`:
   - `"$schema": "./node_modules/oxlint/configuration_schema.json"`.
   - `"plugins": ["typescript"]` in a TypeScript project. Core ESLint rules don't need a plugin entry.
   - `"categories": { "correctness": "error" }`, then each rule from the old config with the same severity and options. `oxlint --rules` lists what exists.
   - Rules that the shared configs turned on, but that aren't in oxlint's `correctness` category, must be listed explicitly. Otherwise they silently turn off.
   - Rule names: core rules use the plain name (`no-unused-vars`). TypeScript rules use the `typescript/` prefix (`typescript/no-explicit-any`), not `@typescript-eslint/`. Where typescript-eslint had its own version of a core rule (`@typescript-eslint/no-unused-vars`), use oxlint's core rule with the same options.
   - Environments and globals: `"env": { "browser": true }` at the top, and `overrides` for scripts and tests (e.g. `"globals": { "Bun": "readonly" }`, `"no-console": "off"`).
4. Leave out what doesn't carry over, and list it in the PR:
   - Formatting rules (`semi`, `brace-style`, `*-spacing`, `no-trailing-spaces`, `linebreak-style`, …). oxlint doesn't implement them, and ESLint has deprecated them. `.editorconfig` and `.gitattributes` cover whitespace and line endings.
   - Rules oxlint doesn't have. In node-diff3 these were `dot-notation`, `no-invalid-this`, `no-process-env`, `no-undef-init` and `require-atomic-updates`. Check whether they ever flagged anything.
   - Rules deprecated in ESLint (`no-return-await`, `no-catch-shadow`) and old aliases (`no-native-reassign` is now `no-global-assign`).
5. Point `check:lint` at oxlint (e.g. `oxlint ./scripts ./src ./test`) and delete `eslint.config.js`.

## Gotchas

- Diagnostics for core rules still print as `eslint(rule-name)`. That's oxlint's label, not a sign ESLint is still installed.
- Existing `// eslint-disable-next-line` comments keep working in oxlint, so they can stay.
- Rules like `no-dupe-args`, `no-octal` and `no-octal-escape` describe code that can't parse as an ES module, so dropping them loses nothing in an ESM project.

## Verify

- `oxlint --print-config` shows the effective config. Compare its rule list with the old ESLint setup, to catch rules that silently turned off.
- `bun run check` passes, and `bun audit` is clean (or better than before).
- `grep -rn eslint` finds nothing left except comments, the changelog and `eslint-disable` comments.
