# TypeScript

**TypeScript 7 no longer loads `@types/*` packages automatically.** List them in `"types"` (e.g. `["bun"]`). Without it, a global like `Bun` may still type-check, but only because some file happens to import from `'bun'`. (verified: TypeScript 7.0.2, 2026-11)

**TypeScript 7 turns `strict` on by default**, along with `noUncheckedSideEffectImports`. `module: "Preserve"` also implies `bundler` resolution and `resolveJsonModule`. Keep explicit settings anyway: TypeScript 5 doesn't share these defaults. (verified: TypeScript 7.0.2, 2026-11)

**`noImplicitAny` is part of `strict`.** Don't add it separately. (verified: TypeScript 7.0.2, 2026-11)

**`isolatedDeclarations` requires `declaration`, and then every exported function and value needs an explicit type** (error TS9010 for an unannotated exported variable). Worth it for a library, since its public API can't change by accident. In an app it's just noise. (verified: TypeScript 7.0.2, 2026-11)

**Under `noUncheckedIndexedAccess`, tests that compare whole results are easier than tests that index into them.** `assert.deepStrictEqual(result, expected)` needs no `!` or guards, and also checks fields a piecemeal test would miss. (verified: TypeScript 7.0.2, 2026-11)

**To accept "an array of `T`, or a string to split when `T` is `string`", use a conditional type:** `readonly T[] | (T extends string ? string : never)`. Callers with non-string `T` can't pass a string by mistake. (verified: TypeScript 7.0.2, 2026-11)
