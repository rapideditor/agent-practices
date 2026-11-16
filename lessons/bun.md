# Bun

**With `[run] bun = true` in `bunfig.toml`, package bins run under Bun even when their shebang is `#!/usr/bin/env node`.** Node-only CLIs like publint and arethetypeswrong work with no Node installed. (verified: Bun 1.4.4, 2026-11)

**With `[run] shell = "bun"`, `package.json` scripts run in Bun's own shell on every platform**, so globs and `&&` work on Windows too. The convention is still single-purpose scripts combined with `bun run --sequential` or `--parallel`, which reads better and fails clearly. (verified: Bun 1.4.4, 2026-11)

**`bun pm pack` won't combine `--filename` with `--destination`, and `--filename` doesn't create missing directories.** Use `--destination ./build` and refer to the tarball with a glob (`./build/*.tgz`). (verified: Bun 1.4.4, 2026-11)

**Bun ignores both `engines` and `devEngines`**, with no warning. `engines.bun` is still useful as a signal to people and other tools. (verified: Bun 1.4.4, 2026-11)

**`Bun.file().json()` returns `any`**, so nothing that touches the result is type-checked, and the compiler never says so. For a fixed file (`package.json`, a JSON schema), import it as a typed module: `import pkg from '../package.json' with { type: 'json' }`. For data read at runtime, type the result as `unknown` and narrow it with a type guard or schema validation. `JSON.parse()` and `JSON5.parse()` have the same problem. (verified: Bun 1.4.4, @types/bun 1.4.4, TypeScript 7.0.2, 2026-11)

**`bun:assert` has no type declarations.** In TypeScript tests, use `node:assert`, which Bun supports. (verified: Bun 1.4.4, @types/bun 1.4.4, 2026-11)

**`bun:util` isn't a documented module and has no type declarations**, though Bun resolves it at runtime. Use `node:util`. (verified: Bun 1.4.4, @types/bun 1.4.4, 2026-11)

**For a Node library build, `Bun.build()` needs `packages: 'external'`.** The default, `packages: 'bundle'`, inlines every dependency, which suits a browser IIFE but not a library whose users install its dependencies themselves. `'external'` keeps bare specifiers as imports and still bundles relative ones; `external: ['*']` does the same more bluntly. Relative JSON imports are inlined either way. (verified: Bun 1.4.4, 2026-11)

**CI should run `bun install --frozen-lockfile`.** It fails when `bun.lock` is out of step with `package.json`, instead of quietly rewriting the lockfile. (verified: Bun 1.4.4, 2026-11)
