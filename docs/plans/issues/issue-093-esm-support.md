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

`node --no-warnings scripts/repros/issue-093-esm-imports.mjs`

The repro imports and lightly exercises:
- `ojlogger`
- `ojeventtarget`
- `ojkeyset`
- `ojconverter-nativenumber`
- `ojarraydataprovider`

## Current status

- Branch pushed: `issue-93-esm-support`
- Fork URL: `https://github.com/jasperan/oraclejet/tree/issue-93-esm-support`

## Deferred follow-up

`Config.setLocale()` still relies on AMD-style locale-bundle loading semantics. That looks fixable, but it is a second step beyond the initial self-contained ESM bootstrap.
