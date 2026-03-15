# Issue #93: OJET support for ESM modules

- Issue: https://github.com/oracle/oraclejet/issues/93
- Bucket: `patchable feature request`

## What the branch does

The pushed branch makes the shipped `debug_esm` tree self-contained enough to load representative modules under native Node ESM without an external `ojs` package alias, and also exposes those modules through installed-package subpath imports like `@oracle/oraclejet/ojkeyset`.

## Key changes

- rewrote `debug_esm` imports from bare `ojs/*` specifiers to relative `./*.js` imports
- added `dist/js/libs/oj/debug_esm/package.json` with `type: module`
- patched `ojconfig.js` to use generated local ESM translation shims at bootstrap time and during `setLocale()`
- patched `ojdataprovider.js` to use the imported `oj$1` namespace instead of a stray global `oj`
- added package-level `exports` so installed-package subpath imports resolve to the ESM runtime and type declarations
- guarded `ojthemeutils.parseJSONFromFontFamily()` for non-browser hosts so `LocalDateConverter` can fall back to defaults instead of crashing on `window`
- generated ESM `localeElements` and `timezoneData` shims and rewired `ojlocaledata.js`, `ojtimezonedata.js`, and `ojconfig.setLocale()` to them so locale-data and timezone-dependent package imports work under native ESM too
- fixed legacy `oj` alias references in `ojconverter-number.js` and `ojconverter-datetime.js` so the older number/date converter stack and date validators import cleanly under installed-package ESM checks

## Validation

- `node --no-warnings scripts/repros/issue-093-esm-imports.mjs`
- `./autoresearch.sh`

The installed-package benchmark now covers 20 checks across:
- logging
- event mixins
- key sets
- native and legacy number conversion
- native and legacy datetime conversion
- array data providers
- locale switching
- translations
- local-date conversion
- sync and async validators
- date restriction and date-time range validators
- URL adapters
- tree data providers
- locale data
- time utilities
- timezone utilities

## Current status

- Branch pushed: `issue-93-esm-support`
- Fork URL: `https://github.com/jasperan/oraclejet/tree/issue-93-esm-support`

## Commit progression

1. self-contained `debug_esm` imports
2. translation shims + `ojconfig.setLocale()` support
3. package-level ESM subpath exports
4. locale-elements + timezone-data ESM shims
5. legacy converter alias cleanup for date-validator coverage
