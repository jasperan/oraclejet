# Issue #59: `ojinputtext` leaves extra whitespace in `aria-describedby`

- Issue: https://github.com/oracle/oraclejet/issues/59
- Bucket: `code-fixable now`

## Report summary

The issue body points to a specific fix: trim the concatenated `aria-describedby` value before writing it back. The report says the extra whitespace breaks jquery validation after repeated date interactions.

## Minimal repro plan

1. Recreate the input interaction that appends helper IDs repeatedly.
2. Capture `aria-describedby` before and after the helper text is added.
3. Confirm that the value accumulates leading/trailing whitespace in the current build.
4. Apply `trim()` and verify the attribute stays normalized.

## Candidate files

- `dist/js/libs/oj/debug/ojinputtext.js`
- `dist/js/libs/oj/debug_esm/ojinputtext.js`
- `dist/js/libs/oj/min/ojinputtext.js`

## Validation command

At minimum, run a targeted repro that prints the resulting `aria-describedby` string and fails if it contains leading/trailing whitespace.

## Root-cause questions

- Is trimming enough, or can duplicate helper IDs also accumulate?
- Does the bug exist in both debug and ESM variants, with minified output updated afterward?
- Can we turn the report into a tiny repeatable regression script instead of a manual browser check?

## pi-autoresearch

**Probably not.** The candidate fix is already narrow. TDD plus a small repro should be faster.
