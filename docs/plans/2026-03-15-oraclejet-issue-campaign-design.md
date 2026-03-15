# Oracle JET issue campaign design

## Objective

Audit all 49 currently open GitHub issues for `oracle/oraclejet`, then produce a clean fix campaign in the fork `jasperan/oraclejet` where each actionable issue gets its own isolated branch, validation path, and push target.

## Constraints

- Upstream is `oracle/oraclejet`, default branch `master`.
- The repo is a packaged distribution snapshot. Most code lives under `dist/`.
- The repo does not expose a normal test suite through `package.json` scripts.
- The repo maintainers state that they are not currently accepting external contributions, so the primary target is a clean maintained fork rather than upstream merge throughput.
- The user wants each issue fix pushed separately so Oracle can approve or ignore changes one by one.
- pi-autoresearch should be used only where the issue is measurable and benefits from an experiment loop.

## Repository observations

- Open issues: 49
- Repo shape: small root, large `dist/` payload, no obvious source tree, no obvious test runner
- Baseline local validation available right now:
  - `npm install --package-lock=false`
  - `npm pack --dry-run`
- Current dependency surface includes older ecosystem pieces like `knockout`, `requirejs`, and `hammerjs`, which line up with several open maintenance and packaging issues.

## Campaign shape

### 1. Full triage first

Every open issue gets reviewed and placed into one of 4 buckets:

1. **Code-fixable now**
   - reproducible bug
   - packaging problem
   - type declaration problem
   - dependency maintenance problem
2. **Patchable feature request**
   - enhancement that looks self-contained and low blast radius
3. **Needs stronger repro**
   - plausible issue, but not enough detail to make a safe code change yet
4. **Non-code / support**
   - usage question, environment-specific report, or request outside this repo’s control

### 2. One issue, one branch

Every actionable issue gets a dedicated branch from a clean fork baseline.

Examples:
- `issue-94-knockout-maintenance`
- `issue-93-esm-support`
- `issue-89-arraydataprovider-typing`

No branch should mix unrelated fixes.

### 3. Separate validation per issue

Because the repo lacks a general test suite, validation must be issue-specific.

Possible validation modes:
- install/build/package smoke check
- TypeScript compile check against the published declarations
- issue-specific repro script
- dependency audit comparison
- manual import/runtime scenario

### 4. Use pi-autoresearch selectively

Autoresearch is a fit for issues that have:
- a reproducible command
- a measurable primary metric or pass/fail target
- enough surface area for iteration

Likely candidates:
- install or packaging failures
- ESM / module packaging work
- dependency compatibility upgrades
- type declaration regressions
- performance complaints with a reproducible workload

Not good candidates:
- vague support questions
- broad roadmap asks
- issues that depend on user app context we do not have

## Output contract

The campaign is successful when it produces:

- a complete triage map of all open issues
- a fork at `jasperan/oraclejet`
- one pushed branch per actionable issue
- issue-specific autoresearch artifacts only where experimental loops are justified
- a record of issues that are not honestly actionable from this repo, with reasons

## Risk management

- Do not claim unsupported issues are fixed.
- Do not mix feature requests with bugfixes unless the issue itself requires it.
- Prefer the smallest reviewable patch.
- If a fix requires a huge architectural rewrite, classify it as non-actionable for this repo snapshot instead of forcing a weak patch.

## Validation baseline gathered so far

- `npm install --package-lock=false` succeeds
- `npm pack --dry-run` succeeds
- No standard `npm test` script exists
