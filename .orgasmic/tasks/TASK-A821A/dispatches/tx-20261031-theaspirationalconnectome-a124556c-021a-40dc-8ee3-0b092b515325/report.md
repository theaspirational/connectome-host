# LOW-only fix-round report

## Changed

Final local commit: `8e92bfbe30d141140531b170e9ef0421c6a16cad` (clean worktree).
Base: `8dd238b5cb5b18c89d64512c1cac8d439ffdc2c4`.
Task operator: Tarn; constraints: Soma. No owner decisions made.

Read `/srv/review-drop/TASK-A821A-FIX1-REVIEW.md` in full and verified SHA-256
`5dec2e351f86575764af753172949bf67024d638176af743ea6f8ce27b8a5747`.

Exactly two tracked files changed:
- `docs/design/f-host-2a-brief.md`: repaired the open integration-release bullet;
  documented caller ownership of fallback-log cleanup; corrected structural-rule
  coverage wording. No table, pins, scope or publication-policy changes.
- `test/fixtures/generic-recipient/check-scope-contract.mjs`: moved the checker-source
  and CI-workflow reads into the existing guarded `docsContract()` loop. Default
  and self-test modes no longer read either; docs mode retains both required inputs
  and fails nonzero with a named diagnostic when missing/unreadable.

Detection coverage is **two independent detection rules plus a message
discriminator**: `toolingEnvironment` is a subset of `toolingCli`; it selects a
specific message, not an additional independently detected class. The other
independent rule detects numbered programme integration identifiers. No detection
logic, structural patterns, controls, PARTIAL semantics or wrapper logic changed.
Any token in the controls is a **synthetic lexical marker only**, not a real
credential or secret.

The `mktemp -d` fallback intentionally leaves a directory containing the raw log
for inspection. The path is printed; **cleanup is the caller's responsibility**.
The brief now explicitly documents this without changing cleanup behavior.

Commits, each with the required Vasyl noreply author/committer and
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>` trailer:
- `55961528`: documentation clarifications.
- `8e92bfbe`: guarded checker-source/workflow reads.

No production, CI-file, permission, stub, fixture, dependency, lockfile, secret or
harness implementation change. No compiler run/acquisition/borrowing/symlinks.
No push or proposed push.

## Verification Gates

All four assigned gates freshly ran at the final clean commit and were recorded
reusable, exit 0. No earlier pass was implied or reused.

Bounds unchanged: 60s deadline under `timeout --signal=TERM --kill-after=60s`,
65536-byte cap; exits 124/137/141 and cap/125 remain INCONCLUSIVE, never PASS.
Exact commands from repository root, using this round's private evidence directory:

```sh
export BOUNDED_LOG_DIR="$ORGASMIC_EVIDENCE_DIR"
orgasmic dispatch gate scope-self-test -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test
orgasmic dispatch gate docs-neutrality -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract
orgasmic dispatch gate missing-input-control -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node "/home/orgasmic/.orgasmic/runs/run-01M67WRSBCZG2VARTM1KWXE9Y7/evidence/missing-input-control.mjs"
orgasmic dispatch gate input-diff -- bash test/stubs/f-host-2a/bounded-gate.sh 60s git diff --check 8dd238b5cb5b18c89d64512c1cac8d439ffdc2c4 HEAD
```

- **scope-self-test:** PASS exit 0: 57 cases, 45 rejection controls, four named oracle mutants, singleton negative control, naming-parser controls.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67WRSBCZG2VARTM1KWXE9Y7/evidence/gate-scope-self-test-01M67WT2046HA2DSQMC46P0HXJ.log`
- **docs-neutrality:** PASS exit 0, explicitly PARTIAL: nine public files, 11 rejected controls, zero injected names. Missing naming policy does not claim full coverage.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67WRSBCZG2VARTM1KWXE9Y7/evidence/gate-docs-neutrality-01M67WT25DZFJ2BHVZQB2HAP4P.log`
- **missing-input-control:** PASS exit 0: complete mirror 0/0/0; each of all seven scanned optional-to-oracle inputs removed individually gives default 0, --self-test 0, --docs-contract 1 with the named file, no stack trace, no PARTIAL/docs-contract PASS.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67WRSBCZG2VARTM1KWXE9Y7/evidence/gate-missing-input-control-01M67WT2AWR6Y42AT2077E5G0J.log`
- **input-diff:** PASS exit 0: entire LOW-only commit range passes whitespace checking.
  Log: `/home/orgasmic/.orgasmic/runs/run-01M67WRSBCZG2VARTM1KWXE9Y7/evidence/gate-input-diff-01M67WT368DKB8DTXD6ZCMVB4G.log`

The retained evidence-only helper `missing-input-control.mjs` builds and removes a
synthetic temporary mirror. It tests all seven docs-only reads: brief, stubs.ts,
typecheck.ts, tsconfig.json, bounded-gate.sh, ci.yml and the checker source.
For the source case, an existing Node 22.23.3 synchronous load hook reads the real
mirror entry point then unlinks it **before evaluation**. Thus Node can execute the
already-loaded source while its guarded self-read fails as intended. This avoids
confusing an interpreter entry-point failure with the checker diagnostic.
No source mutation or real credentials are needed for that control. All missing
cases assert nonzero docs validation, named diagnostics and absence of stack trace,
PARTIAL and docs-contract PASS. Raw bounded logs are retained beside the receipts.

### Inherited evidence and scope

The original full host/T1/fixture reads and reviewed .2a definition remain context:
`docs/design/generic-recipient-host.md:683` at the frozen host pin defines the
harness plus real startup/transport controls. The frozen documents, scope-cases,
policy-shapes, 57 table rows, stubs, tsconfig, package declaration and bounded
wrapper are unchanged from the round base (the diff contains only the two files
listed above). Previously reproduced hashes and static reviews remain applicable
to those unchanged inputs, not new hash/gate receipts from this round.
No previous gate was reused for the modified checker/brief; assigned checks were
rerun. The current 57-case oracle self-test is fresh. The LOW review's rule-overlap
finding corrects the prior report's independence overstatement.

## Unmet Criteria

None for this LOW-only round. **F1 / stub-types remains PENDING** by instruction,
not a gate here: no qualification under the host's declared TypeScript `^7.0.2`
closure. Earlier borrowed 5.9.3 evidence remains EXPLORATORY only. The readonly
TS2542 assertion is not claimed compiler-validated.

## Residual Risk

- Full naming coverage still needs the private supplied policy; current result is
  correctly PARTIAL. Structural rules cover their specified classes only.
- Compiler compatibility and the six documented TS7 risk surfaces remain open.
- The composed A+B+D integration release and reviewed scope artifact remain pending;
  no integration, production/native/store/live evidence is implied.
- Fallback log directories survive until callers clean them, deliberately documented.
- **LOCAL ONLY** ancestry hold remains intact. No push. Any later public submission
  requires a fresh clean commit/tree on a public base, privacy review of the entire
  publication range and retained private refs. This work is not publication approval.
