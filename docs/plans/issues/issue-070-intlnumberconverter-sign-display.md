# Issue #70: IntlNumberConverter ignores `signDisplay`

- Issue: https://github.com/oracle/oraclejet/issues/70
- Bucket: `code-fixable now`

## Report summary

The issue says `oj.IntlNumberConverter` does not honor `signDisplay: 'always'`, while native `Intl.NumberFormat` does.

## Minimal repro plan

1. Build a tiny JS repro that formats the same numeric values with:
   - JET number converter
   - native `Intl.NumberFormat`
2. Use a small matrix of options:
   - `signDisplay: 'always'`
   - positive, negative, and zero values
3. Diff the outputs.

## Candidate files

- `dist/js/libs/oj/debug/ojconverter-nativenumber.js`
- `dist/js/libs/oj/debug_esm/ojconverter-nativenumber.js`
- minified bundle only after the debug variants are confirmed

## Validation command

```bash
node ./scripts/repros/issue-070-sign-display.mjs
```

Expected success condition: JET output matches `Intl.NumberFormat` for the repro cases.

## Root-cause questions

- Is `signDisplay` being dropped when native options are derived?
- Is the option preserved for some styles but not decimal formatting?
- Is there a formatting post-process that strips a leading `+`?

## pi-autoresearch

**Used successfully.** The issue turned out to have a deterministic repro and a narrow winning change: thread `signDisplay` through the resolved option shape, native formatter options, and public TS option types.

## Current status

- Branch pushed: `issue-70-sign-display`
- Fork URL: `https://github.com/jasperan/oraclejet/tree/issue-70-sign-display`
