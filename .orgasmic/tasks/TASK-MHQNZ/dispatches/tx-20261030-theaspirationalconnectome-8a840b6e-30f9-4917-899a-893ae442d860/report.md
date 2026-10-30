# TASK-MHQNZ Phase P revision 2 — bounded static review (3 questions)

Scope as dispatched: read-only static reading of `test/store-probe/*` at
`1bb624822c16ea58971ee1fed031b55b67a81b97` plus the existing receipts in
`/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence`.
No container started, no store created/opened, no matrix run, no gate re-run.
Worktree left untouched (no commits, no edits, no new files in the tree).

## Verdict

**APPROVE** for Phase P revision 2 (prep only). All three dispatched questions
answer in the affirmative on the committed code and the existing receipts. One
MEDIUM finding (a host-receipt assertion that cannot fail) and three LOW
findings; none of them invalidates the prep artifacts or the kill/no-op proofs,
and none is an unmet Phase P acceptance criterion. Phase E remains correctly
gated (approved-manifest SHA256 + `--section native` + owner OK).

## Answers to the three questions

### (1) Engine selection — YES, with no silent fallback

- `test/store-probe/runtime.mjs:16-21` maps `bun`+`old` → `/runtime/bun-old/bun`,
  version `1.4.2`, condition `bun`, `--no-install`; `bun`+`new` →
  `/runtime/bun-new/bun`, `1.4.4`; `node` → `/runtime/node/node`, `22.23.3`,
  condition `import`. `launch.sh:30-31` mounts the two Bun binaries read-only at
  those distinct paths from separate positional arguments; the executed gate argv
  (`evidence/proof-argv.txt`, brief header) supplies the oracle's
  `pr361-v1-.../bin/bun` as old and `bun-1.4.4-linux-x64/bun` as new.
- The `bun` condition really is the source entry: old
  `@animalabs/{context-manager,agent-framework,membrane}/package.json` declare
  `exports["."].bun = ./src/index.ts` vs `import = ./dist/src/index.js`
  (verified by reading the fixture package.json files); Chronicle has no
  `exports` and resolves via `main: index.js` in both conditions, which
  `runtime.mjs:23-27` handles explicitly. `input-manifest.json.runtimeRoutes`
  shows exactly `.../src/index.ts` for bun cm/af rows and `.../dist/src/index.js`
  for node rows, and `check-prep.py:46-50` asserts that shape.
- Executable SHA before import: `worker.mjs:61-68` asserts actual runtime,
  version, `process.execPath === spec.executable`, and
  `hash(process.execPath) === manifest.runtimeBinaries[binaryKey].sha256` from
  the host-pinned `/inputs/input-manifest.json`; the first dynamic `import()` is
  only at `worker.mjs:99`. `launch.sh:13-14` refuses unless the manifest file
  hashes to the operator-approved SHA256, so a swapped binary or a swapped
  old/new mount fails closed.
- Per-operation logging: every child (seed `write`, control `verify`, attempt
  `attempt`) emits `runtime-verified` (executable + its SHA256),
  `input-tree-verified`, and one `resolved` + `transitive-resolved` event per
  package carrying `entry`, `entrySha256` and version
  (`worker.mjs:68,81,93`), all framed onto stdout via `probe.mjs:100`.
  `verifyTree()` additionally hashes every file of the mounted tree against
  `old-tree.json`/`new-tree.json`.
- Cross-version rows: `runtime.mjs:9` declares `['old-reader-to-new-store',
  'new', 'old']`; `probe.mjs:155-168` seeds/seals the **new** master, `cpSync`s a
  fresh writable clone per attempt, and runs the attempt with `tree = reader =
  'old'` → old executable + old conditional entry on a clone written by the new
  writer. Masters are re-inventoried and asserted unchanged afterwards
  (`probe.mjs:174-176`).
- No fallback path exists: `packageEntry` throws when the condition key is
  absent (`runtime.mjs:24-25`), `prepare.py:190` uses the same keyed lookup
  (KeyError, no default), `worker.mjs:79` rejects any resolution that differs
  from the declared conditional entry, `worker.mjs:85` pins
  `0.22.2/0.14.1/0.4.0` vs `0.30.0/0.24.0/0.5.1`, and `worker.mjs:88-94` rejects
  mixed transitive trees. Node rows are labelled
  `native-format component probe; NOT actual old-reader proof` in the machine
  output (`probe.mjs:180`), the README (`README.md:89`) and the report (§Correct
  runtime scope).

### (2) Capture lifecycle — YES

- Evidence leaves before the stop: `stream.mjs:11-31` writes each frame with a
  looping `fs.writeSync(1, …)` (no buffering, no async queue); `launch.sh:56,68`
  redirect container stdout into the evidence directory. `probe.mjs:79-102`
  frames child stdout/stderr, strace lines and API events as they arrive, and
  `probe.mjs:116` emits `attempt-flush` at the end of every attempt. The forced
  kill receipt proves it empirically: `capture-kill.stdout.jsonl` holds 4
  verified frames (`plan`, `limits-proof`, `mock-event`, `mock-inventory`),
  `capture-kill.exit = 137`, `capture-kill-check.json` = `INCOMPLETE`,
  `final: null`.
- Traces keep real detail: `probe.mjs:59-60` runs `/tools/strace -f -ttt -r -T
  -yy -s 256 -e trace=%file,%desc,%memory,%process -o /dev/fd/3`, i.e. syscall +
  path/decoded FD + flags/mode + result + absolute and relative timestamps +
  duration + PID, and each line is streamed verbatim (base64) as its own frame
  with a `receivedRelativeNs` stamp (`probe.mjs:76-91`). No hash is substituted
  for discarded bytes anywhere.
- Caps: `stream.mjs:4-6,33-39,51-56` — 256 MiB total with a 4096-byte terminal
  reserve and a 1 MiB frame cap; on reach it emits
  `limit-exceeded-inconclusive` and throws `EvidenceLimitError`
  (→ exit 2, `probe.mjs:194`). Filtering at 192 MiB (`probe.mjs:86-91`) retains
  any line containing the store path plus all path/mapping/lock/close syscalls,
  so store paths are preserved by construction.
- No RW mount: all eleven binds in `launch.sh:26-36` are `:ro`, the root is
  `--read-only`, and `check-prep.py:72-74` asserts `ReadonlyRootfs` and
  `all(not m['RW'] for m in Mounts)`. The captured `/proc/self/mountinfo`
  (`limits-proof.stdout`) shows only `/tmp` (128 MiB) and `/work` (1600 MiB) as
  writable tmpfs; `/dev` and `/dev/shm` are mounted `ro`.

### (3) Checker / completeness — YES

- `stream.mjs:77-82` requires all three: a normal terminal (`status ===
  'complete'`), a `plan` frame whose predeclared case list equals the expected
  list **and** a `case-result` set equal to it (the 18
  `runtime/route/scenario` IDs in `input-manifest.json.capture.expectedCases`,
  matching `probe.mjs:133` exactly), plus the host exit/deadline receipt.
- The host cannot synthesize a terminal: `stream-check.mjs` only reads; it never
  writes frames (its one write, line 22, extracts the already chain-verified
  `limits-proof` text). `host-receipt.mjs` emits exit/time/digest facts only.
  `stream-check.mjs:24` additionally binds the receipt's stdout
  digest/size to the bytes it verified, so host-side appending breaks the check.
- Duplicates/gaps/truncation: sequence+previous-chain+per-record SHA+running
  chain (`stream.mjs:60-68`), `data after final frame` rejection, sorted-list
  equality on case IDs (catches duplicates and gaps), final count/chain/bytes
  cross-check, `lastByte !== 10` truncation check, oversized-frame and
  size-over-cap checks. A partial trailing line also fails `JSON.parse`.
- The two receipts support it: `limits-proof-check.json` = COMPLETE, 6 records,
  `caseIds: ['mock/noop']`, exit 0; `capture-kill-check.json` = INCOMPLETE with
  a 4-record verified prefix and no final, enforced by `launch.sh:83-85`
  (`rc == 137 && checked == 2`) and re-verified data-only by
  `check-prep.py:51-62`. Phase-E failure paths all collapse to INCOMPLETE: any
  `NOT-PASS` row makes the terminal `control-failure-inconclusive`
  (`probe.mjs:186-188`), a thrown orchestrator error yields
  `fatal-inconclusive` (`probe.mjs:190-194`), and `--section d` yields
  `D-pending-inconclusive` + exit 78 with no `plan` frame.

## Findings

**MEDIUM `test/store-probe/host-receipt.mjs:12`: the "deadline receipt" asserts
constants, not the deadline actually used.** `deadlineSeconds: 1800` and
`killAfterSeconds: 30` are hardcoded in the receipt generator, and
`stream.mjs:82` then checks the receipt against the same hardcoded 1800/30 —
the assertion cannot fail. Failure scenario: if `launch.sh:40` is ever edited to
`timeout --kill-after=10s 600s` (or an operator runs an older/newer launcher),
the run still produces `deadlineSeconds: 1800` and the checker still reports
COMPLETE, so a matrix executed under a different envelope would be reported as
satisfying the declared 1800 s whole-command deadline. `argvSha256` pins the
argv file but nothing parses or compares its content, and neither the receipt nor
the checker compares against `manifest.capture.deadlineSeconds`/
`killAfterSeconds` (both present and correct in the manifest).
Fix direction: pass the two values from `launch.sh` into `host-receipt.mjs`
(parsed from, or written alongside, the recorded argv) and in
`checkCompleteness` compare `receipt.deadlineSeconds/killAfterSeconds` to
`manifest.capture.*` rather than to literals; `runtime.mjs:14`
(`wholeDeadlineSeconds`) should be the single source.

**LOW `test/store-probe/probe.mjs:86-91`: filtered-out trace lines are not
counted.** When the 192 MiB filter engages, non-matching lines are dropped
silently; `traceLines` counts kept lines only, and `trace-filter-enabled` records
the rule but no volume. Failure scenario: a noisy Phase E attempt crosses
192 MiB, the reviewer sees a complete-looking trace stream for that attempt with
no way to tell that (say) 40 % of lines were dropped, weakening the
exposure/mutation correlation the filter was supposed to protect. Soma's "never
replace with a hash" requirement is met; the gap is quantification only.
Fix direction: keep `droppedLines`/`droppedBytes` counters and emit a periodic
and per-attempt `trace-filter-stats` frame (and include them in `result.json`).

**LOW `test/store-probe/worker.mjs:18` (and `/harness`, `/tools/strace`): the
in-container trust chain does not cover every pinned input.** The approved
manifest SHA256 is the only host-side interlock in `launch.sh`, and in-container
the worker verifies the runtime binary and every file of the mounted old/new
trees — but it reads `/inputs/old-tree.json` / `/inputs/new-tree.json` without
checking them against the `old-tree-assertions`/`new-tree-assertions` entries of
the manifest it already trusts, and nothing at run time hashes `/harness/*` or
`/tools/strace` (both inventoried in the manifest, with
`harness.SHA256SUMS` available). Failure scenario: a Phase E launch that skips
`check-prep.py` (which does verify all input hashes, `check-prep.py:29-36`)
mounts a stale tree-assertion file or an edited harness, and the in-container
assertions silently validate against the wrong expectations. Fix direction:
have `worker.mjs` hash `expectedTree` against the manifest entry before using
it, have `probe.mjs` verify `/harness` and `/tools/strace` against the manifest
inventory at startup, and/or make `launch.sh execute` run `check-prep.py` first.

**LOW `test/store-probe/probe.mjs:122` + `launch.sh:42`: `--phase-e-approved` is
not an independent interlock.** The flag the orchestrator requires is hardcoded
in the committed launcher's `execute` argv, so the only real approval gate is the
operator-supplied approved-manifest SHA256 (`launch.sh:13-14`) and the
`--section native` guard. This matches the intent (no default execution) but the
flag itself carries no approval evidence. Fix direction: if an explicit interlock
is wanted, require the approved manifest SHA256 as the flag's value
(`--phase-e-approved <sha256>`) and compare it in-container against the mounted
manifest.

## Verification Gates

No gate was run or re-run in this review (brief: read-only, no container, no
matrix). Reused receipts, all at the reviewed commit:

- `phase-p-r2-prep-committed` — exit 0 —
  `python3 test/store-probe/check-prep.py <evidence>` — log
  `evidence/gate-phase-p-r2-prep-committed-01M67CGH37SHA28WAA7Y21ZA9G.log`
  (data-only: syntax, 17 mock tests, input hashes/modes, HostConfig, kernel
  limits, both capture checks).
- `phase-p-r2-noop-committed` — exit 0 — `launch.sh proof …` — log
  `…-noop-committed-01M67CGE4V8VCA1S8HFERNSQ2T.log`; artifacts
  `limits-proof-check.json` (COMPLETE, 6 records), `limits-proof.exit` = 0.
- `phase-p-r2-kill-committed` — exit 0 — `launch.sh capture-kill …` — log
  `…-kill-committed-01M67CGFRETXW0R8T2RXAS9MER.log`; artifacts
  `capture-kill.exit` = 137, `capture-kill-check.json` (INCOMPLETE, 4 records,
  `final: null`), `capture-kill-host-receipt.json` (`sigkillExit: true`).

Static evidence read directly in this review: `runtime.mjs`, `worker.mjs`,
`probe.mjs`, `stream.mjs`, `stream-check.mjs`, `host-receipt.mjs`, `noop.mjs`,
`selftest.mjs`, `check-prep.py`, `prepare.py`, `launch.sh`, `Containerfile`,
`README.md`; `input-manifest.json` (capture/runtimeBinaries/runtimeRoutes/
oldLaunch/newPackages/image/inputs), the limits-proof mountinfo and cgroup
output, both `*-check.json`, both `*-host-receipt.json`, both `*.exit`, the
`capture-kill` frame sequence, and the old fixture `package.json` entry fields
for the four `@animalabs` packages.

## Unmet criteria / test gaps

- No Phase P acceptance item is unmet: input-manifest SHA256
  (`7cb8c032748d678d5502c682520d52d903a05d4a41102ac5e09417e6447f3af5`, pinned in
  both committed gate argvs), `harness.SHA256SUMS`, image id
  `5d9c60ee…` + manifest digest `sha256:44b68815…`, both full launch argvs, the
  1800 s/30 s whole-command deadline, and the limits proof are all present.
- Declared and accepted gap (report §Residual Risk): the strace/ptrace channel is
  never exercised in Phase P. `/tools/strace` is a host binary run inside
  `ubuntu:24.04` under cap-drop ALL and the default seccomp profile, writing to
  `/dev/fd/3`, with libunwind supplied via `LD_LIBRARY_PATH=/tools/lib`. If
  ptrace, the loader or `/dev/fd` misbehaves in that envelope, all 42 Phase E
  children fail as environment failures after consuming the 1800 s budget. A
  `strace -o /dev/fd/3 /bin/true` smoke frame inside the existing no-op container
  would retire this for free and is worth adding before Phase E.
- The whole `worker.mjs` path (store opens, instrumentation, Bun source import,
  `Bun.resolveSync` with a file path as the `from` argument, napi named-export
  interop under Node) is unexecuted by design; `selftest.mjs` covers only
  data/mock logic. This is Phase P's intent, not a defect, but it means the first
  Phase E run doubles as the first integration test of that file.
- The chain is unkeyed: it proves integrity/truncation against a partial or
  corrupted capture, not authenticity against the host that wrote the file (the
  receipt is derived from the same bytes). The report states this limitation
  explicitly (§Live evidence, §Residual Risk).

## Residual risk

Phase E can still consume the full deadline and return INCOMPLETE for
environment reasons (ptrace, Bun source transpile cost, napi ABI), and the
stdout cap can be reached by a noisy matrix; both outcomes are correctly
classified as non-passes. A drifted launcher deadline would currently go
unreported (MEDIUM finding). `refusalBeforeMutation` stays `null` by design, so
any mutation-safety conclusion still requires manual syscall correlation. (b)
remains pending the A+B+D tarball with `dIntegrationPin: null`.

## Fix directions (priority order)

1. Derive the receipt's `deadlineSeconds`/`killAfterSeconds` from the launcher
   and check them against `manifest.capture.*` instead of literals.
2. Add a `strace -o /dev/fd/3 /bin/true` (plus `/dev/fd` existence) smoke frame
   to the no-op proof before spending a Phase E budget.
3. Count and frame filtered-out trace lines/bytes.
4. Close the in-container trust seams: hash `expectedTree`, `/harness/*` and
   `/tools/strace` against the pinned manifest at startup.
