# Issue #52: `ResponsiveKnockoutUtils.createMediaQueryObservable` gets null query

- Issue: https://github.com/oracle/oraclejet/issues/52
- Bucket: `needs stronger repro`

## Report summary

The issue says this expression throws:

```js
ResponsiveKnockoutUtils.createMediaQueryObservable(
  ResponsiveUtils.getFrameworkQuery(ResponsiveUtils.FRAMEWORK_QUERY_KEY.XL_ONLY)
)
```

Current code already returns `null` from `getFrameworkQuery(...)` when responsive media-query classes are not present, and `createMediaQueryObservable(...)` warns/throws on null input. That means this could be either:
- a real framework-query gap, or
- expected behavior in a theme/config with responsive classes disabled.

## Minimal repro plan

1. Verify what `ResponsiveUtils.getFrameworkQuery(ResponsiveUtils.FRAMEWORK_QUERY_KEY.XL_ONLY)` returns in a minimal page.
2. Check whether the required responsive CSS classes are present.
3. Confirm whether the problem reproduces with default theme settings.

## Candidate files

- `dist/js/libs/oj/debug/ojresponsiveutils.js`
- `dist/js/libs/oj/debug_esm/ojresponsiveutils.js`
- `dist/js/libs/oj/debug/ojresponsiveknockoututils.js`
- `dist/js/libs/oj/debug_esm/ojresponsiveknockoututils.js`

## Validation command

```bash
node ./scripts/repros/issue-052-responsive-query.mjs
```

Success means we can distinguish configuration failure from library failure and, if it is a library bug, reproduce it consistently.

## pi-autoresearch

**No, not yet.** First we need root-cause clarity. Autoresearch only makes sense after we know whether the bug is in query generation or in the observable wrapper.
