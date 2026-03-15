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

## pi-autoresearch

**Yes.** This has a crisp pass/fail command, small file scope, and likely multiple declaration-shape experiments worth trying.
