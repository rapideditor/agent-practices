# ESM only

Stop shipping a CommonJS build, and let `require()` load the ES module build instead.

**Applies when:** a published package still builds a `.cjs` file (or points `main` / the `require` condition at one), and the maintainer is ready for a major release.

**Last verified:** Node 22, Bun 1.4.4, 2026-11 ([node-diff3#98](https://github.com/bhousel/node-diff3/pull/98))

## Why

- Node 18 and 20 are past end of life. Node 20.19+ and 22.12+ can `require()` an ES module without a flag, so a separate CommonJS build is no longer needed.
- One build means one set of output and declarations, and no dual-package hazards.

## Steps

1. Remove the CommonJS build from the build script.
2. Set `"type": "module"` and write `exports` in this order:
   ```json
   "exports": {
     "types": "./dist/ts/index.d.ts",
     "bun": "./src/index.ts",
     "import": "./dist/js/index.mjs",
     "require": "./dist/js/index.mjs",
     "browser": "./dist/js/index.iife.js",
     "default": "./dist/js/index.mjs"
   }
   ```
   - `types` first, so TypeScript always finds it.
   - `bun` is optional. It lets Bun import the TypeScript source directly, so `src/` must be in `files`.
   - `require` points at the same `.mjs` as `import`, and must come before `browser` (see Gotchas).
   - `browser` is only for packages that also ship an IIFE bundle for `<script>` tags. In practice it's never chosen over `import`; it mostly tells readers where the IIFE is.
3. Don't add `engines.node`. npm only warns on a mismatch, but Yarn 1 fails the install outright, even when the package is only a devDependency. Put the minimum Node version in the README instead.
4. Make it a major version. List in the changelog: CommonJS build removed, `require()` needs Node 20.19+ or 22.12+, and any moved files (e.g. a CDN path for the IIFE).
5. Add [package checks](package-checks.md) if the project doesn't have them.

## Gotchas

- **`require` before `browser`.** A bundler resolving a `require()` call for a browser target matches conditions in order. If `browser` comes first, it picks the IIFE, which has no exports.
- **No top-level `await`** in the ES module. `require()` can't load a module that uses it.
- **arethetypeswrong** still reports `require` → ESM as an error under its default profile. Run it with `--profile esm-only`.
- **CDN users** load files by path (e.g. `dist/diff3.iife.min.js`). If the layout changes, pages that load `@latest` break, so call it out as a breaking change.

## Verify

- `bun run package` (publint and arethetypeswrong) passes.
- In a scratch project with the package linked or packed in:
  - `import` and `require()` both work on Node 22.
  - Types resolve under `moduleResolution` `bundler`, and under `nodenext` from both an ESM and a CommonJS file.
  - Bun loads the TypeScript source, if there's a `bun` condition.
