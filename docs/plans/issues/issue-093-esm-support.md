# Issue #93: OJET support for ESM modules

- Issue: https://github.com/oracle/oraclejet/issues/93
- Bucket: `patchable feature request`

## What the branch does

The pushed branch makes the shipped `debug_esm` tree self-contained enough to load representative modules under native Node ESM without an external `ojs` package alias.

## Key changes

- rewrote `debug_esm` imports from bare `ojs/*` specifiers to relative `./*.js` imports
- added `dist/js/libs/oj/debug_esm/package.json` with `type: module`
- patched `ojconfig.js` to use a generated local ESM translation shim at bootstrap time
- patched `ojdataprovider.js` to use the imported `oj$1` namespace instead of a stray global `oj`

## Validation

- `node --no-warnings scripts/repros/issue-093-esm-imports.mjs`
- `./autoresearch.sh`

The installed-package benchmark now covers 13 checks across:
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

## Current status

- Branch pushed: `issue-93-esm-support`
- Fork URL: `https://github.com/jasperan/oraclejet/tree/issue-93-esm-support`

## Follow-up completed

A second commit on the branch adds generated ESM locale shims and rewires `ojconfig.setLocale()` so representative locale switches (`fr`, `de`) work under the native ESM smoke test too.

A third commit adds package-level `exports` entries so installed-package subpath imports like `@oracle/oraclejet/ojkeyset` and `@oracle/oraclejet/ojconfig` work under the representative Node ESM smoke test as well.
ckage subpath imports like `@oracle/oraclejet/ojkeyset` and `@oracle/oraclejet/ojconfig` work under the representative Node ESM smoke test as well.
