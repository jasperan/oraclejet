# Issue #64: Nested `oj-form-layout` loses default user-assistance-density behavior

- Issue: https://github.com/oracle/oraclejet/issues/64
- Bucket: `code-fixable now`

## Report summary

A nested `oj-form-layout` collapses spacing unless `user-assistance-density='efficient'` is explicitly set, even though `efficient` is the documented default.

## Minimal repro plan

1. Recreate the exact markup from the issue in a tiny HTML fixture.
2. Fill the nested fields and observe the spacing collapse below the inner form layout.
3. Add explicit `user-assistance-density='efficient'` and confirm the layout recovers.

## Candidate files

- `dist/js/libs/oj/debug/ojformlayout.js`
- `dist/js/libs/oj/debug_esm/ojformlayout.js`
- `dist/js/libs/oj/debug/ojeditablevalue.js`
- `dist/js/libs/oj/debug_esm/ojeditablevalue.js`

## Validation path

- Fixture render before/after screenshots, or
- a scripted DOM/class-state check if the inheritance path can be isolated without a full browser app

## Root-cause questions

- Is nested inheritance skipping the default `efficient` value?
- Is the inner layout writing class state correctly but child controls reading container density incorrectly?
- Is this a runtime inheritance bug or just a missing style-class update?

## pi-autoresearch

**Not yet.** We need a stable scripted repro first. Until then, systematic debugging plus a tight fixture is the right approach.
