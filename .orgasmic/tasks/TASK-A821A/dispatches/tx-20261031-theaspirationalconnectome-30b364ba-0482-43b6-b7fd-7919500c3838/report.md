# F-host.2a implementation-input report

## Changed

Completed the INPUT only, not slice .2a implementation. Final local commit:
`e5184b31a1dc077ac1ef0cfec6841866a4ec4f9a` (clean worktree). Task operator: Tarn; engineering constraints and
publication guard: Soma, programme qualifier. No decision was made by owner
Vasyl or requested of the owner.

### Definition and boundary

At `a316200c4781a844d6109a6f8c812c3be99e20b0`,
`docs/design/generic-recipient-host.md:683` defines .2a:
“Synthetic peer/harness and real startup/transport controls; final AF harness
consumes the integration tarball.” Lines 668–673 reference the normative shared
specification. `docs/design/generic-recipient-fixtures.md:13–53,76–92` supplies
transports, barriers and ordered observations. T1 §4:296–339 supplies only the
pending local registration-view/projector shape. T3/T4/T5 behavior is explicitly
not charged to a new .2a implementation here.

Files changed:
- `docs/design/f-host-2a-brief.md`
- `test/fixtures/generic-recipient/check-scope-contract.mjs`
- `test/stubs/f-host-2a/bounded-gate.sh`
- `test/stubs/f-host-2a/v0/stubs.ts`
- `test/stubs/f-host-2a/v0/tsconfig.json`
- `test/stubs/f-host-2a/v0/typecheck.ts`

The brief has all 57 fixture case ids with exact expectation tuples, destination
references, planned test labels and mutants; names clearly denote planned tests,
not runtime receipts. Declaration-only v0 stubs have opaque identities, abstract
admission/denial types and no concrete invented AF packaging. Type tests are
compile-only and never executed. The existing T1 checker now scans nine input
surfaces; naming policy and missing-policy PARTIAL semantics are unchanged.
No fixture, policy-shapes, production, dependency, lockfile or workflow changes.
No caches or generated build files; raw bounded gate logs are evidence-only.
No applicable AGENTS.md found; supplied baseline and CONTRIBUTING.md read;
conventions/gotchas supplied as none. No changelog required for docs/test-only work.

### Local commits (per concern)

- `af6759689211355c231c2480412be28fb62a0642`: bounded brief and acceptance input.
- `4255c6ee534ae145539c405bb24bb2907db88ed9`: v0 stubs/type tests and bounded gate support.
- `e5184b31a1dc077ac1ef0cfec6841866a4ec4f9a`: neutrality scan extension.

Each author/committer is Vasyl Dovhanchyn
<48733736+theaspirational@users.noreply.github.com>, with the required
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>` trailer.

### Pins and hashes (independently reproduced)

`git merge-base a316200c4781a844d6109a6f8c812c3be99e20b0 bff8c46e3a4c403f62b738cead317863d43fca9c`
returned `a316200c4781a844d6109a6f8c812c3be99e20b0`.

| File | Pin | SHA-256 |
| --- | --- | --- |
| host design | `a316200c4781a844d6109a6f8c812c3be99e20b0` | `05382cd6f08874e472143534ee7cf058bbd3ebdce8aad2442abef1ceef0b0d59` |
| T1 contract | `bff8c46e3a4c403f62b738cead317863d43fca9c` | `64559f3dee6550921ecfd227c2f7ef5d071d6b23ec174812fb1647c1cb4c9d56` |
| scope-cases | same T1 pin | `70a1827c21722b6056ba209928434877f4d4ea729338793c4d95a9e5c5126381` |
| policy-shapes | same T1 pin | `b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed` |
| shared fixture specification | same T1 pin | `b5915cdd5b6d68241312e755206e65f0709cf81b394ac3a8df42d883f217aa0b` |

The input-probes gate re-hashes both pinned blobs and current unchanged files.

AF A: `869bb3add7b48893d48b2ce50ea423e4b33fdfbc`.
AF B: `29edcf8b7f7b31d0ab35f59ab0e6aab75c0f4e04` (authorize required).
D and composed artifact: PENDING (INT-1).
AF opt-in registration-scope view: PENDING reviewed S4Q8F head and rebuilt
composed artifact; no unreviewed head used as a consumption pin.

## Verification Gates

All gates ran through dispatch at the clean final commit above, exit 0 and
recorded reusable. Evidence level: **STUB, not integration evidence**.
Each uses 60s deadline, `timeout --signal=TERM --kill-after=60s`, 65536-byte cap;
124/137/141 and cap/125 classify INCONCLUSIVE, never PASS. No full suite or
production-path behavior claim; no live/native/store/network execution.

Exact environment-resolved compiler (pre-existing, read-only use; no install):
`TSC_JS=/home/orgasmic/.orgasmic/worktrees/orgasmic/task-v8sb9/ui/node_modules/typescript/lib/tsc.js`. TypeScript 5.9.3.
- `tsc.js` SHA-256 `2cffde0b8c6760dfb0b5b0382bbb7e00ba6a8b2d981b9205b256a700a481d983`
- `_tsc.js` SHA-256 `e8f349eabd48486bdb2bf9dc1a00c89d58297270c54b745838879e2859194419`
- `typescript.js` SHA-256 `3ae902c92cc44dace175c0e69e13a4b0899f6983c6121d76b9ab8dd5795e7675`

Exact commands (repository root):

```sh
orgasmic dispatch gate stub-types -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node "/home/orgasmic/.orgasmic/worktrees/orgasmic/task-v8sb9/ui/node_modules/typescript/lib/tsc.js" -p test/stubs/f-host-2a/v0/tsconfig.json
orgasmic dispatch gate scope-self-test -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test
orgasmic dispatch gate docs-neutrality -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract
orgasmic dispatch gate input-diff -- bash test/stubs/f-host-2a/bounded-gate.sh 60s git diff --check bff8c46e3a4c403f62b738cead317863d43fca9c HEAD
orgasmic dispatch gate input-probes -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node "/home/orgasmic/.orgasmic/runs/run-01M67V7D9Z9PVK724JFM32R2DK/evidence/input-probes.mjs"
```

- **stub-types**: PASS; positive shape checks and four expected compile errors, no emit. Log: `/home/orgasmic/.orgasmic/runs/run-01M67V7D9Z9PVK724JFM32R2DK/evidence/gate-stub-types-01M67VEKFE8M56X5HRX3T3C882.log`
- **scope-self-test**: PASS; 57 cases, 45 rejection controls, four named oracle mutants killed, singleton negative control and naming parser controls. Log: `/home/orgasmic/.orgasmic/runs/run-01M67V7D9Z9PVK724JFM32R2DK/evidence/gate-scope-self-test-01M67VEM11VS7KNXG0ERWHPW6Q.log`
- **docs-neutrality**: PASS with explicit PARTIAL naming check; nine files, eight rejection controls, zero injected names. SCOPE_FORBIDDEN_NAMES absent. Full naming qualification NOT claimed. Log: `/home/orgasmic/.orgasmic/runs/run-01M67V7D9Z9PVK724JFM32R2DK/evidence/gate-docs-neutrality-01M67VEM6DMEYZYH8FW5R8NWN3.log`
- **input-diff**: PASS; whitespace/diff check across all three commits. Log: `/home/orgasmic/.orgasmic/runs/run-01M67V7D9Z9PVK724JFM32R2DK/evidence/gate-input-diff-01M67VEMBF7VEVV3Y493NGBGQC.log`
- **input-probes**: PASS; all five input hashes unchanged, ancestor confirmed, 57 table rows matched, no production source stub references. Cap/125 and timeout/124 exercised; synthetic 137/141 exits verified inconclusive classification. Log: `/home/orgasmic/.orgasmic/runs/run-01M67V7D9Z9PVK724JFM32R2DK/evidence/gate-input-probes-01M67VFBDWEX4V2FE85VHTERA3.log`

Raw bounded-output logs named by each receipt remain alongside the logs. The
private `input-probes.mjs` helper is evidence-only, not a shipped harness/API.
No regression or failed gate. Deliberate wrapper negative controls were expected
inconclusive child results; the probe gate checks their classification explicitly.

## Unmet Criteria

None for this bounded INPUT task. Actual harness implementation and integration
qualification are deliberately not performed. No full naming-policy claim can
be made without the complete gate-provided environment list; mandated missing-
policy behavior (PARTIAL, exit 0) was observed.

## Residual Risk

- INT-1 full D/composed SHA, tarball/SRI/manifest and source/compiled entries remain
  pending; reviewed S4Q8F scope plumbing and rebuilt composed release remain pending.
- Final scope-projector packaging, denial representation and authenticated scope-
  producer evidence format are engineering inputs, not silently frozen by stubs.
- Real startup/transport, native exact-root/recursive/cycle witnesses, old/new
  storage parity, D effects, typing and recovery remain unqualified. Scope snapshot
  expectations are not storage receipts; no integration evidence inferred.
- Missing SCOPE_FORBIDDEN_NAMES limits neutrality evidence to PARTIAL. New tracked
  inputs contain no operator/qualifier/internal names or machine-local paths/hosts;
  private tool paths exist only in this evidence report.
- Publication guard from Soma via Tarn is honored and recorded in the brief:
  **LOCAL ONLY** history. No push performed or proposed. Any later public submission
  requires a fresh clean commit/tree on a public base, a privacy check of the entire
  publication range, and retention of the private refs. Scrubbing new files does
  not sanitize ancestry; this gate is not publication approval.
- Existing owner questions in frozen host §4 remain unchanged; no permission,
  recovery, behavior or activation decision was taken.
