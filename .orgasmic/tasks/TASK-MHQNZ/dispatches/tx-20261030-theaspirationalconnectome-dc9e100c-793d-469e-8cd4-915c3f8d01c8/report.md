# TASK-MHQNZ Phase P revision 3 — bounded delta review

Scope: `git diff 1bb62482..e3f07c16` only (16 files, all under `test/store-probe/`).
Read-only. Phase E not run, no container started, no store created/opened, no gate re-run.

## Verdict

**APPROVE.** All five requested points are substantively implemented and the
deadline-negative receipt is a genuine refusal demonstration, not a staged one.
No HIGH findings. No scope creep. Findings below are MEDIUM/LOW and none of them
block Soma's Phase-P sign-off; two are worth fixing before Phase E is authorised.

## Point-by-point

**(1) Deadline binding — PASS.**
`launch.sh:17` reads `deadline`/`kill_after` from `runtime.mjs` exports (verified at
`launch.sh:16` before import) and `launch.sh:43` interpolates them into the real
`timeout --kill-after="${kill_after}s" "${deadline}s"`. The same `command`/`args`/`child`
arrays are both recorded (`launch.sh:58` → `*-argv.json`) and exec'd (`launch.sh:62`/`75`).
`host-receipt.mjs:12` derives `deadlineSeconds`/`killAfterSeconds` by parsing that JSON
array — the `1800`/`30` literals are gone. `stream-check.mjs:28-30` rehashes the argv
file against `receipt.argvSha256` and calls `checkExecutionIntent`, which compares argv
→ receipt, argv → `manifest.capture`, argv image → `manifest.image.id`, and argv tail →
declared command. `stream.mjs:83` now compares against `capture.*`, not literals.
`prepare.py:241-248` and `check-prep.py:44-47` both derive the manifest's declared
deadline from `runtime.mjs`, so there is one definition. Grep confirms the only
remaining `1800` occurrences are the definition (`runtime.mjs:14`), a pin assertion
(`selftest.mjs:58`) and a comment (`launch.sh:41`) — none in the receipt/checker chain.

Negative receipt is real: `deadline-negative/deadline-negative-result.json` shows
`launcherExit: 2`, `actualDeadline: 600`, `actualKillAfter: 10`, `declaredDeadline: 1800`,
`declaredKillAfter: 30`, with `final.status: "complete"`, `caseIds: ["mock/noop"]`,
8 verified records / chain intact, and `error: "INCOMPLETE: deadline differs from
approved manifest execution intent"`. That is refusal *despite* a normal terminal
record and the full expected case set — exactly the required shape.

Timeout parsing is fail-closed: `launch-argv.mjs:8` requires `^--kill-after=\d+(\.\d+)?s$`
and `^\d+(\.\d+)?s$`, so `30m`/bare-seconds/`1800` forms throw rather than silently
parse; `launch-argv.mjs:9` additionally requires `podman run` immediately after. An
empty `read` at `launch.sh:17` aborts under `set -euo pipefail`. Bypass exposure is
limited to a modified launcher — see MEDIUM-1.

**(2) Trace counters — PASS.** `traceDecision` (`probe.mjs:47-52`) counts
`retainedLines/retainedBytes/droppedLines/droppedBytes`; `filterStats` emits
`trace-filter-stats` at `start`, every 1 s, and `attempt-end` (`probe.mjs:111-112`,
`probe.mjs:119`); the per-attempt result carries `traceFilterStats` and
`traceCorrelation` (`probe.mjs:122`). Any `droppedLines` → `trace-filtered-uncertain-NOT-PASS`
(`probe.mjs:126`), which the aggregate at `probe.mjs:204` matches via `/NOT-PASS/` →
`final('control-failure-inconclusive')` → `checkCompleteness` throws "no normal terminal
record" → checker INCOMPLETE. The trailing partial buffer is also counted
(`probe.mjs:118`) and a non-empty `traceBuffer` independently forces
instrumentation-inconclusive (`probe.mjs:124`). No silent loss path found.

**(3) Trusted-input check before use — PASS, with a circularity caveat in the gate.**
`verify-inputs.mjs` is stdlib-only and hashes the manifest bytes *before* `JSON.parse`
(`verify-inputs.mjs:8-12`). `verifyCriticalInputs` covers harness (full file-list +
bytes + size + mode), strace, both tree-assertion files, and the trace-lib directory
listing plus each declared library (`verify-inputs.mjs:33-43`). Ordering is correct at
every entry point: `launch.sh:16` runs the preflight before the `runtime.mjs` import at
line 17 and before any podman invocation; `noop.mjs:4-5` verifies before the dynamic
`import('./stream.mjs')`/`import('./strace-smoke.mjs')` at lines 6-7; `probe.mjs:146-149`
verifies before the dynamic imports and before the strace smoke and `/work/native`
creation; `worker.mjs:9-10` verifies before `import('./runtime.mjs')` and before any
storage-package import. Evidence confirms the live run verified 18 harness files,
strace, both tree assertions and 4 libs (`limits-proof.stdout.jsonl`
`critical-inputs-verified` frame).

Root of trust is non-circular on the launcher path: `$approved` is an operator-supplied
CLI argument, compared to `sha256sum` of the manifest at `launch.sh:14-15`, then
propagated to the verifier, to `PROBE_MANIFEST_SHA256` (`launch.sh:28`, visible in
`limits-proof-argv.json`), and to the checker (`launch.sh:82`). It *is* circular inside
`check-prep.py` — see MEDIUM-2.

**(4) strace smoke / fd3 fix — PASS on the envelope and the stdout/API separation.**
Envelope unchanged: the only podman-arg delta in the whole diff is `--env
PROBE_MANIFEST_SHA256` (`launch.sh:28`). `--cap-drop=ALL`, `no-new-privileges`, seccomp,
`--read-only`, `--network=none`, `--user 1000:1000`, pid/cgroup/ipc private and all
tmpfs sizes are byte-identical; `container-inspect.json` matches and `check-prep.py`
HostConfig assertions are unchanged. No ptrace/seccomp/capability relaxation anywhere.

Separation holds. In `probe.mjs:65` the bash script is `"$@" 3>&1 1>&5 | /bin/cat`:
the pipeline binds stdout to the pipe, `3>&1` points fd3 at that pipe, then `1>&5`
moves the tracee's stdout to parent pipe 5. So fd1 of bash = trace (handled at
`probe.mjs:89`), stdio[5] = tracee stdout (`probe.mjs:87`), stdio[4] = API events
(`probe.mjs:101`, with `worker.mjs` still asserting `eventFd === 4`). stdio index 3 is
now `ignore` and is overridden by the shell redirection. Tracer failure is preserved by
`-o pipefail`, and a `timeout` 124/137 still forces instrumentation-inconclusive
(`probe.mjs:124`).

Native path repeats the smoke before store work: `probe.mjs:153-156` runs it after
`plan`/`critical-inputs-verified` and throws INCOMPLETE on failure, strictly before
`fs.mkdirSync('/work/native')` at `probe.mjs:157`. The no-op evidence shows a real
passing smoke (`devFdExists: true`, `exitStatus: 0`, actual `execve/brk/mmap/openat`
trace bytes). See MEDIUM-3 for the fidelity gap.

**(5) Flag is not an authority boundary — PASS.** `probe.mjs:143` message is "Purpose
marker required; NOT authority or proof of owner consent"; `README.md` item 5 states it
in bold and adds that neither a manifest hash nor the recorded argv authenticates
consent, that owner authority and the Phase-E go remain external, and that no signing /
approval-service / nonce scheme was added. Nothing in the diff claims owner consent.

**Scope creep: none.** Every changed file maps to one of the five points; no product,
dependency, lockfile or Containerfile change, no unrelated fixes.

## Findings

**MEDIUM-1 (security/evidence) `test/store-probe/launch.sh:58`+`:62` — the recorded argv
is launcher self-attestation; nothing independently corroborates the host-side `timeout`
wrapper.** The `*-argv.json` and the exec'd process come from the same shell arrays, so
a modified launcher that *records* `--kill-after=30s 1800s` while *executing* a different
wrapper passes `host-receipt.mjs`, `checkExecutionIntent` and `check-prep.py` unchanged.
The negative control only covers the honest-but-drifted case where both change together.
Committed `launch.sh` is hash-bound (it is one of the 18 harness files in the manifest),
so this is only reachable via an out-of-tree launcher — which is exactly what
`deadline-negative.py:21` does. `README.md` item 3 does disclaim "not host-adversary
protection", so this is a documented limitation rather than a defect, but it should be
stated as such in the Phase-P report rather than implied to be a cryptographic bind.
*Fix direction:* cross-check the one independent artifact that already exists —
`container-inspect.json` `Config.Cmd`/`Image`/`HostConfig` — against the recorded argv in
`stream-check.mjs`, and/or record `/proc/<pid>/cmdline` of the live `timeout` process.

**MEDIUM-2 (correctness/test) `test/store-probe/check-prep.py:55` and `:74` — the gate's
"trusted" manifest SHA256 is derived from the manifest file under test, so the gate
cannot detect manifest substitution.** `check-prep.py` takes only an evidence directory;
it recomputes `hashlib.sha256(evidence/'input-manifest.json')` and feeds that to
`stream-check.mjs` as the trusted hash, and line 33-38 verifies inputs using the paths
and hashes the manifest itself declares. Any self-consistent manifest + receipt pair
passes. The non-circular anchor (`$approved`) exists only inside `launch.sh`, i.e. the
gate that Soma will read is strictly weaker than the launcher. *Fix direction:* add a
required `approved_sha256` positional to `check-prep.py`, assert it against the computed
hash and against every receipt's `manifestSha256`, and have the dispatch gate pass the
same value the launcher was given (`337e3bf2…`).

**MEDIUM-3 (test fidelity) `test/store-probe/strace-smoke.mjs:9` — the smoke exercises a
strictly simpler transport than the one Phase E will use, so it does not de-risk the
actual path.** The smoke runs `"$@" 3>&1 | /bin/cat`; `runChild` runs
`"$@" 3>&1 1>&5 | /bin/cat` (`probe.mjs:65`). The smoke therefore never exercises `1>&5`
onto a libuv stdio socket, never exercises the inherited fd4 API channel, and merges
tracee stdout into the trace stream (`trace` and `stdout` are the same bytes at
`strace-smoke.mjs:11-13`, so `trace.length > 0` would also be satisfied by tracee output —
harmless for `/bin/true`, which prints nothing, but the assertion does not actually prove
fd3 carried the trace). `check-prep.py:66-69` asserts only the inner `argv`, not
`transportArgv`, so the shape is unpinned by the gate. Since the whole point of Phase P
is to prevent Phase E failing on the first child, the smoke should use the production
shape. *Fix direction:* make the smoke spawn with `stdio[5]`/`stdio[4]` pipes and the
identical `3>&1 1>&5 | /bin/cat` script, assert fd5 carried the tracee's stdout and fd1
the trace separately, and have `check-prep.py` assert `transportArgv` equals the string
used in `probe.mjs`.

**MEDIUM-4 (design) `test/store-probe/probe.mjs:93` + `:126` — once the volume filter
engages it guarantees NOT-PASS for the rest of the run, so Soma's sanctioned filter can
no longer rescue a high-volume run.** The filter arms only at `evidence.bytes >=
192 MiB` (`probe.mjs:93`), i.e. in the last 64 MiB before the 256 MiB cap, and `filtered`
is driven by the *global* stream byte count, so every child after that point filters. Any
drop then sets `trace-filtered-uncertain-NOT-PASS` (`probe.mjs:126`), which the aggregate
turns into `control-failure-inconclusive` (`probe.mjs:204`) → checker INCOMPLETE. So both
branches of the volume path end INCOMPLETE: exceeding the cap gives
`limit-exceeded-inconclusive`, and avoiding it via the filter gives
`control-failure-inconclusive`. This is fail-closed and satisfies review point 2's
"no silent loss", but Soma's revision-2 instruction was that filtering be the way a
high-volume run *stays usable*. *Fix direction:* either state explicitly in the report
that filter activation is now a terminal condition and the 256 MiB budget must not be
approached, or arm the filter per-case with a per-case byte reserve so that one noisy
case cannot disqualify the other 17.

**LOW-1 (design) `test/store-probe/probe.mjs:129` — a single environment/instrumentation
failure now aborts the entire native matrix.** `runChild` throws for
`environment-failure-NOT-PASS` / `instrumentation-inconclusive-NOT-PASS`, so one route's
ABI, loader or ptrace failure forfeits the evidence for the remaining routes and the
other runtime; r2 recorded those as `control-failed-NOT-PASS` and continued
(`probe.mjs:179-183` still has that path, now largely unreachable). Correct per
"EACCES/import/ABI is never a pass", but it costs diagnostic yield on the first Phase-E
attempt. Consider completing the matrix and failing the verdict instead of throwing.

**LOW-2 (correctness, pre-existing) `test/store-probe/probe.mjs:48` — the retain regex's
`\b` prefix makes it miss `newfstatat`, the actual stat syscall on x86-64 glibc.** In
`newfstatat(` there is no word boundary before `fstat`/`stat`, so none of the
`stat\w*|lstat\w*|fstat\w*` alternatives match. Store-path stat calls are still retained
by the `line.includes(store)` branch, so the exposure/mutation correlation is unaffected,
but the documented rule "all path syscalls" is not what the regex implements, and
`selftest.mjs:122-130` only covers a `read(` line. Carried over unchanged from r2; the
new counters make the resulting drops visible, which is why it is LOW and not higher.

**LOW-3 (design) `test/store-probe/launch-argv.mjs:19-23` — the expected container command
is a literal in the checker, not a field in the approved manifest.** It is still
hash-bound (`launch-argv.mjs` is one of the 18 harness files whose hashes feed the
manifest SHA256, confirmed in `harness.SHA256SUMS:6`), so the binding is sound; the
weakness is reviewability — Soma cannot see the command Phase E will run by reading
`input-manifest.json`. Consider adding `capture.command` per `kind` to the manifest and
having `checkExecutionIntent` read it.

**LOW-4 (perf) `test/store-probe/worker.mjs:9-10` — `verifyCriticalInputs` now re-hashes
~10 MB (18 harness files, strace, 4 libs, two tree-assertion JSONs of 2.3 MB and 1.6 MB)
and re-parses the 4.6 MB manifest inside every one of the 42 traced children.** Correct
for the trust ordering requirement, and comfortably inside the 90 s child deadline, but
it runs under `strace -e trace=%file,%desc,%memory`, so each child's trace now opens with
several hundred lines of harness self-verification before any store work. Harmless today;
relevant if MEDIUM-4's budget is tightened.

**LOW-5 (robustness) `test/store-probe/launch-argv.mjs:7` — `findIndex` takes the *first*
`timeout` element.** A decoy `timeout --kill-after=Ns Ms podman run` sequence earlier in
argv (e.g. smuggled through an `env` assignment) would shadow the real wrapper, while
`argv.indexOf(manifest.image.id)` would still locate the real image so the command check
would pass. Only reachable from a modified launcher, i.e. the same trust boundary as
MEDIUM-1. Prefer matching the last candidate, or require the `timeout` element to be at a
fixed offset after the `env -i` prologue.

## Verification Gates

No gate re-run; the brief forbids it and reusable receipts exist at the reviewed commit.
Read:
- `phase-p-r3-check-prep-committed` — exit 0 — log `…/evidence/gate-phase-p-r3-check-prep-committed-01M67E04E7C845N1STHAWT292K.log`
- `phase-p-r3-noop-committed` — exit 0 — `…/gate-phase-p-r3-noop-committed-01M67E017CXXHH1VTAS8N9PA6Q.log`
- `phase-p-r3-kill-committed` — exit 0 — `…/gate-phase-p-r3-kill-committed-01M67E03G15FTYYS7N544HHDSX.log`
- `phase-p-r3-deadline-negative-committed` — exit 0 — `…/gate-phase-p-r3-deadline-negative-committed-01M67E02D0BS49138GH1G00XBD.log`

Read-only probes I ran (no container, no store, no writes to the worktree):
- `git diff 1bb62482..e3f07c16` in full, plus `git diff --stat` for the scope-creep check.
- `grep -rn '1800\|--kill-after=30' test/store-probe/` — confirmed no deadline literals
  remain in the receipt/checker chain.
- `grep -n` on `launch-argv.mjs`, `probe.mjs`, `strace-smoke.mjs`, `check-prep.py` for the
  cited line numbers.
- Read `limits-proof-argv.json`, `limits-proof-host-receipt.json`, `limits-proof-check.json`,
  `deadline-negative/deadline-negative-result.json`, and parsed the
  `critical-inputs-verified` / `strace-smoke` frames out of `limits-proof.stdout.jsonl`.
- `ls -la` + a manifest input-count summary to size the per-child verification cost
  (LOW-4) and confirm the `capture` block's derived deadline.

## Unmet criteria / test gaps

- Phase-P acceptance (input-manifest sha256, harness sha256, image ID + digest, full
  launch argv, whole-command deadline, limits proof) is met by the report and evidence;
  nothing in the delta regresses it.
- `check-prep.py` has no external manifest anchor (MEDIUM-2) — the gate is weaker than
  the launcher it certifies.
- The production fd3/fd5/fd4 transport is untested (MEDIUM-3); `probe.mjs:65` has never
  executed, so `1>&5` onto a libuv socket and fd4 inheritance through bash→strace remain
  unproven. First Phase-E child is still the first real test.
- `checkExecutionIntent`'s `kind === 'execute'` branch is covered only by data-only
  selftests with a fixture image; `execute-argv-NOT-RUN.json` was generated but never
  fed through the checker.
- `traceDecision`'s regex has no selftest covering `newfstatat`/`statx`/`pread`/`pwrite`
  (LOW-2).
- No test exercises the "filter engaged mid-run" path end to end (MEDIUM-4); the
  192 MiB threshold is unreachable in the mock.

## Residual risk

1. Phase E can still fail on its first child for a transport reason the smoke does not
   cover (MEDIUM-3). Non-destructive — it yields INCOMPLETE, not a false pass.
2. The deadline bind is an honest-launcher bind, not an adversarial one (MEDIUM-1); the
   report must not overstate it to Soma.
3. If Phase E's trace volume approaches 192 MiB, the run is unqualifiable regardless of
   which branch it takes (MEDIUM-4).
4. A substituted manifest+receipt pair passes `check-prep.py` (MEDIUM-2).
5. `/bin/bash` and `/bin/cat` are now runtime prerequisites for every traced child; they
   come from the image, which is pinned by ID and OCI digest, so this is covered — but
   they are not in the manifest's input inventory.

None of these is a correctness or data-loss risk: every failure mode lands on
INCOMPLETE / NOT-PASS, which is the required fail-closed direction.

## Fix directions (priority order, all pre-Phase-E, none blocking Phase-P sign-off)

1. MEDIUM-2: give `check-prep.py` a mandatory `approved_sha256` argument and assert it
   against the manifest and every receipt.
2. MEDIUM-3: make `straceSmoke` use the exact `3>&1 1>&5 | /bin/cat` shape with real
   fd4/fd5 pipes, assert the three channels separately, and pin `transportArgv` in the gate.
3. MEDIUM-1: cross-check the recorded argv against `container-inspect.json`
   `Config.Cmd`/`Image`/`HostConfig`, and state the self-attestation limit explicitly in
   the Phase-P report.
4. MEDIUM-4: decide and document whether filter activation is terminal, or scope the
   filter per case.
5. LOW-3: move the container command into `manifest.capture` so the manifest is
   self-describing for review.
6. LOW-1/LOW-2/LOW-5: continue the matrix instead of throwing; fix the `\b` regex gap and
   add a `newfstatat` selftest; harden the `timeout` element lookup.
