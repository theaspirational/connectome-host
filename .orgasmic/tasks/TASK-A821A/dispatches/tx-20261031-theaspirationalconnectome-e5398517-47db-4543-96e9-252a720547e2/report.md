# Fix-round report — F-host.2a INPUT

## Changed

Final local commit: `8dd238b5cb5b18c89d64512c1cac8d439ffdc2c4`; clean worktree.
Base: `e5184b31a1dc077ac1ef0cfec6841866a4ec4f9a`. Four concern-scoped commits; no push.

Review read in full: `/srv/review-drop/TASK-A821A-REVIEW1.md`.
Verified SHA-256:
`d828893296ca5c45182c95124160f30d91aabc0a80de81552ccd003603d851a1`.
Task operator: Tarn. Constraints: Soma (programme qualifier); not decisions by
owner Vasyl. This report also handles the F3/F4/F1 clarification from Tarn:
normal docs validation fails closed on missing stubs, and compiler qualification
remains PENDING without acquisition.

### Fixes

- **F2:** removed tooling CLI/environment and programme integration identifiers
  from tracked inputs. The wrapper uses `BOUNDED_LOG_DIR` or `mktemp -d` fallback;
  docs show bare wrapper commands. Structural neutrality rules reject the tooling
  CLI name, its environment prefix and numbered integration identifiers independently
  of the optional naming list, with three new rejection controls. No scan exemptions.
- **F3:** supplemental .2a reads now occur only in `docsContract()`. Missing or
  unreadable required files produce nonzero status and a named diagnostic, no
  exception stack. No skip/PARTIAL/PASS on missing files. Default and self-test
  modes remain independent of these five files. Controlled mirror tests confirm.
- **F4:** compile-only assertion now uses the readonly object branch explicitly,
  not the nullable/primitive/array union. It targets TS2542. **Actual diagnostic
  validation on the declared compiler remains PENDING; no typecheck run here.**
- **F1:** brief and this report classify `stub-types` as **PENDING**. The previous
  borrowed TS 5.9.3 result is **EXPLORATORY**, not PASS on the host's declared
  `typescript: ^7.0.2` dependency closure. Later validation must match the
  declaration's major and record actual version plus declaration and closure.
  All six reviewer TS7 risk surfaces are retained in the brief. No compiler
  acquisition, borrowing from another worktree, symlink, or compatibility edit
  to the stub declarations occurred this round.
- Added an explicit line above the 57 rows: planned scope-behaviour cells are not
  .2a's exit gate; .2a remains harness plus startup/transport controls at frozen
  `docs/design/generic-recipient-host.md:683`.

Changed files:
- `docs/design/f-host-2a-brief.md`
- `test/fixtures/generic-recipient/check-scope-contract.mjs`
- `test/stubs/f-host-2a/bounded-gate.sh`
- `test/stubs/f-host-2a/v0/typecheck.ts`

No production, CI, permissions, dependency, lockfile, fixture, policy-shapes or
secret changes. `stubs.ts` and its tsconfig remain unchanged; only the requested
compile-only test assertion changed. No harness implementation or native/live run.

### Commits

- `39b8d795e334c8cea570af975c48edb51f4629f9`: F2 neutral tooling and structural rejection.
- `a8bf5dc3a1bf7eea2d1ef5cb8cee3087bcf6dbd7`: F3 lazy fail-closed supplemental reads.
- `4205e0a3d34baad80178743dd381347a52a1325b`: F4 readonly object-branch assertion.
- `8dd238b5cb5b18c89d64512c1cac8d439ffdc2c4`: F1 pending qualification and table framing.

All have author and committer Vasyl Dovhanchyn
<48733736+theaspirational@users.noreply.github.com> and trailer
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.

## Verification Gates

All five assigned gates freshly ran at the final clean commit, exit 0 and recorded
reusable. No earlier gate pass was assumed or reused. `stub-types` was **not run**
and is **not a gate in this round**.

Bound for each gate: `timeout --signal=TERM --kill-after=60s`, 60-second deadline,
65536-byte output cap; deadline/124, kill/137, SIGPIPE/141 and cap/125 are
INCONCLUSIVE. Maximum timeout-plus-grace is 120 seconds. Set the neutral log
location privately for the exact commands below:

```sh
export BOUNDED_LOG_DIR="$ORGASMIC_EVIDENCE_DIR"
orgasmic dispatch gate scope-self-test -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test
orgasmic dispatch gate docs-neutrality -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract
orgasmic dispatch gate f3-control -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node "/home/orgasmic/.orgasmic/runs/run-01M67W4JC6WTSCP6Q62QW7KBY1/evidence/f3-control.mjs"
orgasmic dispatch gate input-diff -- bash test/stubs/f-host-2a/bounded-gate.sh 60s git diff --check e5184b31a1dc077ac1ef0cfec6841866a4ec4f9a HEAD
orgasmic dispatch gate input-probes -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node "/home/orgasmic/.orgasmic/runs/run-01M67W4JC6WTSCP6Q62QW7KBY1/evidence/input-probes.mjs"
```

- **scope-self-test:** PASS, exit 0: 57 cases, 45 rejection controls, four named oracle mutants, singleton negative control and naming-parser controls.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67W4JC6WTSCP6Q62QW7KBY1/evidence/gate-scope-self-test-01M67W8A5FTTXVZ4PFDQXVJFD9.log`
- **docs-neutrality:** PASS with explicit PARTIAL naming coverage, exit 0: nine files, 11 rejected controls (including three new structural identifier controls); no injected naming list.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67W4JC6WTSCP6Q62QW7KBY1/evidence/gate-docs-neutrality-01M67W8AAR9Z7K0DEQ9SX4XN3E.log`
- **f3-control:** PASS, exit 0: baseline mirror succeeds; each of all five supplemental files individually removed. For each, --self-test and default exit 0; --docs-contract exits 1 naming the file, without stack trace, PARTIAL claim or docs-contract PASS. Mirrors removed afterward.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67W4JC6WTSCP6Q62QW7KBY1/evidence/gate-f3-control-01M67W8AFT7NZAX7P5BQASQ3DN.log`
- **input-diff:** PASS, exit 0: whitespace check across the entire fix-round range.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67W4JC6WTSCP6Q62QW7KBY1/evidence/gate-input-diff-01M67W8AY50EW68E3WD9V7D6WR.log`
- **input-probes:** PASS, exit 0: five unchanged pinned blob/current-file hashes; ancestor check; all 57 table rows in exact order and matching tuples/destination classes/labels; zero tracked tooling/programme-token grep hits; no production stub references; cap/125, deadline/124, 137 and 141 remain INCONCLUSIVE, ordinary failure/3 preserved; neutral fallback works without private environment.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67W4JC6WTSCP6Q62QW7KBY1/evidence/gate-input-probes-01M67W8B37FV0RP2S25GVG25XV.log`

Private evidence-only helpers `f3-control.mjs` and `input-probes.mjs` are retained
alongside their bounded raw logs. The F3 mirror is generated synthetic text/JSON
only, no dependencies or runtime state. Expected missing-file failures are
asserted controls, not failed validation gates.

Exact tracked-token check reproduced by input-probes:

```sh
git grep -n -i -E 'orgasmic|\bINT-[0-9]+\b' -- .
```

Exit 1, empty output = zero hits. Word boundaries avoid the unrelated `print-1`
package-lock substring. The CLI-name check also covers the environment prefix.

### Frozen input hashes reverified this round

| File | SHA-256 |
| --- | --- |
| host design at `a316200c4781a844d6109a6f8c812c3be99e20b0` | `05382cd6f08874e472143534ee7cf058bbd3ebdce8aad2442abef1ceef0b0d59` |
| T1 contract at `bff8c46e3a4c403f62b738cead317863d43fca9c` | `64559f3dee6550921ecfd227c2f7ef5d071d6b23ec174812fb1647c1cb4c9d56` |
| scope-cases at T1 pin | `70a1827c21722b6056ba209928434877f4d4ea729338793c4d95a9e5c5126381` |
| policy-shapes at T1 pin | `b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed` |
| shared fixture specification at T1 pin | `b5915cdd5b6d68241312e755206e65f0709cf81b394ac3a8df42d883f217aa0b` |

Each pinned blob and current file independently matches. `git merge-base` still
returns the full host pin, confirming ancestry.

### Inherited evidence and corrected claims

The original full design/T1/fixture reads and source-derived .2a definition remain
applicable because those exact five files are unchanged and their hashes were
freshly checked. Opaque stub declarations/tsconfig and A/B pin statements are
unchanged. The reviewed acceptance-table analysis remains relevant; the current
input-probes freshly confirms all 57 rows. Baseline/project instructions have not
changed; no startup rescan was needed.

Previous docs/oracle/probe receipts are historical only, not reused for modified
inputs. **Earlier `stub-types: PASS` is explicitly superseded: PENDING /
EXPLORATORY TS 5.9.3 only.** The reviewer's borrowed TS7 probe likewise does not
close the declared dependency-closure gate. The prior claim that no internal
names were present was wrong (F2); this round removes them and adds structural
checks plus a fresh empty grep result.

## Unmet Criteria

None for this narrowed fix round. The original declared-toolchain stub typecheck
remains **PENDING**, deliberately excluded from this round's gates. F4's intended
TS2542 diagnostic is not claimed compiler-verified. The complete private naming
policy is absent; docs-contract correctly reports **PARTIAL**, exit 0, while
structural path/id/credential/tooling checks still run.

## Residual Risk

- Six TypeScript 7 compatibility surfaces remain unqualified until an authorized
  host-declared closure exists: directive diagnostics, unique-symbol readonly
  branding, recursive readonly data, abstract generic ambient binder, bundled DOM
  AbortSignal and native-port module/lib/type resolution. No compiler was acquired,
  invoked, borrowed or symlinked this round.
- The composed A+B+D integration release and reviewed scope-plumbing artifact
  remain pending; no integration/production/native/storage evidence is implied.
- New identifiers outside the structural classes still require the full supplied
  naming policy. PARTIAL is not publication approval.
- **LOCAL ONLY** history descends from the private line. No push or proposed push.
  Later public submission requires a fresh clean commit/tree on a public base,
  whole-publication-range privacy review and retained private refs; cleaning new
  files does not sanitize ancestry. The publication section is unchanged.
- No owner permission/recovery/cutover decision was taken. No source/CI permission
  change or new repo secret was introduced.
