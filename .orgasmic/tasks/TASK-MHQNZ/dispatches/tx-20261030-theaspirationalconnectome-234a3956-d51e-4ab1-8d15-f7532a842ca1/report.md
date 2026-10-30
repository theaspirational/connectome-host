# TASK-MHQNZ — ONE Phase-E native diagnostic observation

## Changed

**No harness/code changes, no commits, no push/deploy/merge.** HEAD remains
`1cfe32a1c381a2d67731a6c2b8e3989874947df9`; worktree clean before and after.
Exactly ONE native invocation via the pinned launch.sh; no retry, relaunch, fix,
flag/environment/argv change, extension or privilege/limit relaxation.

**Authority:** Soma, programme qualifier, approved this exact diagnostic; task
operator Tarn dispatched it. Human owner Vasyl did NOT make this personal decision.
The exclusive intent is an accidental-replay guard, NOT authorization.
This report is a diagnostic observation, **NOT a compatibility/release claim**.

### Bound identities (recomputed before launch)

- Harness aggregate: `7f5abf40a4e61c1a72ccfbe2287ab6f8af467402b7f16f80a9a546e3214e22c3`.
- Manifest / externally passed anchor: `3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2`.
- Image ID: `5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6`.
- OCI digest: `sha256:44b688151db6337cfb11114ea43a644311251e7d87c858dc8f252bc906b4a3dd`.
- Exact native argv JSON SHA256: `6b0642b55a5f93a1b63a25e19e396dfe34b946a24e744d6e426f56c95b199078`.

The approved argv contains R5 evidence paths. To preserve those exact bytes,
launch.sh received the original R5 evidence argument (not substituted with this
round's directory). It streamed there, and all native raw captures/receipts/inspect
were copied intact to THIS round's evidence before cleanup. Both locations retain
the originals. `approved-native-argv.json` and actual `execute-argv.json` are byte
identical and rehashed after execution; no path was changed to fit the new run.

## Verification Gates

### Pre-launch conditions

`phase-e-prelaunch-verified` passed at clean pinned HEAD. Raw commands, stdout,
stderr and exit statuses are retained as `pre-*.command.json`, `.stdout`, `.stderr`.

1. HEAD exact; worktree clean (`pre-head`, `pre-worktree`).
2. Recomputed every harness file hash/size/mode and aggregate; manifest hash;
   image Id+Digest; the argv about to launch equals the approved bytes/hash.
   `launch.sh argv` was a non-launch rendering/preflight only. Both actual launch
   argv and receipt later confirm the same approved hash.
3. `podman ps -a` and inspect: no running project/image container, approved native
   name unused; three prior stopped proof/leaf-measure containers only. Recursive
   `lsof +D` and `fuser -v` for each input directory returned1/no users (empty output,
   not an enforcement failure). Point-in-time trusted-host checks; no concurrent
   input writer was permitted during verification→child use. RO is NOT hostile-host
   immutability. All raw ps/inspect/lsof/fuser evidence saved.
4. No prior native attempt/receipt for the manifest and no probe.mjs container.
   `pre-prior-native-search.json` records searched evidence dirs (including R1–R5)
   and container IDs, no matches. First search tooling overmatched the unrelated
   TASK-ZPVAY filename `native-runtime-versions.json` as a `native-run*` artifact.
   That failed data-only gate did NOT launch anything. The preserved initial search
   and `pre-search-disambiguation.json` show it was neither this task nor a native
   execution receipt. Corrected only the evidence filename matcher and rechecked;
   all actual pre-launch conditions passed before launch. No native retry occurred.
5. Fresh private `execution-intent.json` mode0600, created O_EXCL|O_NOFOLLOW with
   fresh256-bit nonce and fsync(file)+fsync(directory). Also an exclusive
   `native-intent-<manifest-sha>.json` claim in R5 evidence prevents accidental
   cross-round replay. Includes manifest/harness/image/argv identities; contains
   authorization:false. Repetition refuses on existing files, no signing/service.

### Gates and outcomes

| Gate | Result | Meaning |
|---|---|---|
| phase-e-prelaunch | exit1 | Evidence-search false positive described above; no native launch |
| phase-e-prelaunch-verified | exit0 | All actual pre-launch requirements match |
| phase-e-native-once | exit0 wrapper | Exactly one invocation ended and evidence/stopped state saved; NOT a compatibility pass |
| phase-e-stream-and-correlation | exit0 wrapper | Independent checker reproduced INCOMPLETE and preserved prefix; correlated first failed control |


- `gate-phase-e-prelaunch-verified-01M67H5QXRV9KKH4XEN61DPXGV.log`

- `gate-phase-e-prelaunch-verified-01M67H5QXRV9KKH4XEN61DPXGV.log`

- `gate-phase-e-native-once-01M67H6R51YNXP250Y1CKFX2VX.log`

- `gate-phase-e-stream-and-correlation-01M67H8CKVTPKXJTA5N306H1WV.log`


Exact gate commands:

```sh
orgasmic dispatch gate phase-e-prelaunch-verified -- python3 /home/orgasmic/.orgasmic/runs/run-01M67H32GBA8TE7ZXCZNX9ZC2N/evidence/prelaunch.py
orgasmic dispatch gate phase-e-native-once -- python3 /home/orgasmic/.orgasmic/runs/run-01M67H32GBA8TE7ZXCZNX9ZC2N/evidence/native-once.py
orgasmic dispatch gate phase-e-stream-and-correlation -- python3 /home/orgasmic/.orgasmic/runs/run-01M67H32GBA8TE7ZXCZNX9ZC2N/evidence/post-check.py
```

### `launch-invocation.json`
```json
[
  "test/store-probe/launch.sh",
  "execute",
  "5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6",
  "f-host-2b-task-mhqnz-r5-noop",
  "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules",
  "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final",
  "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe",
  "/opt/node-v22.23.3-linux-x64/bin/node",
  "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun",
  "/srv/ci-fixtures/bun-1.4.4-linux-x64/bun",
  "/usr/bin/strace",
  "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs",
  "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence",
  "3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2",
  "native"
]
```

### Full native command (exact JSON bytes separately preserved)
```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r5-noop --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw,nosuid,nodev,size=1600m,mode=1777 --tmpfs /tmp:rw,nosuid,nodev,noexec,size=128m,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/probe.mjs --phase-e-approved --section native
```

Unchanged envelope: rootless orgasmic; container1000:1000; RO root/code/all11
input binds; cap-dropALL, NNP/default seccomp, networknone/noports, private PID/
cgroup/IPC; memory4GiB, additional swap0, 2CPU,256PIDs, core0; writable tmpfs
≤1808MiB overall (≤1920MiB ceiling). Whole deadline1800s+30s; child90s+5s;
capture256MiB/filter192MiB. No RW host volume/home/credential/socket mount.
No live data/providers/connectors/endpoints, installs or network; synthetic-only
paths. D section(b) remained null/refused; only section native was invoked.

### Diagnostic outcome — INCOMPLETE / UNQUALIFIED

The first preparation control, **Node / Chronicle / old seed**, failed while
installing API instrumentation: `TypeError: Cannot redefine property: create`.
Stack: Object.defineProperty → instrument(worker.mjs:46) → worker.mjs:94.
The coarse stage label is `import-chronicle`, but syscall+API+source correlation
locates the failure AFTER loading the native module and BEFORE instrumentation
was ready or the store-open path. It is a harness instrumentation failure, NOT a
storage-format refusal, reader safety barrier, or compatibility result.

No positive seed/reopen control completed. **0/18 cases completed, 1/≤42 child
operations started** (the initial seed, not a cross-version case). The run stopped
immediately as designed; no restarts. All cases below are INCOMPLETE/UNQUALIFIED;
missing cases are simply unobserved, not negative findings.

### Control + API + syscall correlation (before any interpretation)

- Parent verified old10628files/204435070bytes and new7149files/88620916bytes
  (frame3), and reverified old immediately before child spawn (frame6).
- Strace wraps the whole worker (frame8: PID32 execve Node22.23.3, import condition,
  operation write and synthetic master path). Runtime binary hash and conditional
  entries/transitive/native payload hashes passed (API frames580,656–858).
- Native `.node` file successfully opened O_RDONLY|O_CLOEXEC and mapped PRIVATE
  (frames1072–1081; example below). Those are input-module loads, not store writes.
- API failure frame1103: wall1793401775431ms, monotonic284515322439536ns;
  syscall frame1099: PID32 at1793401775.432019, write(fd4, failure event...) corroborates
  the failure event and channel. API frame1104 finished cleanClose:false;
  syscall1267 exit_group(1) at1793401775.438849, frame1269 process exit1.
  Stream record order across pipes is not treated as execution-time ordering;
  wall/monotonic/syscall timestamps and PID correlate the boundaries.
- No instrumentation-ready/open-enter/open-return/rows-exposed/application-rows-
  exposed API event. The exact master-store path occurs in trace only as the
  execve argument, not an observed store access. This is a narrowly observed
  failed-control prefix, **not proof of a reader refusal, no-mutation barrier,
  exposure safety or downgrade behavior**. No case had valid completed controls.
- Automatic result fields such as refusalBeforeRowExposure:true are retained RAW
  but NOT adopted as safety conclusions because the control failed. No inference
  from open-return alone, exception, unchanged hashes or absent case data.

`control-api-syscall-correlation.json` gives exact frame IDs/API fields/syscalls;
`api-events.json`, `syscall-trace-decoded.txt`, `stream-artifacts.json` and original
`execute.stdout.jsonl` preserve all evidence.

Selected actual syscalls (PID, absolute+relative time, path/flags/result retained):

```text
[frame 8] 32    1793401775.276147 (+     0.000000) execve("/runtime/node/node", ["/runtime/node/node", "--experimental-import-meta-resolve", "/harness/worker.mjs", "/inputs/old", "chronicle", "write", "/work/native/node/master-chronicle-old", "4", "/inputs/old-tree.json", "node"], 0x7ffd3ac08760 /* 14 vars */) = 0 <0.000107>
[frame 1072] 32    1793401775.431237 (+     0.000015) openat(AT_FDCWD</work/native/node/seed-chronicle-old/scratch>, "/inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node", O_RDONLY|O_CLOEXEC) = 20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node> <0.000008>
[frame 1073] 32    1793401775.431257 (+     0.000020) read(20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node>, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0@\0\0\0\0\0\0\0\310I-\0\0\0\0\0\0\0\0\0@\08\0\v\0@\0!\0\37\0\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0h\2\0\0\0\0\0\0h\2\0\0\0\0\0\0\10\0\0\0\0\0\0\0\1\0\0\0\4\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\\n\7\0\0\0\0\0\\n\7\0\0\0\0\0\0\20\0\0\0\0\0\0\1\0\0\0\5\0\0\0\\n\7\0\0\0\0\0\\~\7\0\0\0\0\0\\~\7\0\0\0\0\0\324A\30\0\0\0\0\0\324A\30\0\0\0\0\0\0\20\0\0\0\0\0\0\1\0\0\0\6\0\0\0000\260\37\0\0\0\0\0000\320\37\0\0\0\0\0"..., 832) = 832 <0.000005>
[frame 1074] 32    1793401775.431274 (+     0.000016) fstat(20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node>, {st_mode=S_IFREG|0644, st_size=2970120, ...}) = 0 <0.000004>
[frame 1075] 32    1793401775.431295 (+     0.000021) mmap(NULL, 2131408, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node>, 0) = 0x75a10da00000 <0.000009>
[frame 1076] 32    1793401775.431318 (+     0.000023) mmap(0x75a10da77000, 1597440, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node>, 0x76000 <unfinished ...>
[frame 1078] 32    1793401775.431336 (+     0.000013) <... mmap resumed>) = 0x75a10da77000 <0.000014>
[frame 1080] 32    1793401775.431342 (+     0.000002) mmap(0x75a10dbfd000, 32768, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node>, 0x1fb000) = 0x75a10dbfd000 <0.000006>
[frame 1081] 32    1793401775.431361 (+     0.000019) mmap(0x75a10dc05000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node>, 0x202000) = 0x75a10dc05000 <0.000008>
[frame 1083] 32    1793401775.431411 (+     0.000029) close(20</inputs/old/node_modules/@animalabs/chronicle/chronicle.linux-x64-gnu.node> <unfinished ...>
[frame 1084] 34    1793401775.431415 (+     0.000004) mprotect(0x75a0e028e000, 49152, PROT_READ|PROT_WRITE <unfinished ...>
[frame 1099] 32    1793401775.432019 (+     0.000008) write(4<UNIX-STREAM:[42593222->42593221]>, "{\"wall\":1793401775431,\"monoNs\":\"284515322439536\",\"runtime\":\"node\",\"condition\":\"import\",\"executable\":\"/runtime/node/node\",\"kind\":\"failure\",\"stage\":\"import-chronicle\",\"name\":\"TypeError\",\"code\":null,\"message\":\"TypeError: Cannot redefine property: create\",\"sta"..., 448 <unfinished ...>
[frame 1100] 34    1793401775.432024 (+     0.000005) <... mprotect resumed>) = 0 <0.000012>
[frame 1101] 32    1793401775.432035 (+     0.000010) <... write resumed>) = 448 <0.000011>
[frame 1102] 34    1793401775.432038 (+     0.000002) mprotect(0x75a0e0323000, 40960, PROT_READ|PROT_WRITE) = 0 <0.000004>
[frame 1267] 32    1793401775.438849 (+     0.000049) exit_group(1) = ?
[frame 1269] 32    1793401775.440144 (+     0.001259) +++ exited with 1 +++
```

### Node — compiled/import/dist native-format component probe

NOT proof about the actual old Bun reader. Node22.23.3 binary/hash verified;
AF0.22.2 and CM0.14.1 dist entries were resolved/hashed, not imported for this
Chronicle control. Chronicle0.4.0 uses its index.js native-module wrapper.
No completed positive control or accepted store observation.

| Route | Scenario | Status | Observation |
|---|---|---|---|

| chronicle | old-to-old | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| chronicle | new-to-new | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| chronicle | old-reader-to-new-store | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| cm | old-to-old | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| cm | new-to-new | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| cm | old-reader-to-new-store | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| af | old-to-old | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| af | new-to-new | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |

| af | old-reader-to-new-store | INCOMPLETE / UNQUALIFIED | Not reached; first old Chronicle seed instrumentation failed; no case conclusion |


### Bun — actual source conditions; old1.4.2 / new1.4.4

**No Bun child was reached.** These are the approved intended source-runtime
identities only, not executed/imported source results. No actual-old-reader result.

| Route | Scenario | Status | Observation |
|---|---|---|---|

| chronicle | old-to-old | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| chronicle | new-to-new | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| chronicle | old-reader-to-new-store | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| cm | old-to-old | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| cm | new-to-new | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| cm | old-reader-to-new-store | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| af | old-to-old | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| af | new-to-new | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |

| af | old-reader-to-new-store | INCOMPLETE / UNQUALIFIED | Not reached; no Bun observation or case conclusion |


### Capture, timing, chain and stopped state

- Checker: **INCOMPLETE**, 1274 verified records,
  815286 stdout bytes; fatal-inconclusive terminal present,
  but no normal complete terminal and no expected case records. Prefix intact.
- Trace retained **1249lines /196433bytes**; dropped **0lines /0bytes**;
  filter not activated, cap not reached. `trace-filter-stats.json` records start/end.
- Container command elapsed **1.466006111s**; launcher wrapper elapsed
  **1.641581212s**. Actual deadline1800s/kill-after30s, no timeout or SIGKILL.
  Container exit1, launcher exit2. The successful gate wrapper records completion
  of this diagnostic task, not success of the underlying experiment.
- Verified final chain head: `cce2610e871c41d84647833da7d69557b8d0fb80a4a50ffebdfd9222448bdf08`.
- Raw stdout SHA256: `94601c9465e594bbeba4fb51b36a5eae3601ce3a69f0c44ac36299c47bf9ae29`;
  stderr empty. Final's data-chain hash is separately retained in checker output.
- Container `2055ae228c77e5f3244fc51c013d997108af7f75190a12647fe2aa9b272d7fcc`
  was STOPPED after launch and again at end; exit1. `post-stopped.json`,
  `post-container-inspect.json`, `end-container-inspect.json` preserve state.
  No timeout occurred; no safety kill needed. End ps shows no running image
  containers. Evidence was saved before any cleanup. **No cleanup performed**:
  removed list[] in cleanup.json; native and prior owned stopped proofs retained.

The chain proves integrity/truncation, **not that the intended experiment happened**,
compatibility, release qualification, owner consent, exposure or mutation safety.

### `execute-host-receipt.json`
```json
{
  "kind": "execute",
  "exitStatus": 1,
  "started": "1793401774433264424",
  "finished": "1793401775899270535",
  "elapsedSeconds": 1.466006111,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/execute-argv.json",
  "argvSha256": "6b0642b55a5f93a1b63a25e19e396dfe34b946a24e744d6e426f56c95b199078",
  "manifestSha256": "3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/execute.stdout.jsonl",
    "size": 815286,
    "sha256": "94601c9465e594bbeba4fb51b36a5eae3601ce3a69f0c44ac36299c47bf9ae29"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/execute.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### `post-stream-check.json`
```json
{
  "classification": "INCOMPLETE",
  "verifiedRecords": 1274,
  "verifiedBytes": 815286,
  "verifiedChain": "cce2610e871c41d84647833da7d69557b8d0fb80a4a50ffebdfd9222448bdf08",
  "caseIds": [],
  "inspectedContainer": "2055ae228c77e5f3244fc51c013d997108af7f75190a12647fe2aa9b272d7fcc",
  "final": {
    "status": "fatal-inconclusive",
    "totalCount": 1274,
    "dataChainHash": "33d4aafa6a0c9c49478c20a99eecb808623855056bf351411f623376c7e29667",
    "bytesBeforeFinal": 814842,
    "cap": 268435456
  },
  "error": "Error: INCOMPLETE: no normal terminal record",
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

### `launcher-exit.json`
```json
{
  "exitStatus": 2,
  "startedNs": 1793401774334769475,
  "finishedNs": 1793401775976350687,
  "elapsedSeconds": 1.641581212
}
```

### `post-stopped.json`
```json
{
  "container": "2055ae228c77e5f3244fc51c013d997108af7f75190a12647fe2aa9b272d7fcc",
  "running": false,
  "status": "exited",
  "exitCode": 1,
  "observedAt": "2026-10-30T23:09:35.994597+00:00"
}
```

### `trace-filter-stats.json`
```json
[
  {
    "sequence": 7,
    "label": "seed-chronicle-old",
    "runtime": "node",
    "phase": "start",
    "filtered": false,
    "retainedLines": 0,
    "retainedBytes": 0,
    "droppedLines": 0,
    "droppedBytes": 0
  },
  {
    "sequence": 1270,
    "label": "seed-chronicle-old",
    "runtime": "node",
    "phase": "attempt-end",
    "filtered": false,
    "retainedLines": 1249,
    "retainedBytes": 196433,
    "droppedLines": 0,
    "droppedBytes": 0
  }
]
```

### `cleanup.json`
```json
{
  "removed": [],
  "reason": "No cleanup performed. Native diagnostic and prior owned proof containers are stopped and retained for audit; no unrelated containers touched.",
  "nativeContainer": "2055ae228c77e5f3244fc51c013d997108af7f75190a12647fe2aa9b272d7fcc",
  "nativeStopped": true
}
```

### Inherited evidence and scope

R5 evidence at `/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence` supplies the exact approved manifest, image provenance and
argv. These unchanged byte identities were rechecked here; no earlier gate pass
is presented as this run's pass. R5 no-op kernel/HostConfig limits proof remains
context for the exact unchanged envelope; current native inspect was separately
checked against actual argv by stream-check. No image rebuild or environment
provenance alteration. The historical first-build inherited-environment disclosure
and selected clean offline no-RUN image provenance remain in R5's report/logs.

## Unmet Criteria

None for executing/reporting ONE approved diagnostic: INCOMPLETE is expressly a
valid task outcome. The native experiment itself remains **INCOMPLETE/UNQUALIFIED**:
no completed controls/cases, no old-reader→new-store observation. D remains pending
A+B+D integration tarball, pin:null. No release/compatibility decision was made.
No new commit was made, as specifically requested for this unchanged-code round.

## Residual Risk

- First-control instrumentation cannot redefine native JsStore.create; future
  investigation needs a new dispatch/pinned preparation and approval. No fix or
  retry is included or authorized by this report.
- No Bun/source-reader, new-reader, cross-version, store exposure/mutation,
  application-floor or D-stamp conclusion. Missing cases are unobserved only.
- RO binds and per-child verification trust the host/no concurrent writer;
  recorded argv/inspect/intent/chain are not hostile-host attestation or consent.
- Full workload trace budget and control/API/syscall correlation remain unqualified.
  This short unfiltered prefix neither validates the full42-child budget nor the
  storage behavior. No negative conclusion can be drawn from the early failure.
