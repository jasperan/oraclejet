# Oracle JET issue campaign implementation plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Triage all open `oracle/oraclejet` issues, then push isolated fix branches to `jasperan/oraclejet`, one issue per branch.

**Architecture:** Keep the repo clean by separating campaign planning from issue execution. First build a complete issue matrix and fork wiring, then reproduce and fix only the issues that are actually actionable from this distribution snapshot. Use pi-autoresearch only on issues with a reproducible command and measurable validation loop.

**Tech Stack:** GitHub CLI, git worktrees, npm, Node.js, Oracle JET distribution files under `dist/`

---

### Task 1: Snapshot the full open-issue inventory

**Files:**
- Modify: `docs/plans/2026-03-15-oraclejet-issue-campaign-design.md`
- Create: `docs/plans/2026-03-15-oraclejet-issue-matrix.md`

**Step 1: Create the issue matrix header**

Write `docs/plans/2026-03-15-oraclejet-issue-matrix.md` with this table header:

```markdown
# Oracle JET open issue matrix

| Issue | Title | Bucket | Repro status | Candidate files | Validation path | Branch name | Notes |
|---|---|---|---|---|---|---|---|
```

**Step 2: Pull the open issues into a local snapshot**

Run:

```bash
gh issue list --repo oracle/oraclejet --state open --limit 100 --json number,title,url > /tmp/oraclejet-open-issues.json
node -e "const fs=require('fs'); const issues=JSON.parse(fs.readFileSync('/tmp/oraclejet-open-issues.json','utf8')); console.log(issues.length)"
```

Expected: `49`

**Step 3: Append one row per issue**

Use the JSON snapshot to append placeholder rows for every issue to `docs/plans/2026-03-15-oraclejet-issue-matrix.md`.

**Step 4: Commit the matrix scaffold**

```bash
git add -f docs/plans/2026-03-15-oraclejet-issue-matrix.md docs/plans/2026-03-15-oraclejet-issue-campaign-design.md
git commit -m "docs: add oraclejet issue campaign inventory scaffold"
```

### Task 2: Create and verify the fork target

**Files:**
- Modify: `.git/config`
- Modify: `docs/plans/2026-03-15-oraclejet-issue-matrix.md`

**Step 1: Create the fork and add a remote**

Run from the repo root:

```bash
gh repo fork --remote --remote-name fork
```

Expected: `fork` remote points to `git@github.com:jasperan/oraclejet.git`

**Step 2: Verify remotes**

Run:

```bash
git remote -v
```

Expected: `origin` still references `oracle/oraclejet`, and `fork` references `jasperan/oraclejet`

**Step 3: Push the planning branch**

Run:

```bash
git push -u fork campaign/oraclejet-issues
```

Expected: branch `campaign/oraclejet-issues` exists on the fork

**Step 4: Record fork readiness**

Add a short note to the matrix file that the fork exists and planning branch push succeeded.

### Task 3: Triage all 49 issues into actionable buckets

**Files:**
- Modify: `docs/plans/2026-03-15-oraclejet-issue-matrix.md`
- Create: `docs/plans/issues/README.md`

**Step 1: Create the issue-notes directory**

Create `docs/plans/issues/README.md` with a short note saying this directory stores per-issue repro notes for the campaign.

**Step 2: Review each issue**

For each open issue, run:

```bash
gh issue view <number> --repo oracle/oraclejet
```

Fill in the matrix columns:
- Bucket: `code-fixable now`, `patchable feature request`, `needs stronger repro`, or `non-code / support`
- Repro status: `not tried`, `reproducible`, `insufficient detail`, or `not repo-owned`
- Candidate files: exact paths when known, otherwise `TBD after repro`
- Validation path: exact command or manual check target
- Branch name: `issue-<number>-<slug>` for actionable issues

**Step 3: Validate the triage count**

Run:

```bash
node -e "const fs=require('fs'); const s=fs.readFileSync('docs/plans/2026-03-15-oraclejet-issue-matrix.md','utf8'); const rows=s.split('\n').filter(l=>l.startsWith('| #')); console.log(rows.length)"
```

Expected: `49`

**Step 4: Commit the triage pass**

```bash
git add -f docs/plans/2026-03-15-oraclejet-issue-matrix.md docs/plans/issues/README.md
git commit -m "docs: classify oraclejet open issues"
```

### Task 4: Write the first-wave reproduction notes

**Files:**
- Create: `docs/plans/issues/issue-089-arraydataprovider-typing.md`
- Create: `docs/plans/issues/issue-070-intlnumberconverter-sign-display.md`
- Create: `docs/plans/issues/issue-064-user-assistance-density.md`
- Create: `docs/plans/issues/issue-059-ojinputtext-extra-space.md`
- Create: `docs/plans/issues/issue-052-media-query-observable-null.md`

**Step 1: Write one note per likely actionable issue**

Each note must include:
- original issue link
- minimal repro steps
- exact candidate files
- validation command
- whether pi-autoresearch is justified

Use exact file-path guesses grounded in the current repo. Examples:
- `dist/types/ojarraydataprovider/index.d.ts`
- `dist/types/ojrouterstate/index.d.ts`
- `dist/js/libs/oj/debug/ojresponsiveknockoututils.js`
- `dist/js/libs/oj/debug_esm/ojresponsiveknockoututils.js`
- `dist/js/libs/oj/debug/ojinputtext.js`
- `dist/js/libs/oj/debug_esm/ojinputtext.js`
- `dist/js/libs/oj/debug/ojeditablevalue.js`
- `dist/js/libs/oj/debug_esm/ojeditablevalue.js`

**Step 2: Rank the first issue to execute**

Pick the issue with the cleanest repro and smallest blast radius. Write `Priority: 1` at the top of that issue note.

**Step 3: Commit the repro notes**

```bash
git add -f docs/plans/issues/*.md
git commit -m "docs: add first-wave oraclejet issue repro notes"
```

### Task 5: Start the first autoresearch-backed issue branch

**Files:**
- Create: `autoresearch.md`
- Create: `autoresearch.sh`
- Create: `autoresearch.checks.sh`

**Step 1: Create the first issue branch from a clean fork baseline**

Run:

```bash
git fetch origin master
git checkout -b issue-89-arraydataprovider-typing origin/master
```

Expected: current branch is `issue-89-arraydataprovider-typing`

**Step 2: Write the autoresearch objective**

Set `autoresearch.md` to describe:
- objective: reproduce and eliminate the `ArrayDataProvider` / `DataProvider` typing mismatch from issue `#89`
- primary metric: pass/fail of the TypeScript repro command, treated as `1` for pass and `0` for fail if no finer metric exists
- files in scope: `dist/types/ojarraydataprovider/index.d.ts`, `dist/types/ojrouterstate/index.d.ts`, related declarations discovered during repro
- off-limits: unrelated runtime bundles and styling assets

**Step 3: Write the experiment runner**

Set `autoresearch.sh` to:
- fail fast on syntax/setup errors
- build a tiny TS repro in a temp directory
- print `METRIC pass=0` on failure or `METRIC pass=1` on success

**Step 4: Write the checks runner**

Set `autoresearch.checks.sh` to run:

```bash
npm pack --dry-run >/dev/null
```

Expected: exit code `0`

### Task 6: Fix, verify, and push the first issue branch

**Files:**
- Modify: `dist/types/ojarraydataprovider/index.d.ts`
- Modify: `dist/types/ojrouterstate/index.d.ts`
- Modify: any additional declaration file named in `docs/plans/issues/issue-089-arraydataprovider-typing.md`

**Step 1: Reproduce the failure before editing**

Run the exact repro command captured in the issue note.

Expected: TypeScript compile fails with the `prototype` mismatch.

**Step 2: Make the smallest declaration fix**

Update only the declarations required to make the repro compile without widening unrelated public types.

**Step 3: Re-run validation**

Run:

```bash
./autoresearch.sh
./autoresearch.checks.sh
```

Expected: `METRIC pass=1` and checks exit `0`

**Step 4: Commit and push the issue branch**

```bash
git add dist/types/ojarraydataprovider/index.d.ts dist/types/ojrouterstate/index.d.ts autoresearch.md autoresearch.sh autoresearch.checks.sh
git commit -m "fix: resolve arraydataprovider typing mismatch"
git push -u fork issue-89-arraydataprovider-typing
```

### Task 7: Repeat the branch pattern for the remaining actionable issues

**Files:**
- Modify: `docs/plans/2026-03-15-oraclejet-issue-matrix.md`
- Modify: the exact files named in each issue note

**Step 1: For each actionable issue, create a clean branch**

Pattern:

```bash
git fetch origin master
git checkout -b issue-<number>-<slug> origin/master
```

**Step 2: Decide execution mode per issue**

Use pi-autoresearch when the note has a measurable repro. Use a normal minimal-fix workflow when the issue is too small or too qualitative for a loop.

**Step 3: Record branch status in the matrix**

Update each matrix row with:
- `in progress`
- `pushed to fork`
- `not actionable`
- `blocked`

**Step 4: Commit matrix updates after each issue**

```bash
git add -f docs/plans/2026-03-15-oraclejet-issue-matrix.md docs/plans/issues/*.md
git commit -m "docs: update oraclejet issue campaign status"
```

### Task 8: Publish the final campaign summary

**Files:**
- Create: `docs/plans/2026-03-15-oraclejet-issue-campaign-summary.md`

**Step 1: Write the summary file**

Include:
- total issues reviewed
- total actionable issues
- total pushed branches
- non-actionable issue list with reasons
- blocked issue list with missing context

**Step 2: Push the summary branch updates**

Run:

```bash
git add -f docs/plans/2026-03-15-oraclejet-issue-campaign-summary.md
git commit -m "docs: summarize oraclejet issue campaign"
git push
```

Expected: the planning branch contains the final campaign summary
