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

## Validation

- `node --no-warnings scripts/repros/issue-093-esm-imports.mjs`
- `./autoresearch.sh`

The installed-package benchmark now covers 16 checks across:
- logging
- event mixins
- key sets
- number conversion
- array data providers
- locale switching
- translations
- local-date conversion
- sync and async validators
- URL adapters
- tree data providers
- locale data
- time utilities
- timezone utilities

## Current status

- Branch pushed: `issue-93-esm-support`
- Fork URL: `https://github.com/jasperan/oraclejet/tree/issue-93-esm-support`

## Follow-up completed

A second commit on the branch added generated ESM locale shims and rewired `ojconfig.setLocale()` so representative locale switches (`fr`, `de`) work under the native ESM smoke test too.

A third commit added package-level `exports` entries so installed-package subpath imports like `@oracle/oraclejet/ojkeyset` and `@oracle/oraclejet/ojconfig` work under the representative Node ESM smoke test as well.

A fourth commit added generated ESM locale-elements and timezone-data shims so `ojlocaledata`, `ojtimeutils`, and `ojtimezoneutils` also work under the installed-package ESM benchmark.
