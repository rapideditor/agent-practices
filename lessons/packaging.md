# Packaging

**Node 20.19+ and 22.12+ can `require()` an ES module**, as long as it has no top-level `await`. An ESM-only package can point the `require` condition at its `.mjs` file. See the [ESM only](../playbooks/esm-only.md) playbook. (verified: Node 22, 2026-11)

**In `exports`, put `require` before `browser`.** A bundler resolving `require()` for a browser target matches conditions in order, and would otherwise pick an IIFE bundle that has no exports. (verified: 2026-11)

**Don't add `engines.node` to a library.** npm only warns on a mismatch (EBADENGINE), but Yarn 1 fails the install outright, even for a devDependency. publint suggests adding it; ignore that, and put the minimum version in the README. (verified: npm 10.9, Yarn 1.22, 2026-11)

**`devEngines` doesn't fix this.** npm 10.9+ enforces it only for the project you're working in, not for dependencies, and Bun ignores it. (verified: npm 10.9, Bun 1.4.4, 2026-11)

**arethetypeswrong still treats `require` → ESM as an error.** For an ESM-only package, use `attw --profile esm-only` rather than `--ignore-rules`, which can hide real problems. (verified: @arethetypeswrong/cli 0.18.5, 2026-11)

**`attw --pack` shells out to `npm pack`.** In a Bun-only setup, pack with `bun pm pack` and pass the tarball. See the [Package checks](../playbooks/package-checks.md) playbook. (verified: @arethetypeswrong/cli 0.18.5, 2026-11)
