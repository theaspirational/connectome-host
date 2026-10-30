# TASK-MHQNZ Phase P revision 4 — bounded delta review

Scope: `git diff e3f07c16..bac2ebb2` only (10 files, all `test/store-probe/`). Read-only.
No Phase E, no container started, no store created/opened, no gate re-run — receipts read.

## Verdict

**REJECT for Phase-P sign-off / Phase-E authorization.**

The r4 delta itself is good: **all five carried-forward items (M-1, M-2, M-3, M-4, LOW-2)
are closed**, the envelope/limits/privileges/binds are byte-identical to r3, and there is no
scope creep. Reviewed on the delta alone I would approve it.

But the manager's concrete viability check (msg-a5962e5e, Soma) is **CONFIRMED**, and it is a
HIGH: at `bac2ebb2` Phase E provably exhausts its own evidence budget inside the **first of six
runtime×route blocks**, so the `old-reader-to-new-store` rows — the assignment's actual
experiment — can never be produced for any route. See HIGH-1. That is unmet acceptance, not a
hardening wish, so it blocks authorizing Phase E on this manifest.

## Point-by-point

**M-2 (external manifest anchor) — CLOSED, no residual circularity.**
`check-prep.py:12` adds a required positional `approved_sha256`, `:14` enforces the hex
shape, `:15` asserts `sha256(evidence/input-manifest.json) == approved` *before* any other
work. `:17-21` declares an explicit `(expected_manifest, expected_sha)` binding per
receipt — normal, kill and negative — and `:22` asserts the receipt set discovered by
`rglob('*-host-receipt.json')` equals exactly that set, so an extra or missing receipt
refuses. `:24-25` re-hashes each expected manifest against the anchor and asserts each
receipt's `manifestSha256` equals it. Both stream-check invocations (`:68`, `:94`) now
pass `a.approved_sha256`; `grep -n hashlib check-prep.py` confirms the only remaining
`hashlib` uses are the anchor comparisons and the per-input manifest-declared hash loop
(`:47`), which is now downstream of an externally anchored manifest. The r3 circularity
(`hashlib.sha256(manifest) → trusted argument`) is gone.
The negative's "byte-identical copy" is **not** a hole: `:20`+`:24` require
`sha256(deadline-negative/input-manifest.json) == approved`, i.e. it is the approved bytes,
not an independently self-consistent manifest. Receipt-level substitution is caught by `:25`.
Refusal is selftested: `selftest.py:61-71` runs the gate with no anchor (argparse error
mentioning `approved_sha256`) and with `'0'*64` (`approved manifest SHA256 mismatch`),
asserting non-zero exit both times. Verified the live gate was given the anchor externally:
`check-prep.py <evidence> d9b0eea04c17…` in the recorded gate command.

**M-3 (production transport in the smoke) — CLOSED; a pass with a broken transport is not
reachable through any of the four checks.**
`strace-smoke.mjs:4-9` exports a single `transportScript` (`"$@" 3>&1 1>&5 | /bin/cat`),
`productionStdio` (`[ignore,pipe,pipe,ignore,pipe,pipe]`) and `productionTransport()`;
`probe.mjs:65-71` consumes both (`...productionTransport([...])`, `stdio: productionStdio`),
obtained from the same module object the smoke comes from (`probe.mjs:153-155`).
`grep -rn '3>&1' test/store-probe/` shows exactly one code occurrence — the shared export —
plus the gate assertion and the README; the duplicate literal is gone.
The asserts are disjoint and each one is falsified by a different transport break:
`traceHasTrueExec` (fd3→cat→bash fd1), `stdoutMarkerOnly` (exact `PROBE_SMOKE_STDOUT_v1\n`
on fd5), `apiMarkerOnly` (exact marker JSON on fd4), `traceHasNoMarkers` (no crosstalk);
`passed` ANDs all four plus `devFdExists`, `status === 0` and no spawn error
(`strace-smoke.mjs:22`). `check-prep.py:81-87` re-derives `transportArgv`/`stdio` by
importing the committed `productionTransport` and asserts equality, asserts
`transportArgv[4] === '"$@" 3>&1 1>&5 | /bin/cat'`, asserts `all(checks.values())`,
`execve("/bin/true"` present, both markers exact, and both markers absent from the trace —
so `transportArgv` is pinned in the gate, not merely "output non-empty".
Live evidence (`limits-proof.stdout.jsonl` `strace-smoke` frame) shows all four checks
`true`, `exitStatus 0`, real trace bytes (`execve("/runtime/node/node"…`, `execve("/bin/true"…`),
`stdout 'PROBE_SMOKE_STDOUT_v1\n'`, `api` = marker JSON — i.e. the production fd shape
actually executed, which was the r3 gap. `transport-control.mjs` is hash-bound: present in
`harness.SHA256SUMS:17` and in the manifest harness group, now 19 files (was 18).

**M-1 (inspect cross-check) — CLOSED.**
`launch-argv.mjs:26-55` `checkContainerInspection` compares recorded argv against inspect
for Image, `Config.Cmd` (exact `argv.slice(imageIndex+1)`), `Config.User`, Memory,
MemorySwap, NanoCpus, PidsLimit, ShmSize, NetworkMode, PidMode, CgroupMode, IpcMode,
ReadonlyRootfs, Privileged, cap-drop, SecurityOpt, port bindings, `core=0` ulimit, tmpfs
count+flags, and bind count + per-bind source/destination/read-only. `stream-check.mjs:30`
calls it before `checkExecutionIntent` and surfaces `inspectedContainer` in the output;
`launch.sh:81-85` moves `podman inspect` ahead of the checker and passes `$inspect`.
Mismatch refusal is selftested (`selftest.mjs:151-163`: Cmd push, Image swap, Memory
change → `/inspect\/recorded argv mismatch/`). Non-vacuous on live data: 11 binds vs 11
`Mounts`, 2 tmpfs, `CapDrop` 11 entries, `inspectedContainer` populated in all three checks
(`limits-proof-check.json`, `capture-kill-check.json`, `deadline-negative-result.json`).
Wording is honest: `launch-argv.mjs:25` ("Cheap corroboration, not an attestation of the
host timeout or launcher") and `README.md:264-268` state the launcher is trusted, recorded
argv is self-attestation, inspect does not attest the host `timeout` wrapper, no
auth/attestation service is introduced and owner consent stays external. No new services.

**M-4 (budget semantics) — CLOSED as documentation, no new architecture.**
`README.md:269-273`: filter activation at ≥192 MiB with ANY drop is **terminal INCOMPLETE**,
filtering cannot rescue a run into a qualification, and "the budget must not be approached".
`probe.mjs` is unchanged on this path — the diff touches only the regex, the argv/stdio
wiring and the import; no per-case budget or reserve was added, as requested.

**LOW-2 (regex) — CLOSED.** `probe.mjs:48` now has `(?:new)?fstat\w*`, `f?access\w*`,
`f?chmod\w*`, `[fl]?chown\w*`, `f?utime\w*`; `newfstatat(`, `faccessat2(`, `fchmodat(`,
`fchownat(` all match (`\b` + optional prefix). `selftest.mjs:146-150` asserts all four are
retained with no store path and `droppedLines == 0`.

**Envelope / scope.** `git diff … -- launch.sh` contains no podman-argument change at all —
the only launch.sh delta is relocating `podman inspect` above the `stream-check` block.
Programmatic comparison of r3 (`run-01M67DABP7KS1CNCSB0SPAW9BW`) vs r4 inspect over
Memory/MemorySwap/NanoCpus/PidsLimit/ShmSize/Network/Pid/Cgroup/Ipc/ReadonlyRootfs/
Privileged/CapAdd/CapDrop/SecurityOpt/Ulimits/PortBindings/PublishAllPorts/Tmpfs/User/Cmd
and the sorted bind destinations+RW: **identical**. No product, dependency, lockfile,
Containerfile or image change; every one of the 10 changed files maps to M-1…M-4/LOW-2.

## Findings

**HIGH-1 (correctness / unmet acceptance) `test/store-probe/worker.mjs:77` (`verifyTree`, defined
`:23-42`) + `test/store-probe/probe.mjs:65-70` + `probe.mjs:93` — every traced child re-walks and
re-hashes the entire input tree *inside* strace, and each syscall line becomes its own framed
base64 record, so the 256 MiB stdout cap is exhausted during the first route and the
cross-version rows never run. CONFIRMED from the code; no runs performed.**

*Chain of custody in the code.* `probe.mjs:65-70` makes `worker.mjs` the tracee of
`/tools/strace -f -ttt -r -T -yy -s 256 -e trace=%file,%desc,%memory,%process`, so everything
`worker.mjs` does is traced. `worker.mjs:9` runs `verifyCriticalInputs` (cheap: 19 harness files
+ strace + 2 tree-assertion JSONs + 4 libs) and `worker.mjs:77` then calls `verifyTree()`, which
at `:26-32` `readdirSync`/`lstatSync`-walks the whole tree and at `:35-41` does, **per record**,
one `lstatSync` plus a full `readFileSync` + SHA256 (`hash()` at `:22`). `probe.mjs:93` emits
**one `syscall-trace` frame per trace line** via `raw()` (`probe.mjs:84-86`), base64-encoding the
line; `stream.mjs:30-39` enforces `stdoutCap = 256 * 1024 * 1024` (`stream.mjs:3`) across the
whole run.

*Inputs and per-child cost.* From the evidence tree assertions: `old-tree.json` = 10,628 files /
204,435,070 bytes / 882 directories; `new-tree.json` = 7,149 files / 88,620,916 bytes / 306
directories (matches the figures in the message). Per input file `verifyTree` issues ≈6 traced
syscalls (walk `newfstatat`; then `newfstatat` + `openat` + `fstat` + `read` + `close`), plus ≈5
per directory for `readdirSync`:
- old tree ≈ **68,178** trace lines/child; new tree ≈ **44,424** lines/child.

Framing overhead is the dominant term and is computable exactly from `stream.mjs:23-28`: each
frame is `{"sequence":…,"type":"syscall-trace","data":{label,runtime,route,operation,
receivedRelativeNs,encoding,bytes},"sha256":<64>,"previous":<64>,"chain":<64>}` + `\n` ≈ **435
bytes fixed**, plus `4/3 ×` the raw line. With `-yy` (fd→path annotation, so the ~67-char
container path is rendered twice on `openat`) and `-s 256`, raw lines average ~223 bytes; I also
ran a deliberately conservative floor of 150 bytes:

| | per old-tree child | per new-tree child |
|---|---|---|
| floor (L=150) | 43.3 MB | 28.2 MB |
| realistic (L=223) | 50.1 MB | 32.7 MB |

*Case order.* `probe.mjs:160-186` iterates `runtimes` (`node`, `bun`) → `routes`
(`chronicle`, `cm`, `af`) → per route: `seed`/`verify` on `old`, `seed`/`verify` on `new`, then
`scenarios` (`runtime.mjs:6-10`) `old-to-old` (reader `old`), `new-to-new` (reader `new`),
`old-reader-to-new-store` (reader **`old`**). So each route is 7 children = 4 old-tree + 3
new-tree, and the first cross-version case is child **7 of 42**. Cumulative evidence bytes:

| child | tree | cum (floor) | cum (realistic) |
|---|---|---|---|
| 1 seed-chronicle-old | old | 43.3 MB | 50.1 MB |
| 2 verify-chronicle-old | old | 86.6 MB | 100.2 MB |
| 3 seed-chronicle-new | new | 114.8 MB | 132.9 MB |
| 4 verify-chronicle-new | new | 143.0 MB | 165.5 MB |
| 5 attempt chronicle/old-to-old | old | 186.3 MB | **215.6 MB → filter arms** |
| 6 attempt chronicle/new-to-new | new | **214.5 MB → filter arms** | 248.3 MB |
| 7 attempt chronicle/**old-reader-to-new-store** | old | 257.8 MB (cap at 268.4 MB) | **298.4 MB → cap exhausted** |

Over all 42 children (24 old-tree + 18 new-tree) the demand is **1,547 MB (floor) to 1,790 MB
(realistic) — 5.8× to 6.7× the 256 MiB cap.**

*Both exits are terminal, and the filter cannot rescue it.* The 192 MiB arming point
(`probe.mjs:91`) is crossed at child 5–6, i.e. **before** the first `old-reader-to-new-store`
attempt. Once armed, `traceDecision` (`probe.mjs:48`) drops anything it does not match — and
`read(`, `write(`, `getdents64(`, `execve(`, `clone(`, `wait4(`, `brk(` and the
`+++ exited +++` / `--- SIGCHLD ---` lines are **not** in the retain alternation, and the
`line.includes(store)` escape does not apply because these lines reference `/inputs/old`,
not the clone store path. So `droppedLines > 0` is guaranteed, which sets
`trace-filtered-uncertain-NOT-PASS` (`probe.mjs:128`) → `control-failure-inconclusive`
(`probe.mjs:208`) → `checkCompleteness` INCOMPLETE (`stream.mjs:88`). This is exactly the
terminal semantics r4 just documented for M-4 (`README.md:269-273`), so M-4's "the budget must
not be approached" is violated by the harness's own mandatory per-child work. The other branch
(cap) raises `EvidenceLimitError` with `final('limit-exceeded-inconclusive')`
(`stream.mjs:35-37`) — also INCOMPLETE. **Every path ends INCOMPLETE at or before case 7 of 42;
5 of the 6 `old-reader-to-new-store` cases and the entire `bun` matrix never execute.**

*Secondary, same root cause:* the aggregate `wholeDeadlineSeconds = 1800` (`runtime.mjs:14`) is
also at risk — 24×204 MB + 18×88 MB ≈ **6.5 GB** of SHA256 under ptrace plus ≈2.0 M
ptrace-stopped syscalls and ≈2.0 M unbuffered `fs.writeSync(1, …)` calls (`stream.mjs:12-18`,
one per frame). Not the binding constraint; the cap is hit first.

*Fix direction (as the message proposes, and it fits the existing structure).* Hoist the bulk
read-only input verification out of the tracee: call `verifyPinned(manifest, 'old-tree'/'new-tree', …)`
(or the equivalent of `verifyTree`) **once** in `probe.mjs main()` next to the existing
`verifyCriticalInputs(manifest)` at `probe.mjs:148` — untraced, bound to the externally anchored
manifest — and emit it as a single `input-trees-verified` frame. Keep in `worker.mjs` only the
per-runtime assertions that must be made from inside the runtime and are already cheap and
separately traced: the executable path + binary hash + version (`worker.mjs:71-75`), the
conditional-entry resolutions + entry hashes (`:80-88`), the version/transitive-resolution checks
(`:90-101`), the native payload hash (`:103`), and the full store-access trace. That removes
~68k/44k lines per child, leaving the store work — the thing the trace is actually for. State the
boundary explicitly in the report: this trusts the host and the read-only bind mounts
(`launch.sh:29-39`, all `:ro`) for the duration of the run, i.e. it assumes no concurrent writer
to `/inputs/old` and `/inputs/new` between the one-time hash and each child's use; the per-child
entry/binary/native hashes still detect substitution of anything the child actually loads.

**LOW-1 (correctness) `test/store-probe/launch-argv.mjs:38` — the cap-drop cross-check is
weaker than the envelope it certifies.** `option('--cap-drop') !== 'ALL' || !h.CapDrop?.length`
accepts any non-empty `CapDrop`. An inspect reporting a single dropped capability against an
argv saying `--cap-drop=ALL` would pass. Live data happens to show 11 entries. *Fix:* assert
`CapDrop` covers the image's full default set (or at least a pinned expected length/list).

**LOW-2 (correctness) `test/store-probe/launch-argv.mjs:39` — only the first `--security-opt`
is cross-checked, and seccomp is not covered.** `option()` returns the first match, so a
future second `--security-opt` (e.g. an explicit `seccomp=…`) would be silently unverified;
the envelope's seccomp requirement is attested only by the kernel proof, not by this
argv↔inspect comparison (argv relies on podman's default profile). *Fix:* collect all
`--security-opt` values and require `SecurityOpt` to be a superset.

**LOW-3 (robustness) `test/store-probe/launch-argv.mjs:46-48` — `binds.length !==
container.Mounts.length` assumes podman never lists tmpfs in `Mounts`.** True for this
podman version (2 tmpfs appear only under `HostConfig.Tmpfs`), but a version that reports
tmpfs as mounts turns a healthy run into INCOMPLETE. Fail-closed, so not a correctness risk.
*Fix:* filter `Mounts` to `Type === 'bind'` before the count.

**LOW-4 (robustness) `test/store-probe/launch.sh:81-85` — moving `podman inspect` ahead of
`stream-check` means an inspect failure now aborts under `set -e` before `$prefix-check.json`
is written.** Previously the checker artifact existed regardless. Loses a diagnostic
artifact on an otherwise-recoverable host hiccup. *Fix:* wrap the inspect in `set +e` and let
the checker refuse on a missing/empty inspect file.

**LOW-5 (test) `test/store-probe/check-prep.py:25` — the receipt→anchor binding has no
negative selftest.** `selftest.py:61-71` covers the missing and wrong *manifest* anchor only;
no test feeds a receipt whose `manifestSha256` differs from the anchor, nor an extra/missing
receipt against the `:22` set assertion. The code is read-correct; the refusal is unproven.

**LOW-6 (test fidelity) `test/store-probe/strace-smoke.mjs:13` — the smoke narrows strace to
`-e trace=execve` while production uses `trace=%file,%desc,%memory,%process`.** The fd/pipe
shape is now identical (that was the M-3 ask and it is met), but the smoke still does not
exercise trace volume under `%desc`, so the filter/backpressure path and the `traceHasNoMarkers`
style of reasoning do not transfer to the production filter. Note that the narrowing is what
makes `traceHasNoMarkers` meaningful here, so this is a deliberate, sound trade-off — it just
leaves the high-volume path untested, same as r3's MEDIUM-4 residual.

**LOW-7 (correctness, pre-existing) `test/store-probe/probe.mjs:48` — `pread64`/`pwrite64`/
`readv`/`writev` are still unmatched by the retain regex.** Beyond the r3 ask (which named
`newfstatat` and `*at` variants, now fixed). Store-path I/O is still retained via
`line.includes(store)` because `-yy` annotates fds with paths, so correlation is unaffected;
only the "all path syscalls" wording overstates the regex.

## Verification Gates

No gate re-run (brief forbids it; reusable receipts exist at `bac2ebb2`). Read:
- `phase-p-r4-check-prep-committed` — exit 0 — final line "Phase P gate PASS: syntax, 24
  mock/data tests + no-op/strace smoke/kill/deadline-negative checks…" — log
  `…/evidence/gate-phase-p-r4-check-prep-committed-01M67FBST5M5DRPKX9RQ47VRBW.log`
- `phase-p-r4-noop-committed` — exit 0 — `…/gate-phase-p-r4-noop-committed-01M67FBDVS5YYCWGD032XPC52Q.log`
- `phase-p-r4-kill-committed` — exit 0 — `…/gate-phase-p-r4-kill-committed-01M67FBRMYK512E8HTM7V67P1E.log`
- `phase-p-r4-deadline-negative-committed` — exit 0 — `…/gate-phase-p-r4-deadline-negative-committed-01M67FBKDVCPMFQPPR8ECVYJK5.log`

Read-only probes I ran (no container, no store, no writes to the worktree):
- `git diff e3f07c16..bac2ebb2` in full plus `--stat` for the scope check; `git diff -- launch.sh`
  filtered for podman-argument lines (empty).
- `grep -n 'hashlib|approved_sha256' check-prep.py`; `grep -n selftest check-prep.py`;
  `grep -rn '3>&1' test/store-probe/`; `grep -n traceDecision selftest.mjs`.
- `find <evidence> -name '*-host-receipt.json'` → exactly the three paths `check-prep.py:17-21`
  expects; `ls <evidence>/deadline-negative` → `container-inspect.json` present.
- Parsed the `strace-smoke` frame out of `limits-proof.stdout.jsonl` (checks, transportArgv,
  stdio, stdout, api, trace bytes); read `limits-proof-check.json`, `capture-kill-check.json`,
  `deadline-negative/deadline-negative-result.json`.
- Python comparison of r3 vs r4 `container-inspect.json` across the 20 envelope fields above.
- Manifest harness group = 19 files including `transport-control.mjs`; `harness.SHA256SUMS:17`.

## Unmet criteria / test gaps

- Phase-P acceptance artifacts (manifest sha256, harness sha256, image ID+digest, full argv,
  whole-command deadline, limits proof) are all present and nothing in this delta regresses them.
  But the assignment's *method* (old-reader→new-store with before/after inventory) is unreachable
  under the approved manifest — HIGH-1.
- No test or estimate in the harness bounds the evidence volume of a real traced child against the
  256 MiB cap. The only exercised children are the mock no-op/kill, which emit 6-8 frames; nothing
  extrapolates to 42 real children. That blind spot is what let HIGH-1 through three revisions.
- No negative selftest for the receipt→anchor binding or the receipt-set assertion (LOW-5).
- The high-volume filter path (≥192 MiB) is still untested end to end; it is now documented as
  terminal rather than exercised (M-4 closed as documentation, by design).
- `checkContainerInspection` has no selftest for the tmpfs/bind/ulimit/cap branches — only
  Cmd/Image/Memory are mutated in `selftest.mjs:159`.
- `checkExecutionIntent`'s `kind === 'execute'` branch still only has data-only coverage;
  `execute-argv-NOT-RUN.json` has never been fed through the checker (carried from r3).

## Residual risk

0. HIGH-1: Phase E as approved cannot produce a cross-version result. Fail-closed (INCOMPLETE,
   never a false pass), so there is no data-loss or false-qualification risk — but ~25 minutes of
   container time and the whole Phase-E authorization would be spent to learn nothing.

1. The deadline/argv bind remains an honest-launcher bind; inspect corroborates the container
   but not the host `timeout` wrapper. This is now stated plainly in code comment and README —
   the report must keep it that way to Soma.
2. Phase E's first child can still fail for a reason the smoke does not cover — but the fd3/fd5/fd4
   transport itself is now proven to work in the real envelope, which was the main r3 exposure.
3. A trace volume approaching 192 MiB makes the run unqualifiable in both branches (accepted,
   documented).
4. LOW-1/LOW-2 mean the inspect cross-check is a corroboration of shape, not a full
   re-derivation of the envelope; the kernel/cgroup proof remains the primary limits evidence.

Every failure mode above lands on INCOMPLETE/NOT-PASS — fail-closed, no false-pass or data-loss path.

## Fix directions

0. **HIGH-1, blocking:** hoist the bulk tree hash out of the tracee into `probe.mjs main()` (see
   HIGH-1 for the exact split and the trust boundary to state), then re-derive the per-child
   evidence estimate and record it in the manifest/report so the 256 MiB cap is checkable before
   Phase E rather than after. A new approved manifest is needed either way.
1. LOW-1/LOW-2: pin the full `CapDrop` set and make `SecurityOpt` a superset check over all
   `--security-opt` values.
2. LOW-5: add selftests for a receipt/anchor mismatch and for an extra/missing host receipt.
3. LOW-3/LOW-4: filter `Mounts` to `Type === 'bind'`; keep `podman inspect` non-fatal so the
   checker artifact is always written.
4. LOW-6/LOW-7: if the volume path is ever to be relied on, add a bounded `%desc` smoke; otherwise
   soften the README's "all path syscalls" wording to match the regex.

## Addendum — manager message msg-a5962e5e (Soma viability check)

Answered in HIGH-1: **confirmed**, with file:line, the per-child line counts (68,178 old /
44,424 new), the per-child evidence cost (43–50 MB old, 28–33 MB new), the cumulative crossing
point (192 MiB filter at child 5–6 of 42, 256 MiB cap at child 6–7 — i.e. inside
`node`/`chronicle`, before the first `old-reader-to-new-store` row), the all-42 total
(1,547–1,790 MB = 5.8–6.7× the cap), and the fix direction the message proposes, with the
trusted-host / no-concurrent-writer boundary spelled out. Derived from the code and the committed
tree assertions only; no runs, no container, no store.
