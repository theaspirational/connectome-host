# Review — TASK-EC6WW (LOCAL ONLY)

Range reviewed: `c353e1c8d19a1169..698bb5870116d14e` (2 commits). Worktree detached at
`698bb5870116d14e45ae5e78ff1bdf37fb95c144`, left clean (no commits, no edits; probes in `/tmp/ec6ww`).
Task operator Tarn, qualifier Soma; reviewer is not owner.

## Verdict

**APPROVE — HIGH confidence.**

The narrowing is sound for the purpose asked: it can be relied on as **one input to the publication
gate** (predicate + the enumerated `--docs-contract` inputs), and **not** as a privacy control.
Everything was re-derived from source with `/opt/node-v22.23.3-linux-x64/bin/node`; all nine
implementer receipts were reproduced independently, plus three extra mutants I wrote and ran.

Required explicit statements:
- A `--docs-contract` PASS describes **only** the tested predicate over the 18 enumerated inputs.
  It is **not** a "privacy-clean" claim and carries no universal-privacy guarantee.
- The tip fix does **not** clean history. `a5017576` remains in history containing the real values
  `['task', '4zm52']` (checker:261) and `['run', '01M68913ZJTD9137NBTA1RGTXS']` (checker:262) —
  historical, correctly **not** rewritten per the qualifier's instruction. Private refs retained.

## Findings

### MEDIUM — test/correctness, pre-existing scope gap
`check-scope-contract.mjs:231-232` — the prefix alternation is `(?:TASK|RUN|AGENT|PROJECT)`. The
real programme format **`CONV-XXXXX` is not covered at all, in any case**. Empirically `CONV-` is the
*most common* real prefix in the ledger: 24 of 41 real ids found by
`grep -rhoE '\b(TASK|CONV|AGENT|PROJECT)-[0-9A-Z]{5}\b'` over the ledger. Probe: `CONV-153K8` →
MISS, `conv-153k8` → MISS. This is **not a regression** (`c353e1c8`'s regex had the same prefix set)
and is outside the assignment's wording, but it is decisive for the question "can this be relied on
as a publication-gate input": the gate is blind to the single largest real id class.
*Fix direction:* add `CONV` to both branches of the alternation and add an `uppercase prefix CONV`
control; mutant m6 (below) shows per-prefix controls are already discriminating.

### MEDIUM — correctness, quantified false-negative class (intended trade-off, but larger than the comment implies)
`check-scope-contract.mjs:227,231-232` — the comment reads *"Uppercase identifiers remain reserved;
other prefix cases require a digit."* The code is stricter than that: branch 1 requires the **whole
token** uppercase (prefix *and* suffix), not just the prefix. Measured:

| sample | result |
|---|---|
| `TASK-ABCDE` | CATCH |
| `TASK-abcde` | **MISS** (uppercase prefix, lowercase suffix, no digit) |
| `Task-ABCDE` | **MISS** |
| `TASK-AbCdE` | **MISS** |
| `TASK-MHQNZ` / `TASK-ZPVAY` | CATCH |
| `task-mhqnz` / `task-zpvay` | **MISS** (real ids, lowercased) |
| `task-ec6ww`, `run-01M6898ZKQ32H2DASBRH3B1C3A` | CATCH (digit present) |

**Share of real ids with an all-letter suffix** (which evade whenever the token is not fully
uppercase): 2/17 TASK (`TASK-MHQNZ`, `TASK-ZPVAY`) = 11.8%; 5/24 CONV = 20.8%; **7/41 = 17% overall**,
matching the theoretical `(26/36)^5 ≈ 19.3%` for a 5-char `[0-9A-Z]` suffix. `run-01M…` always has a
digit and is caught in any case — that class is safe.

This is not hypothetical: lowercase id renderings are routine in this programme (this worktree's own
branch `task-ec6ww-impl` and path `.../task-ec6ww`). A branch or slug named `task-mhqnz-impl` would
pass the gate. The assignment explicitly sanctioned "uppercase prefix **or** a digit in the suffix",
so this is the accepted cost, not a defect — but the inline comment overstates the coverage and
should be corrected to "fully-uppercase tokens remain reserved". Note `TASK-EC6WW-IMPL1.md:31-33`
states the rule correctly ("uppercase prefix/suffix is reserved even without digits") — it is the
in-code comment, the thing a future maintainer reads, that is loose.

### MEDIUM — the fix round's remediation was scoped to git only; the real ids remain in the shared report drop
`/srv/review-drop/TASK-EC6WW-IMPL1.md:49,50,196,197` — the r1 implementer report still contains the
real values in plain text: `task-4zm52` (:49, :196) and `run-01M68913ZJTD9137NBTA1RGTXS` (:50, :197).
`698bb587` replaced these in the repo, and `diff-id-probe.py` / `prefix-probe.sh` verify only the git
net diff and commit messages — neither looks at `/srv/review-drop`. `TASK-EC6WW-FIX1.md` never
mentions `IMPL1` and does not supersede or flag it (`grep -n 'IMPL1\|review-drop\|4zm52'` on FIX1 →
only an incidental grep-pattern echo at :48). So the artifact the fix round existed to sanitise is
still unsanitised in a *shared, world-readable* drop directory (`drwxr-xr-x root`), which is more
exposed than the private git history the qualifier deliberately chose to keep.
I have not touched it — remediating another round's report is outside a reviewer's write scope.
*Fix direction:* operator decision. Either replace the four lines in `TASK-EC6WW-IMPL1.md` with the
synthetic values, or add an explicit supersede note in `FIX1.md` recording that `IMPL1.md` retains the
pre-fix values by the same "history is not cleaned" rationale. Also extend the neutrality probe's
input set beyond git to the report drop, otherwise its PASS keeps missing this surface.

### LOW — pre-existing `\b` boundary gaps, not introduced here
`_` is a word character, so `\b` does not fire adjacent to it. Verified MISS at HEAD **and** under
the old `c353e1c8` regex (so: not a regression):
`x_TASK-ABCDE`, `TASK-ABCDE_x`, `x_run-01M6898ZKQ32H2DASBRH3B1C3A`, `run-01M6898ZKQ_x`, `task_abcde`.
Path/URL embedding is fine (`/` is non-word): `a/TASK-ABCDE`, `https://x/y/task-ec6ww/z`,
`runs/run-01M…/evidence` all CATCH. Also MISS: `#task-abcde` is caught, but `tasks-abcde`,
`mytask-abcde`, `sub-task-abcde` correctly pass (desired).

### LOW — implementer's neutrality probe is narrower than its PASS line reads
`$REVIEWED/diff-id-probe.py:11` greps only `01M6|4zm52|(?:TASK|RUN|CONV)-[A-Z0-9]+` — i.e. only the
specific values `a5017576` leaked. Its own source therefore records the real fragment `4zm52`, in the
evidence dir (outside the tracked tree — consistent with "history isn't cleaned", not a new tip leak).
I ran a broader independent scan instead (below) and it is clean, so the conclusion holds.

## Verification Gates

Re-run by me, wrapped (reusable receipts in `$ORGASMIC_EVIDENCE_DIR`):
- `rv-self-test` — exit 0 — `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test`
  — 13 identifier controls PASS, 45 rejection controls, 4 oracle mutants killed.
  log `gate-rv-self-test-01M689EKGYQXPHNABKG0KE32GD.log`
- `rv-docs-contract` — exit 0 — `… --docs-contract` → `PASS docs-contract: 18 public files;
  11 rejected controls; 0 injected names`. log `gate-rv-docs-contract-01M689EP5MN3STRBCB6S6YPYW2.log`

Unwrapped scoped probes (no reusable receipt, all bounded `timeout 600`, no retries, no installs):
- **Predicate truth table** (`/tmp/ec6ww/probe.mjs`, `p2.mjs`) — 44 samples; results tabulated above.
- **Old-vs-new regex differential** (`/tmp/ec6ww/oldprobe.mjs`) — isolates the `_`-boundary gaps as
  pre-existing and confirms `agent-config` / `run-mutants` were CATCH under the old regex (the bug
  this task fixes) and are PASS now.
- **Implementer mutants m1–m4** re-run via `$REVIEWED/identifier-mutants.mjs` — all four killed,
  each by its own named control, source sha256 `568dc0f4…`:
  m1 (restore broad `iu`) → `ordinary filename run mutants`; m2 (uppercase-only) → `lowercase with
  digits`; m3 (`return false`) → `uppercase without digits`; m4 (diverged second copy at the scan
  consumer) → `identifier scan control: ordinary filename run mutants`.
- **Three extra mutants I wrote and ran** (`/tmp/ec6ww/extra-mutants.mjs`) — all killed, no survivors:
  - **m5** drop the case-sensitive uppercase branch, keep only the digit branch → killed by
    `uppercase without digits`. (m3 kills both branches at once; m5 isolates branch 1 — this was the
    one plausible coverage gap, and it is covered.)
  - **m6** drop `PROJECT` from the alternation in both branches → killed by `uppercase prefix PROJECT`.
    Confirms the four per-prefix controls are load-bearing, not decoration.
  - **m7** make the digit branch case-sensitive (`u` instead of `iu`) → killed by `lowercase with digits`.
- **Other detectors not weakened** — `git diff c353e1c8 HEAD` touches exactly one existing line
  (`assert(!/\b(?:TASK|…)/iu.test(content))` → `assert(!internalIdentifier(content))`). UUID
  (`:239`), credential (`:240`), machine-local path (`:238`), `toolingEnvironment` (`:224`),
  `toolingCli` (`:225`), `programmeIntegration` (`:226`) and `forbiddenNames` (`:237`) are
  byte-identical. Nothing removed from the docs list (the diff adds one line, removes none); no
  name/path exemption exists — `texts.forEach(neutral)` at `:395` is unconditional and includes
  `./check-scope-contract.mjs` itself. `grep -niE 'exempt|skip|allowlist|whitelist|ignore'` returns
  only three comments asserting the *absence* of exemptions.
- **Neutrality of the source (broader than the implementer's probe)** —
  `git grep -niIE "<all 41 real ledger ids>" HEAD` → no match; `git grep -nIE
  'run-01[0-9A-HJKMNP-TV-Z]{24}' HEAD` → no match; `docs/design/f-host-h-brief.md` (409 lines) clean.
  Commit messages for both commits are subject + `Co-Authored-By:` only — clean.
  Every reconstructed literal in the diff evaluated and checked against the real-id list
  (`/tmp/ec6ww/samples.mjs`): `TASK-EXAMPLE`, `run-0123456789`, `task-0abc0`,
  `run-0Z0Z0Z0Z0Z0Z0Z0Z0Z0Z0Z0Z0Z` (= `'0Z'.repeat(13)`, 26 chars), `AgEnT-a1b`, the four
  `<PREFIX>-EXAMPLE`, and the four filenames → **none matches any real id**. The split-literal
  technique here serves self-scan non-exemption, not concealment.
  `git show a5017576:…` confirms the real values existed only there and were replaced at the tip.

Not re-run (receipt reused, substance re-derived by the probes above): `fix-diff-check`,
`fix-prefix-probe`.

## Unmet Criteria / test gaps

Assignment criteria are all met: predicate narrowed without filename special-casing; brief added to
the docs list; all pre-existing rejection controls retained (45 in self-test, 11 in docs-contract);
controls added for real-id shapes and for ordinary filenames.

Remaining test gaps:
1. **No control for `CONV-`** at all (MEDIUM above) — zero coverage of the largest real prefix class.
0. **Nothing verifies the report drop.** `diff-id-probe.py` and `prefix-probe.sh` bound their input to
   `git diff`/`git log` output, so the gate suite structurally cannot detect the retained real ids in
   `/srv/review-drop/TASK-EC6WW-IMPL1.md`. A PASS on those two gates must not be read as "the round's
   artifacts are free of real ids".
2. **No negative-direction control documenting the accepted false negatives.** Nothing in the suite
   asserts `internalIdentifier('task-abcde') === false` / `('TASK-abcde') === false`. The trade-off is
   therefore undocumented in executable form and a future "fix" could silently re-widen or re-narrow
   it. Adding two `false`-expectation cases to `identifierControls()` would pin it.
3. **No control for `_`-adjacent ids** (LOW above); the gap is pre-existing and unpinned either way.
4. `--docs-contract` reports `PARTIAL naming check: no nonempty SCOPE_FORBIDDEN_NAMES list` — the
   name-injection arm of the gate ran with 0 injected names in this environment, so only
   path/id/credential checks were exercised. Pre-existing, environment-dependent.

## Residual Risk

- ~17% of real ids (all-letter suffix) pass the gate whenever they appear not-fully-uppercase;
  100% of `CONV-*` ids pass in every case. Treat a PASS as "these 18 inputs contain no
  **fully-uppercase or digit-bearing** TASK/RUN/AGENT/PROJECT token", nothing more.
- `a5017576` retains two real values in private history; `diff-id-probe.py` retains the `4zm52`
  fragment in the run evidence dir. Both are intentional and out of scope to clean. **The same is not
  true of `/srv/review-drop/TASK-EC6WW-IMPL1.md`** (MEDIUM above): that is a shared, world-readable
  path, no receipt covers it, and no document records the decision to leave it.
- The detector is a text-shape heuristic. It cannot see ids that are themselves split/reconstructed,
  encoded, or paraphrased in an input file — the same technique the controls use deliberately.

## Fix Directions

1. (MEDIUM) Add `CONV` to both branches at `check-scope-contract.mjs:231-232`; add an
   `uppercase prefix CONV` control to the `['TASK','RUN','AGENT','PROJECT']` map at `:263`.
2. (MEDIUM) Correct the comment at `:227` to "fully-uppercase tokens remain reserved; any other
   casing requires a digit in the suffix" — the current wording implies prefix-only casing suffices.
3. (test) Add explicit `false`-expectation controls for `task-abcde` and `TASK-abcde` so the accepted
   false-negative boundary is pinned executably rather than implied.
4. (optional, LOW) If `_`-adjacent ids matter, replace the trailing `\b` with a `(?![A-Z0-9-])`-style
   guard and widen the leading boundary; add controls either way so the chosen behaviour is pinned.
5. Adopt m5/m6/m7 into `identifier-mutants.mjs` — m5 in particular isolates branch 1, which m1–m4
   only cover jointly via `return false`.
