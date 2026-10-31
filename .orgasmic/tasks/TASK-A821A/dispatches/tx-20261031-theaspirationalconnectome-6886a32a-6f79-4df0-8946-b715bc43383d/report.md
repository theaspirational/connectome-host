# Review — TASK-A821A fix round (bounded delta)

Range `e5184b31a1dc077ac1ef0cfec6841866a4ec4f9a..8dd238b5cb5b18c89d64512c1cac8d439ffdc2c4`
(4 commits, docs/test only). Inputs read in full, hashes confirmed:
`/srv/review-drop/TASK-A821A-REVIEW1.md` = `d828893296ca5c45182c95124160f30d91aabc0a80de81552ccd003603d851a1`,
`/srv/review-drop/TASK-A821A-FIX1.md` = `34a338516eaf76b2f7e056d86e7333295d5da6340b799d2e6b007270a397f650`.
Task operator Tarn; constraints from Soma (programme qualifier); not owner (Vasyl) decisions.
Worktree left clean (`git status --porcelain` empty before and after every probe); no edits, no commits.

## Verdict

**APPROVE.** All four review concerns (F1–F4) are addressed as directed, and I
reproduced each of the brief's required checks independently rather than
accepting the fix report's claims. No HIGH or MEDIUM findings. Four LOW items
below; none blocks this round.

Confirmed point by point:

- **F2 — closed.** `git grep -n -i -E 'orgasmic|\bINT-[0-9]+\b' -- .` → exit 1, zero output.
  Three new structural rules at `test/fixtures/generic-recipient/check-scope-contract.mjs:55-57`
  with three new controls at `:120-122`. I mutation-tested them: deleting the
  `toolingCli` rule and deleting the `programmeIntegration` rule each makes
  `--docs-contract` fail with "Missing expected exception" — the controls are
  load-bearing, not decorative. Injecting each live token (`orgasmic dispatch gate x`,
  `ORGASMIC_EVIDENCE_DIR`, `INT-1`) into a mirrored `f-host-2a-brief.md` makes
  `--docs-contract` exit 1 while `--self-test` stays 0.
- **PARTIAL semantics unchanged.** With no `SCOPE_FORBIDDEN_NAMES`: `PARTIAL naming
  check: ... path/id/credential checks only` then `PASS docs-contract: 9 public
  files; 11 rejected controls; 0 injected names`, exit 0. With a valid nonempty
  list the PARTIAL line disappears; with an invalid entry (`bad-1`) it refuses,
  exit 1. `parseForbiddenNames` and the PARTIAL branch (`:110`) are byte-identical
  to base.
- **`bounded-gate.sh` without private env.** `test/stubs/f-host-2a/bounded-gate.sh:9-12`
  now takes `BOUNDED_LOG_DIR` with a `mktemp -d` fallback. Verified with both
  `BOUNDED_LOG_DIR` and `ORGASMIC_EVIDENCE_DIR` unset: exit 0. Arg-count guard → 2;
  nonexistent `BOUNDED_LOG_DIR` → 2. Classification logic (`:21-24`) untouched.
- **F3 — closed, reproduced in my own temp mirrors** (not the implementer's).
  For each of the five supplemental files removed individually: `--docs-contract`
  exits 1 with `docs-contract input missing or unreadable: <path>` naming the file,
  with **no** `PASS docs-contract`, **no** `PARTIAL naming`, and **no** stack
  trace / `Error: ENOENT` / `AssertionError`; `--self-test` and default both exit 0.
  Checker `:91-108` moved the reads into `docsContract()` behind try/catch with
  `process.exitCode = 1; return`.
- **F4 — as directed.** `test/stubs/f-host-2a/v0/typecheck.ts:29-32` now declares
  `objectDescriptor: { readonly [key: string]: ReadonlyData }` and asserts the write
  against that branch, replacing the old union-wide `descriptor.changed` assertion.
  Discriminating by construction: `true` is assignable to `ReadonlyData`
  (`stubs.ts:12-13`), so the only possible diagnostic at that site is the readonly
  index write (TS2542) — nullability and missing-property diagnostics are excluded.
  Its validation is correctly labelled PENDING; **I did not run any borrowed
  compiler as a gate**, consistent with the Soma classification.
- **F1 — PENDING everywhere.** `brief:204-209` states `stub-types` is PENDING and the
  5.9.3 result EXPLORATORY; `:211-215` adds the compiler-major rule
  ("the compiler **major must match the `package.json` TypeScript declaration**",
  record version *plus* the declaration it satisfies, closure, hash, input list,
  and "a zero-byte compiler log alone is not a closed gate"); `:216-230` carries all
  six TS7 risk surfaces verbatim; `:231-232` explicitly refuses both the 5.9.3
  observation and the reviewer's borrowed 7.0.2 probe. `stub-types` and `TSC_JS`
  are gone from the gate command block (`:245-249`); `grep -rn TSC_JS docs test` → none.
  No surviving "stub-types: PASS" anywhere.
- **Line above the 57-row table** present at `brief:129-130`: "These are planned
  scope-behaviour cells, not .2a's own exit gate: .2a is the harness plus
  startup/transport controls (`generic-recipient-host.md:683`)." Table still 57 rows.
- **Publication section unchanged**: `brief:260-266` has zero hunks in the range diff;
  LOCAL ONLY, no push/no proposed push, fresh clean commit/tree on a public base,
  whole-range privacy check, private refs kept, local check ≠ publication approval.
- **Scope held.** `git diff --name-only` over the range = exactly four files
  (brief, checker, `bounded-gate.sh`, `typecheck.ts`). No production, CI
  (`.github/workflows/ci.yml` untouched), dependency, lockfile, `scope-cases.json`,
  `policy-shapes.json` or `stubs.ts` change. No new secrets or permission changes.

## Findings

### LOW — docs — `docs/design/f-host-2a-brief.md:270`: garbled sentence from the `INT-1` substitution

"- Exact the composed A+B+D integration release D/composed release SHA, tarball
hash/manifest and supported entries." The F2 scrub replaced `INT-1` in place, leaving
a sentence that reads as a substitution artifact and duplicates "release". The two
table cells at `:74-75` read correctly; only this bullet is malformed. Impact: the
"Open engineering inputs" list is the first thing a downstream implementer reads for
what is still blocking, and this line is the only one that is hard to parse.
Fix: "- Exact composed A+B+D integration release SHA, tarball hash/manifest and
supported entries."

### LOW — test — `test/fixtures/generic-recipient/check-scope-contract.mjs:12-13`: the F3 crash class is still open for `ci.yml`

The F3 fix moved five of six supplemental reads into `docsContract()`, but
`.github/workflows/ci.yml` is still read unconditionally at module load. Reproduced
in a mirror: with that file removed, the **default** mode dies on a raw
`node:fs:440` ENOENT stack trace, exit 1 — exactly the failure mode F3 was
chartered to eliminate. `ci.yml` (like the checker's own source at `:12`) is scanned
only by `docsContract()`; `--self-test` and default never use it, so the dependency
is gratuitous in those modes. Classification: **pre-existing at the range base**
(introduced by `e5184b31`, which is this delta's base), not a regression — but it is
the same defect class the round closed for the other five inputs, so closing it
costs one hunk. Fix: move the `import.meta.url` and `ci.yml` reads into the same
guarded loop at `:94-108`.

### LOW — test — `check-scope-contract.mjs:51,55`: `toolingEnvironment` adds no detection beyond `toolingCli`

`toolingEnvironment` matches `\b<stem>_[A-Z0-9_]+\b`, which is a strict subset of
`toolingCli` (`/<stem>/iu`): any `ORGASMIC_*` token already contains the stem. I
confirmed this by neutering the env rule alone — `--docs-contract` still fails, but
with a message-mismatch error ("Expected values to be strictly deep-equal"), i.e.
the control detects the rule's *absence*, not a missed token. So the "three
structural controls" are two independent detection rules plus one message
discriminator. Not a hole — nothing escapes — but the fix report's framing
("the tooling CLI name, its environment prefix ... independently") overstates
independence. Keep the rule for diagnostic clarity or fold it into the CLI rule;
either way the coverage claim should say two rules.

### LOW — test — `test/stubs/f-host-2a/bounded-gate.sh:10`: `mktemp -d` fallback leaks a directory per invocation

When `BOUNDED_LOG_DIR` is unset the wrapper creates a temp directory and never
removes it; each gate run leaves one `/tmp/tmp.XXXX/bounded-output.*.log` behind.
Deliberate in part — the raw log must survive for inspection, and `:20` prints its
path — but with the fallback now the default for any contributor without the private
variable, repeated runs accumulate. Fix direction: print the fallback path (already
done) and document that the caller owns cleanup, or trap-remove the directory only
when it was created by the fallback *and* the gate passed.

## Verification Gates

Reused receipts (read, not re-run), all exit 0 at `8dd238b5`, `clean=yes`:
`input-diff`, `input-probes`. I read both logs; `input-probes` records the five
pinned blob hashes, ancestry, 57 ordered rows, the zero-hit token grep, the five
wrapper exit classifications and `PENDING stub-types`.

Run by me at `8dd238b5` with `BOUNDED_LOG_DIR` set privately, each under the
60s/65536-byte bounded wrapper:

| Gate / probe | Command | Result |
| --- | --- | --- |
| `scope-self-test` | `bounded-gate.sh 60s node check-scope-contract.mjs --self-test` | exit 0 — 57 cases, 45 rejection controls, 4 oracle mutants killed, singleton negative control, naming-parser control. Log `gate-scope-self-test-01M67WGB5HACHBN0MEM5KWN206.log` |
| `docs-neutrality` | `bounded-gate.sh 60s node check-scope-contract.mjs --docs-contract` | exit 0 — `PARTIAL naming check`, 9 public files, 11 rejected controls, 0 injected names. Log `gate-docs-neutrality-01M67WGBAX17Z2MBR5EWD8C7CS.log` |
| `f3-control-recheck` | `bounded-gate.sh 60s bash f3-recheck.sh` (my own mirror harness) | exit 0 — baseline 0/0/0; all five files individually removed → default 0, self-test 0, docs-contract 1 naming the file, no PASS/PARTIAL/stack. Log `gate-f3-control-recheck-01M67WGG10N82CFEYNHQ73JZYP.log` |

Unwrapped scoped probes (no reusable receipt, read-only, tree left clean), scripts
retained in this evidence directory:

- `f3-recheck.sh` — the F3 mirror matrix above, plus the `ci.yml` control and three
  `bounded-gate.sh` environment controls (no private env → 0; too few args → 2;
  bad log dir → 2).
- `mutation-probe.sh` — neuters each of the three structural rules in a mirror
  (each → `--docs-contract` nonzero), injects each live token into a mirrored brief
  (each → docs-contract 1, self-test 0), and exercises PARTIAL with an empty,
  a valid nonempty and an invalid `SCOPE_FORBIDDEN_NAMES`.
- `git grep -n -i -E 'orgasmic|\bINT-[0-9]+\b' -- .` → exit 1, zero hits.
  Broader sweep for `S4Q8F|task-v8sb9|TASK-A821A|Soma|Tarn|Vasyl|tx-20261031` →
  zero hits; the only `/srv/` matches are long-standing unrelated test fixtures
  (`test/update-contract.test.ts`, `test/load-repair-alert.test.ts`, …), untouched
  by this range and outside the neutrality scan set.
- `git diff --name-only` / `--stat` over the range; `grep -rn TSC_JS docs test`.

`stub-types` was **not run** and is **not a gate this round** — PENDING per Soma.
I ran **no** compiler, borrowed or otherwise.

## Unmet Criteria / test gaps

- **`stub-types` remains PENDING** — correctly, by instruction. No typecheck evidence
  exists under the host's declared `typescript: ^7.0.2` closure, and this round
  deliberately produced none. The six TS7 surfaces at `brief:216-230` are unqualified.
- **F4's TS2542 claim is unvalidated by a compiler.** The `@ts-expect-error` mechanism
  accepts *any* diagnostic at the site; the specific code is pinned only by my static
  argument above, not by a compiler run. This is the directed state, not a defect.
- **Neutrality coverage is still PARTIAL** and correctly self-reports as such. The
  three new rules close the specific class F2 found; any new private vocabulary
  outside `{stem, stem_ENV, INT-n, path, TASK/RUN/AGENT/PROJECT-id, UUID, credential}`
  still needs the full private naming policy at publication time.
- No integration, production-path, native, store or live evidence exists anywhere in
  this slice — as designed and disclosed. The 57 rows remain *planned* tests.
- The fix report's gate list is accurate against the receipts I read; its claim that
  the three structural rules act "independently" is the one overstatement (LOW 3).

## Residual Risk

- The TS7 qualification gap is unchanged and is the only substantive open technical
  risk in the stubs. The four `@ts-expect-error` directives remain the likeliest
  failure point, since an *unused* directive is itself a compile error; F4's rewrite
  swaps one brittle site for a different brittle site, not for a safe one.
- `ci.yml` (LOW 2) keeps a frozen-contract gate coupled to a file outside its concern;
  if the checker is ever run from a docs-only export it crashes on a stack trace.
- The wrapper now prints its raw-log path to stdout (`bounded-gate.sh:20`). When
  `BOUNDED_LOG_DIR` points at private evidence, that machine-local path appears in
  gate output. Harmless while output stays in private evidence; it must not be pasted
  into tracked prose, where the `machine-local path` rule would (correctly) reject it.
- **LOCAL ONLY ancestry hold is intact and remains the containing control.** The F2
  scrub cleans new files only; publication still requires a fresh clean commit/tree on
  a public base plus a whole-publication-range privacy check, with private refs kept.
  Nothing in this round is a publication approval.
- The composed A+B+D integration release and the reviewed scope-plumbing head remain
  genuinely blocking for any .2a implementation; this round does not pre-empt them.

## Fix Directions

1. (LOW 1) Rewrite `brief:270` to "- Exact composed A+B+D integration release SHA,
   tarball hash/manifest and supported entries."
2. (LOW 2) Move the `import.meta.url` and `ci.yml` reads from `check-scope-contract.mjs:12-13`
   into the guarded loop at `:94-108`, so default/`--self-test` depend on nothing but
   `scope-cases.json` and the contract document.
3. (LOW 3) Either fold `toolingEnvironment` into `toolingCli` or keep it purely as a
   message discriminator, and state two independent rules rather than three in the
   next report.
4. (LOW 4) Decide and document ownership of the `mktemp -d` fallback directory in
   `bounded-gate.sh`.
5. Unchanged from the prior round: close `stub-types` only with the host's declared
   toolchain/closure in an isolated code-only setup, recording version *plus* the
   declaration it satisfies, and re-verify F4's TS2542 there — not with a borrowed
   or acquired compiler.

None of items 1–4 needs to land before this input is consumed.
