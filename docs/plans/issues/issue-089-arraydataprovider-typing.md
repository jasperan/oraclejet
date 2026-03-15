# Issue #89: ArrayDataProvider typing mismatch

**Priority: 1**

- Issue: https://github.com/oracle/oraclejet/issues/89
- Title: `Property 'prototype' is missing in type ArrayDataProvider...`
- Bucket: `code-fixable now`

## Why this is a first-wave target

The report includes a clear before/after trigger (`jest@29.2.0` added to a fresh TypeScript navbar template), and the failure mode is a clean TypeScript compile error. That makes it a good pi-autoresearch candidate.

## Minimal repro plan

1. Create a scratch TypeScript repro under a temp directory.
2. Install `typescript`, `jest@29.2.0`, and point module resolution at the local `@oracle/oraclejet` package in this repo.
3. Add the smallest TS file that constructs `ArrayDataProvider` with router state data and assigns it where a `DataProvider<...>` is expected.
4. Run `tsc --noEmit` and capture the exact failure.

## Candidate files

- `dist/types/ojarraydataprovider/index.d.ts`
- `dist/types/ojdataprovider/index.d.ts`
- `dist/types/index.d.ts`
- any other declaration file surfaced by the repro, including router-related types if they appear in the error chain

## Validation command

```bash
npx tsc --noEmit -p /tmp/oraclejet-issue-089/tsconfig.json
```

Success means the repro compiles cleanly with no `prototype` mismatch.

## Root-cause questions to answer before editing

- Is the mismatch caused by `export =` class typing versus interface import shape?
- Does the issue depend on a specific TS or Jest type package interaction?
- Is the failure caused by `ArrayDataProvider` declarations, or by how `DataProvider` is re-exported/imported?

## Current investigation note

A direct scratch-project repro against the current `20.0.0` package compiled cleanly, even with `jest@29.2.0` installed. That suggested one of 3 things:

1. the issue is already fixed in current JET,
2. the failure depends on older CLI-generated template code, or
3. the failure depends on a narrower `@types/node` / TS version combination than the first scratch repro used.

A follow-up matrix against packaged `20.0.0` found real type-environment sensitivity, but not yet the exact reported `prototype` mismatch:

- `typescript@4.8.4` + `@types/node@18.7.0` passed
- `typescript@4.8.4` + `@types/node@18.8.0` passed
- `typescript@4.8.4` + `@types/node@18.11.0` failed with an `AbortSignal` duplicate declaration
- `typescript@4.9.5` and `5.0.4` failed across the tested `@types/node` variants for the same `AbortSignal` conflict when `skipLibCheck` was disabled

So this looks more like an older-toolchain compatibility pocket than a current unconditional package typing bug.

## pi-autoresearch

**Maybe later.** It is still a good experiment target if we can pin down the exact older toolchain combination, but it is no longer the best first issue now that #70 has a cleaner live repro.
