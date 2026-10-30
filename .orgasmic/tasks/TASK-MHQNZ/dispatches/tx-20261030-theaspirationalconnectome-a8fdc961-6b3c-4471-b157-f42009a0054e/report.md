# TASK-MHQNZ Phase P revision 5 — bounded delta review

Scope: `git diff bac2ebb2..1cfe32a1` (7 files, all `test/store-probe/`), the implementer
report `/srv/review-drop/TASK-MHQNZ-PHASEP-R5.md`, the r4 review
`/srv/review-drop/TASK-MHQNZ-R4-DELTA-REVIEW.md` and the reviewed evidence dir
`run-01M67FZBDAY9S8PY6V45EEXF07/evidence`. Read-only: no Phase E, no containers started,
no gate re-runs. Worktree left clean at `1cfe32a1` (`git status --porcelain` empty).

## Verdict

**APPROVE.**

**HIGH-1 is closed, and closed correctly.** The bulk walk/lstat/full-tree hash is gone from
the tracee; it now runs in the untraced parent, bound to the externally anchored manifest,
immediately before every child spawn, and refuses before `spawn`. The child keeps every r4
assertion that mattered and now *compares* its leaf hashes against pinned manifest records
instead of merely logging them — verified empirically in a real container for all four
runtime×tree combinations. LOW-1..3 are fixed with selftests. The envelope, limits,
privileges, binds, 18 cases, 42 children, 1800 s/30 s deadline and 256/192 MiB caps are
unchanged; no scope creep (`launch.sh`, `Containerfile`, `runtime.mjs` are not in the diff).

One MEDIUM and four LOWs below. None blocks Phase E approval: every one of them lands on
INCOMPLETE/NOT-PASS, never on a false compatibility pass. The MEDIUM is about *wasting* the
Phase-E authorization, which is exactly the class of problem r4's HIGH-1 was.

## Point-by-point against the brief

### 1. HIGH-1 closed — CONFIRMED

- **No bulk walk/lstat/hash in the tracee.** `worker.mjs` no longer contains `verifyTree`,
  `verifyCriticalInputs`, `readdirSync` or `lstatSync` (diff removes `worker.mjs:21-41` of
  `bac2ebb2`; `selftest.mjs:170` asserts the absence over the source text). The only file
  bytes the child reads are: the anchored manifest (`worker.mjs:9`), its own executable
  (`:57`), four `package.json`s (`:66`), four entries + five transitive entries (`:70`, `:82`)
  and one native payload (`:86`) — 15 files, not 10,628.
- **Verification is in the UNTRACED parent, before each child.** `probe.mjs:70-71` calls
  `verifyInputTree(approvedManifest, tree)` and only then `spawn` at `:72`. It verifies the
  tree *that child uses*: `tree` is the same variable interpolated into `/inputs/${tree}`
  at `:67` and into `runtimeSpec(runtime, tree)` at `:61`. Initial whole-tree verification of
  both trees at `:141`, one summary frame at `:157`.
- **Bound to the external manifest.** `verifyInputTree` (`verify-inputs.mjs:34-41`) resolves
  `manifest.inputs[old-runtime|new-runtime]` from the manifest that `trustedManifest`
  (`verify-inputs.mjs:10`) accepted only after matching `PROBE_MANIFEST_SHA256` — an env
  anchor outside `/inputs`. `verifyPinned:19-28` checks the file *list*, bytes, size and mode
  bits, and symlink targets. The old tree is rooted at `/inputs/old/node_modules`
  (`verify-inputs.mjs:37`), matching the only bind for that tree
  (`container-inspect.json`: `/inputs/old/node_modules`, RO).
- **Mismatch refuses before spawn.** `verifyPinned` throws; the throw propagates out of
  `runChild` and out of `main`'s loops (the `continue` guards at `probe.mjs:177,179` are on
  `exitStatus`, not on throws), reaching `probe.mjs:219-223` → `fatal` +
  `final('fatal-inconclusive')` → nonzero exit → `checkCompleteness` (`stream.mjs:78`) rejects
  any status other than `complete`. Fail-closed. `selftest.mjs:168` covers the tamper case
  (`/input mismatch/`).
- **Are the "before each attempt" claims honest?** Yes, *for the input trees*, which is what
  the README r5 section and the report say. See LOW-3 for what is no longer re-checked per
  child.

### 2. Tracing starts before the SDK imports / store access — CONFIRMED

`probe.mjs:65-67` builds `timeout … productionTransport(['/tools/strace' … spec.executable,
…flags, '/harness/worker.mjs', …])`, i.e. strace execs the runtime itself, so the whole worker
— including its top-level `import` of node builtins and `./verify-inputs.mjs`, the anchored
manifest read at `worker.mjs:9` and the dynamic `./runtime.mjs` import at `:11` — is traced.
The SDK imports are at `worker.mjs:93` (chronicle), `:100` (CM), `:106-107` (AF/Membrane),
all strictly after the trace begins. **No import-time effect is hidden**: everything the child
executes is downstream of the strace exec, and the only untraced work is the parent's
verification and the parent's `inventory()` of the store clone (`probe.mjs:194,198,203`),
which was always host-side evidence, not tracee behaviour.

**The `prep-assertions` branch (`worker.mjs:87-91`) cannot come into play in the 18 cases.**
`operation` is only ever the literals `'write'`, `'verify'` and `'attempt'`
(`probe.mjs:176,178,197`); it is a positional argument built inside `runChild`, not read from
env or argv. `probe.mjs` accepts only `--phase-e-approved`, `--section` and `--af-tarball`,
and the declared execution intent is pinned to exactly
`['/runtime/node/node','/harness/probe.mjs','--phase-e-approved','--section','native']`
(`launch-argv.mjs:20`, cross-checked against `Config.Cmd` at `:37`). There is no flag or env
path that reaches `prep-assertions` in production. Its only caller is
`measure-leaf-control.mjs:16`, a prep-only measurement against the deliberately nonexistent
`/work/NO-STORE-NEVER-OPENED`, and `selftest.mjs:171` pins the branch to source-order *before*
the chronicle import.

### 3. Child checks vs the pinned manifest — CONFIRMED, with one evidence-fidelity nit

Retained and now manifest-bound: executable path + hash + version (`worker.mjs:55-57`),
conditional-entry resolution and entry hash (`:68,70`), top-level versions (`:72-75`),
transitive resolution and hashes (`:77-84`), native payload hash (`:86`). `pinnedHash`
(`:23-28`) looks the path up in the anchored manifest's records and **throws on absence or
mismatch** — r4 only logged `hash(entry)`. So on this axis r5 is *stronger*, not weaker.

Verified against real evidence, not just by reading: `measure-leaf-control.results.json`
contains, for node/old, node/new, bun/old and bun/new, `runtime-verified` + 4 `resolved` + 5
`transitive-resolved` + `native-payload` + `finished{prepOnly:true}` with exit 0, and I
re-checked every `native-payload` digest against `input-manifest.json` — all four present in
the manifest and hash-matching. The old/new relative-path mapping in `pinnedHash:24`
(old → relative to `tree/node_modules`, new → relative to `tree`) matches the manifest record
paths (`old-runtime`: `@animalabs/…`; `new-runtime`: `node_modules/@animalabs/…`), confirmed
by that run.

**What was genuinely dropped from the child vs r4:** per-file *mode* bits and symlink-target
checks over the whole tree (now parent-side, `verifyPinned:28`), and `verifyCriticalInputs`
(now parent-only, see LOW-3). Both are deliberate and disclosed; under the stated trusted-host
boundary they are equivalent. Nothing else weakened.

### 4. Budget model — arithmetic honest; label honest; filter rule terminal; deadline NOT tight

- **Arithmetic re-derived independently and it checks out.**
  `12·582500 + 9·582239 + 12·300910 + 9·313002 = 18,658,089` = `baselineAll42FramedBytes`;
  `4 · 18,658,089 + 16,777,216 = 91,409,572` = `estimatedAll42Bytes` (87.17 MiB);
  `201,326,592 − 91,409,572 = 109,917,020` = `marginBytes`. The child allocation
  `{(node,old):12,(node,new):9,(bun,old):12,(bun,new):9}` is asserted to sum to the manifest's
  42 (`budget-check.py:31-32`) and matches the real plan (per runtime×route: 2 seeds + 2
  reopens old/new + 3 clone attempts → 4 old-tree and 3 new-tree children ×3 routes).
  `budget-check.py:11-23` additionally re-verifies all four auxiliary chains frame-by-frame
  against the production framing. `tracedBulkVerificationLinesPerChild: 0` is a true statement
  about the committed code.
- **"4× + 16 MiB" is labelled an allowance, not a bound.** `budget-check.py:35-38` and the
  emitted `assumption`/`qualification` strings say so explicitly ("modeled, NOT measured or
  upper-bounded", "estimate is not experiment evidence"), and `README.md` repeats it. Honest.
- **The 192 MiB filter rule is still terminal.** `probe.mjs:96-99` flips `filtered`; any
  `droppedLines` sets `trace-filtered-uncertain-NOT-PASS` (`:128`), which makes `failed` true
  (`:215`) → `final('control-failure-inconclusive')` → `checkCompleteness` throws
  `INCOMPLETE: no normal terminal record` (`stream.mjs:78`). The 256 MiB cap path raises
  `EvidenceLimitError` → `final('limit-exceeded-inconclusive')` (`stream.mjs:35-38`). Both
  terminal, neither can become a pass.
- **Does ~6.5 GB of parent SHA256 fit in the unchanged 1800 s? Yes, comfortably — and yes, it
  counts toward the same timeout.** The hashing happens inside `probe.mjs`, inside the
  `podman run` that `launch.sh:43` wraps with `timeout --kill-after=30s 1800s`, so it is on
  the same clock. My volume figure matches the implementer's: 24 old-tree verifications
  (204,435,070 B) + 18 new (88,620,916 B) + 2 initial = **6.81 GB**. For the cost I used the
  only real measurement available: in `measure-leaf-control.results.json` the gap between one
  child's last API event and the next child's first is **0.259 s / 0.319 s / 0.220 s**, and
  each gap *contains* a full `verifyInputTree` of the next tree plus scratch setup, strace
  exec and runtime startup. So ≤0.32 s is an upper bound on one old-tree verification
  (≈640 MB/s hashing + ~33k file opens/s on the page-cached RO bind — consistent with SHA-NI).
  42 verifications ≤ **13.4 s**, under 1 % of the 1800 s deadline. **Not tight — no MEDIUM on
  this axis.** (The pre-existing arithmetic tension is elsewhere and unchanged: 42 children ×
  90 s child deadline = 3780 s > 1800 s, documented at `README.md:180`; see Residual Risk.)

### 5. LOW-1..3 — all three fixed, each with a selftest

- LOW-1 (CapDrop as a full list): `launch-argv.mjs:26` pins the 11-capability set;
  `:44` compares sorted arrays, pinning membership *and* length. I checked the pinned list
  against both real receipts (`container-inspect.json`,
  `capture-kill-container-inspect.json`) — exact match, `CapAdd: []`. Selftest at
  `selftest.mjs:158-159` pops one capability and asserts `/capabilities/`.
- LOW-2 (all `--security-opt` values): `launch-argv.mjs:45-46` extracts both the split and
  `=` forms and requires `SecurityOpt` to be a superset. Selftests at `selftest.mjs:160-163`
  cover the missing-value and extra-inspect-value directions. The superset direction cannot
  detect a privilege-relaxing option added outside argv, but that is covered independently by
  the in-container kernel proof (`check-prep.py:117-119` asserts `NoNewPrivs: 1`,
  `Seccomp: 2` and five zeroed `Cap*` lines; `limits-proof.stdout:11-13` shows them).
- LOW-3 (Mounts by bind): `launch-argv.mjs:53-55` filters `Type === 'bind'` before the count
  and the per-bind lookup. Selftest at `selftest.mjs:164-165` adds a tmpfs mount and asserts
  it still passes. See LOW-2 below for the residue.

### 6. Trust boundary and no scope creep — CONFIRMED

The r5 README section states the boundary in the terms the brief asks for: trusted host, all
input binds `:ro`, **"RO in a container is NOT hostile-host immutability"**, no concurrent host
writer between verification and child use, per-child hashes detect substitution of the *loaded*
payloads only, no hostile-host attestation claim. `verify-inputs.mjs:32-33` carries the same
caveat at the call site, and `verifyInputTree` returns `verifier: 'untraced-parent'` into the
evidence frame. `aggregateSha256` is correctly described as a digest of the verified pinned
*records*, not of host state.

No scope creep: the diff is 7 harness files / +115 −44. `launch.sh`, `Containerfile`,
`runtime.mjs`, `stream.mjs`, `prepare.py`, `host-receipt.mjs`, `noop.mjs` are untouched, so
limits, privileges, binds, argv construction, the 18 `expectedCases`, `maxChildOperations = 42`
and `wholeDeadlineSeconds = 1800`/`wholeKillAfterSeconds = 30` are unchanged by construction.
`input-manifest.json` confirms `capture.deadlineSeconds: 1800`, 18 unique `expectedCases`,
`stdoutCap: 256 MiB`, and full per-file inventories (`old-runtime` 10,628, `new-runtime`
7,149 records, modes in `0o…` form that `Number()` parses correctly).

## Findings

**MEDIUM-1 (correctness of the viability estimate) `budget-check.py:39-41` /
`budget-estimate.json` — the 4× sensitivity factor is below the per-child headroom the real
case order needs, and the consequence is an all-or-nothing INCOMPLETE after up to 1800 s of
Phase-E container time.**

The model is honest about being a model, but it is the artifact Soma will use to judge
viability, so its *calibration* matters. Deriving the headroom from the same numbers:
`(192 MiB − 16 MiB) / 42 = 4,394,033` framed bytes per child; the measured framing cost is
`582,500 / 882 ≈ 660` B per trace line (node) — so filtering starts at about **6,650 retained
trace lines per child**, i.e. **≈7.5× the measured node leaf path (882 lines)**, and the
256 MiB cap at ≈9,100 lines (≈10×). The model budgets 4× (3,528 lines).

The measured baseline is the `prep-assertions` path, which imports **no SDK at all**. The real
children run under `-e trace=%file,%desc,%memory,%process` and import the storage/agent stack.
From the pinned trees: 2,893 JS files in the new tree, 4,211 in the old; the plausible
`af`-route import closure (`@animalabs/agent-framework` 164, `context-manager` 299, `zod` 248,
`@anthropic-ai/sdk` 122, `discord.js` 378, `discord-api-types` 285, `undici` 99, plus
chronicle/membrane) is on the order of 800–1,600 modules. At the usual ~6–10 file/desc lines
per ESM module load (resolution stats, `openat`, `fstat`, reads, `close`) that is roughly
5,000–16,000 retained lines per child *before* any store work — straddling and plausibly
exceeding the 6,650 threshold. The `af` route is also last in `routes`
(`runtime.mjs:5`), so the cumulative threshold is most likely to trip there.

Impact is bounded but real: it is never a false pass, but `probe.mjs:215-216` makes *one*
filtered case set `final('control-failure-inconclusive')` for the whole run, so 17 clean cases
are nullified with it — the same "spend the authorization to learn nothing" failure mode r4
flagged, one order of magnitude less certain. The README already says an estimate approaching
192 MiB requires STOP; the point is that the estimate may be understating by ~2×.

*Fix direction (not blocking approval):* either (a) add one bounded prep measurement of the
*import-only* path — resolve and `import()` the chronicle/CM/AF entries under strace with no
store path ever opened, which stays inside Soma's "no store created/opened" constraint and
would replace the 4× guess with a measured import factor; or (b) keep the estimate and make the
failure cheap: carry per-child retained bytes against the running budget (the
`trace-filter-stats` frames already exist) and STOP at the first child that projects over
threshold, instead of discovering it at case 15 of 18; and/or (c) make the filtered verdict
per-case so unfiltered earlier cases remain qualifiable.

**LOW-1 (evidence fidelity) `worker.mjs:58` — the `runtime-verified` frame now reports the
manifest's expected digest instead of the computed one.** r4 emitted
`executableSha256: hash(process.execPath)`. The comparison at `:57` still recomputes the real
digest, so the *check* is intact; but a reviewer reading the stream can no longer distinguish
"recomputed and matched" from "copied out of the manifest", which is exactly the distinction
this frame exists to carry. The saving is one redundant 125 MB re-hash. Fix: keep the computed
value in a local and emit that.

**LOW-2 (robustness) `launch-argv.mjs:53-55` — filtering `Mounts` to `Type === 'bind'` also
stops counting named volumes.** r4's `binds.length !== container.Mounts.length` would have
rejected *any* extra mount, including a `type=volume` one the envelope forbids; the new count
only looks at binds, so a non-bind, non-tmpfs mount added by a modified launcher passes this
check silently. Mitigated — not hidden — by `check-prep.py:110`
(`all(not m['RW'] for m in container['Mounts'])`), which covers every type on the committed
proof path. Fix: exclude only `Type === 'tmpfs'` (already cross-checked against `--tmpfs`) and
assert no mount has a type outside `{bind, tmpfs}`.

**LOW-3 (coverage reduction, disclosed) `probe.mjs:139` / `worker.mjs:6` — `/harness`, the
strace binary and the tracer libraries are now verified once per run, not once per child.**
In r4 every child re-ran `verifyCriticalInputs`; r5 removes it from the tracee (correctly — it
was part of the trace flood) but did not add a per-child equivalent to the parent, which only
checks at `main()` start. The README's revised §3 wording is honest about this. It is
consistent with the trusted-host/no-concurrent-writer boundary, but unlike the tree walk it is
*cheap* — 19 harness files, one 2 MB strace, four small libs, zero trace cost — so there is no
volume reason not to call `verifyCriticalInputs(approvedManifest)` next to the
`verifyInputTree` call at `probe.mjs:71`. Also note `/inputs/{old,new}-tree.json` now have no
consumer at all in the child (`worker.mjs:10` only string-compares the path); they are dead
weight kept alive by `verifyCriticalInputs`.

**LOW-4 (test gap) `selftest.mjs:160-172` — nothing pins the ordering that closes HIGH-1 on the
parent side.** The new test asserts the absence of bulk calls in `worker.mjs` and the
source-order of the `prep-assertions` branch, both over `worker.mjs` text, plus
`verifyInputTree`'s tamper refusal in isolation. There is no assertion that `probe.mjs` calls
`verifyInputTree` *before* `spawn` — the one property a future refactor is most likely to
break silently (moving `:71` below `:72` would still pass every committed test and the whole
gate suite). Fix: add the same style of source-order assertion over `probe.mjs`
(`indexOf('verifyInputTree(approvedManifest') < indexOf('spawn(')`), or better, a seam test
that stubs `spawn` and asserts it was never reached on a tampered tree.

## Verification Gates

Reused receipts at `1cfe32a1` (per the brief: read, not re-run) — all exit 0:

| gate | log |
| --- | --- |
| `phase-p-r5-check-prep-committed` | `…/gate-phase-p-r5-check-prep-committed-01M67G723PWMW9KCE5KY1TYTZB.log` — "25 mock/data tests", 6 python tests OK |
| `phase-p-r5-budget-estimate-committed` | `…/gate-phase-p-r5-budget-estimate-committed-01M67G84B0T0A4YNFVDSZE6XKM.log` |
| `phase-p-r5-leaf-measure-committed` | `…/gate-phase-p-r5-leaf-measure-committed-01M67G7762B5HVH1RDAQMCK5JM.log` — 4 prep-only children, exit 0 |
| `phase-p-r5-noop-committed` | `…/gate-phase-p-r5-noop-committed-01M67G6PGEKDN20BWM4R2PJW7Y.log` |
| `phase-p-r5-kill-committed` | `…/gate-phase-p-r5-kill-committed-01M67G70Z1SHDJ45NMS87TFN10.log` |
| `phase-p-r5-deadline-negative-committed` | `…/gate-phase-p-r5-deadline-negative-committed-01M67G6ZFY5KP6E93S4WBV675A.log` |

Read-only probes I ran myself (no containers, no gates, nothing written to the worktree):

1. `git diff bac2ebb2..1cfe32a1` full read of all 7 files + full read of `probe.mjs`,
   `worker.mjs`, `verify-inputs.mjs`, `runtime.mjs`, `stream.mjs`, `launch-argv.mjs`,
   `check-prep.py`, `measure-leaf-control.mjs`, `budget-check.py`.
2. Re-derived the entire budget arithmetic from `measure-leaf-control.results.json`
   independently of `budget-estimate.json` — every figure reproduces (see §4).
3. Cross-checked `expectedCapDrop` against both real `podman inspect` receipts — exact
   11-capability match, `CapAdd: []`, `SecurityOpt: ['no-new-privileges']`, all 11 Mounts
   `Type: bind`, all `RW: false`.
4. Re-checked all four `native-payload` digests from the leaf-control API events against
   `input-manifest.json` records — present and matching for both trees and both runtimes;
   confirms `pinnedHash`'s old/new relative-path mapping is right on the real trees.
5. Derived the parent-hashing time bound from the inter-child wall-clock gaps in
   `measure-leaf-control.results.json` (0.220–0.319 s, each containing one full
   `verifyInputTree`) → ≤13.4 s for all 42 (see §4).
6. Counted the JS/`package.json` population of both pinned trees from `old-tree.json` /
   `new-tree.json` to size MEDIUM-1 (2,893 / 4,211 JS files; per-package breakdown above).
7. Confirmed the manifest's mode encoding (`0o644`-style) parses under
   `verifyPinned:28`'s `Number()`, and that `old-runtime`/`new-runtime` carry full 10,628 /
   7,149 record inventories.
8. `git status --porcelain` empty before and after.

Not run, deliberately: Phase E, any container, any gate re-run.

## Unmet criteria / test gaps

- Phase-P acceptance artifacts (manifest sha256, harness sha256, image ID + digest, full argv,
  whole-command deadline, limits proof) are all present and nothing in this delta regresses
  them. r4's HIGH-1 blocker on the assignment *method* is removed: old-reader→new-store with
  before/after inventory is now reachable under the approved manifest.
- The per-child evidence volume of a child that actually **imports the SDK** is still
  unmeasured — the r4 "blind spot" is narrowed (the leaf path is now measured in the real
  envelope) but not closed. MEDIUM-1.
- No test pins `verifyInputTree`-before-`spawn` in `probe.mjs`. LOW-4.
- Carried forward from r4, explicitly not addressed by r5 (the report says so): LOW-5
  (negative selftests for receipt→anchor binding and receipt-set assertion), LOW-4/LOW-6/LOW-7
  (bounded `%desc` smoke, README "all path syscalls" wording). Out of this brief's scope.
- The ≥192 MiB filter path remains documented-as-terminal rather than exercised end to end
  (closed by design in r4).
- `checkExecutionIntent`'s `kind === 'execute'` branch still has data-only coverage;
  `execute-argv-NOT-RUN.json` has never been fed through the checker (carried from r3).

## Residual Risk

1. **MEDIUM-1:** Phase E may terminate INCOMPLETE on trace volume during the `af` route, after
   spending a large part of the 1800 s authorization. Fail-closed, no false pass, no data loss.
2. **Unchanged arithmetic tension:** 42 children × 90 s child deadline = 3780 s against an
   1800 s whole-command deadline (`runtime.mjs:11,14`, documented at `README.md:180`). Healthy
   children are far faster than 90 s, but a handful of slow ones ends the run on the aggregate
   timeout → INCOMPLETE. Unchanged vs r4; the +13 s of parent hashing does not move it.
3. **Trust boundary:** correctness of the per-child verification rests on "no concurrent host
   writer to the input trees between verification and child use". Stated explicitly in code and
   README; RO binds are not hostile-host immutability. Accepted, and the same boundary r4's fix
   direction prescribed.
4. The deadline/argv bind is still an honest-launcher bind; `podman inspect` corroborates the
   container, not the host `timeout` wrapper (`launch-argv.mjs:27`). LOW-2 slightly widens what
   inspect would miss on the stream path; `check-prep.py:110` and the kernel/cgroup proof remain
   the primary limits evidence.
5. LOW-3 means harness/strace/lib substitution mid-run is detected only at run start.

Every failure mode above lands on INCOMPLETE/NOT-PASS. There is no false-qualification or
data-loss path in this delta.

## Fix Directions

1. **MEDIUM-1 (recommended before Phase E is authorized, not blocking this review):** add a
   bounded *import-only* prep measurement (resolve + `import()` the chronicle/CM/AF entries
   under strace, no store path opened) to replace the 4× guess with a measured factor; and/or
   make the budget enforce itself per child from the existing `trace-filter-stats` so an
   over-budget run STOPs early instead of at case 15 of 18; and/or make the filtered verdict
   per-case so unfiltered cases stay qualifiable.
2. **LOW-1:** `const executableSha256 = hash(process.execPath)` and emit that computed value at
   `worker.mjs:58`.
3. **LOW-2:** at `launch-argv.mjs:53`, exclude only `Type === 'tmpfs'` and assert no mount has
   a type outside `{bind, tmpfs}`.
4. **LOW-3:** call `verifyCriticalInputs(approvedManifest)` alongside the `verifyInputTree` call
   at `probe.mjs:71` (zero trace cost, ~2 MB per child), or state in the README that
   harness/tracer integrity is a run-start property only.
5. **LOW-4:** pin `verifyInputTree`-before-`spawn` in `selftest.mjs` the same way
   `prep-assertions` ordering is pinned, or add a stubbed-`spawn` seam test.
