# TASK-MHQNZ — r6 PREP: explicit observer + real import readiness

## Changed

Local commit **0cf9edff7810b3834cd5fc5bcde8be8c66364a0a**, directly on
**1cfe32a1c381a2d67731a6c2b8e3989874947df9**. Vasyl noreply identity and
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>` trailer. Ten harness files
changed; no product/SDK/dependency/lockfile/image/Containerfile changes, no push.
No operator manifest/private host path is committed.

**NO second store experiment, NO native section run this round.** Only authorized
prep: data tests, no-op/kill/deadline controls, and separately labeled real
**import-preflight**. No store constructor/open/read/write, provider construction
or endpoint was invoked by this harness. Parent only makes scratch directories;
import-only workers receive `-`, not a store path, and exit before store code.

**Authority:** Soma as programme qualifier; Tarn task operator dispatches. This is
NOT a personal decision by human owner Vasyl. Purpose flags/hashes/receipts are not
authority. A future native diagnostic needs a new external exact-manifest decision
and MUST NOT launch from this prep because the measured budget is NOT VIABLE.

Read IN FULL and copied into this evidence:
- Phase-E report `/srv/review-drop/TASK-MHQNZ-PHASEE-1.md`, SHA256
  `2f766adb61f0820299f75bc321760043c4cf64d2be527e4e4d2c626d88971360`.
- r5 delta review `/srv/review-drop/TASK-MHQNZ-R5-DELTA-REVIEW.md`, SHA256
  `b47f5926feb94b43e2c06d55e8404a666a80f96ae85e739589828fb5141e42da`.
Historical first native control failed `Cannot redefine property: create` at
worker.mjs:46←94. That observation is inherited context, NOT rerun here.

### Delta against the requested conditions

1. **Separate explicit observer, no native-property mutation.** observer.mjs uses
   Reflect.get/apply with original receiver/argument values, or Reflect.construct
   with original constructor/newTarget. No defineProperty/assignment/Proxy over
   SDK classes/prototypes. No SDK files, loader resolution, import maps/conditions,
   transitive exports or reader identity are patched/substituted. All SDK calls
   made by the harness now pass through observer.call/construct at their call sites.
   No arbitrary native method is silently skipped.
2. Observer is NOT async; returns the identical value/promise, rethrows identical
   errors, accesses a getter once with its original receiver. It attaches NO then/
   catch handlers and does not change promise rejection/handling/identity.
   api-return means function RETURNED ONLY, NOT async settlement. Explicit caller
   open-return/clean-close events occur after their existing awaits. Observation
   output failure remains an instrumentation failure, not silent loss.
3. **Honest API boundary:** ONLY harness-originated calls are observed. Native/SDK
   internal methods remain UNKNOWN at the API layer and are observed through the
   unchanged syscall capture where applicable. No hook/event absence means no read
   or mutation. Automatic absent-exposure/mutation and refusalBeforeRowExposure
   fields now remain NULL/unknown; observed exposure may make the latter false,
   never true just because an internal hook is missing. A regression test pins it.
4. Readiness inspects every applicable real exported callable descriptor without
   invoking it, refuses missing/noncallable targets, logs location/depth and all
   configurable/writable/enumerable/getter/setter flags plus facadeWrapped. The
   complete 16-target list and 64 real runtime/tree descriptors are below.
5. New import-preflight branch is BEFORE all store construction/open/read/write
   code. It imports actual route exports under unchanged resolution and strace,
   emits instrumentation-ready, then exits. All 12 runtime×tree×route children
   passed with zero observer calls/open/row events and zero native store-path
   references. It never enters the native loop/master/clone directory.
6. MEDIUM-1: measured actual import capture replaces the4× no-SDK guess. Imports
   are NOT called side-effect-free: initializer I/O is preserved/analyzed below.
   Full workload budget now says **STOP**; no cap increase/new capture architecture.

### Exact new identities

- Harness aggregate SHA256 (20 files; SHA256 of harness.SHA256SUMS):
  `f40b94d1be003c67800106bd561e2f586f05c40992f90a746b0e34df94ec68a6`.
- Input manifest / externally passed anchor: `bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0`.
- Image ID unchanged: `5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6`.
- OCI digest unchanged: `sha256:44b688151db6337cfb11114ea43a644311251e7d87c858dc8f252bc906b4a3dd`.
- NEW native argv JSON SHA256, **NOT RUN**:
  `335cf6eec684bfe298d534f04e732ce18d4224e060f96b414908a2b60412f736`.
- Executed import-preflight argv SHA256:
  `7f1d556c4b1def3db4aadebe3cc229059ebfafcdbd08897f80844f859cc04656`.

`payload-inventory-comparison.json` compares ALL non-harness/non-assertion-file
inventories against R5: identical pinned files/hash/size/mode, including complete
old/new SDK trees, sealed cache, AF0.30 driver, Bun binaries/provenance, Node,
strace/libs, locks/oracle. Fresh ignored new-r6/new-r6-final are unpacked CODE,
not stores. No installs/lifecycle hooks/network or native payload substitutions.
check-prep rehashed inputs AFTER import preflight; no input mutation found.

## Verification Gates

All REQUIRED final gates ran at CLEAN committed HEAD **0cf9edff7810b3834cd5fc5bcde8be8c66364a0a**:

- `phase-p-r6-import-preflight-committed`: exit0; `gate-phase-p-r6-import-preflight-committed-01M67HY7VZMS1KXXY0CVMSQ6QZ.log`.

- `phase-p-r6-noop-committed`: exit0; `gate-phase-p-r6-noop-committed-01M67HYRQCB0D0ZFZ2V2VMK019.log`.

- `phase-p-r6-kill-committed`: exit0; `gate-phase-p-r6-kill-committed-01M67HYTH0JFDQMNS3C0VRJT5F.log`.

- `phase-p-r6-deadline-negative-committed`: exit0; `gate-phase-p-r6-deadline-negative-committed-01M67HYSNQV9S3TBRBXH2J7WVQ.log`.

- `phase-p-r6-check-prep-committed`: exit0; `gate-phase-p-r6-check-prep-committed-01M67HYVJGDAE5Q5R7B3JEAC59.log`.

- `phase-p-r6-readiness-and-budget-committed`: exit0; `gate-phase-p-r6-readiness-and-budget-committed-01M67J1GYS040N7WNH67P2AXFM.log`.


29 data/mock tests pass (23 Node +6 Python), syntax, private-path scan, external
anchor and EVERY expected receipt binding, all input hashes/modes, kernel limits,
HostConfig, channel separation and new import descriptor/readiness checks pass.
No earlier-round gate pass is reused. Development dirty data/preflight/prepare
passes are not the selected clean-HEAD receipts. There were two import-only passes
(development and final committed verification), not a retried native experiment.
No native run occurred. Final import pass measured all12 readiness cases and
trace volume in the SAME pass.

Synthetic tests cover non-configurable/non-writable method, getter once, original
receiver/arguments, sync error identity, async rejection, exact return/promise
identity, constructor semantics and no-negative-inference from missing hooks.
No full compatibility/release qualification is inferred from these gates.

### Exact required gate invocations

```sh
orgasmic dispatch gate phase-p-r6-import-preflight-committed -- test/store-probe/launch.sh import-preflight 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r6-import /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r6-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0 import-preflight
orgasmic dispatch gate phase-p-r6-noop-committed -- test/store-probe/launch.sh proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r6-noop /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r6-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0 native
orgasmic dispatch gate phase-p-r6-kill-committed -- test/store-probe/launch.sh capture-kill 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r6-kill /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r6-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0 native
orgasmic dispatch gate phase-p-r6-deadline-negative-committed -- python3 test/store-probe/deadline-negative.py proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r6-deadline /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r6-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/deadline-negative bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0 native
orgasmic dispatch gate phase-p-r6-check-prep-committed -- python3 test/store-probe/check-prep.py /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0
orgasmic dispatch gate phase-p-r6-readiness-and-budget-committed -- python3 /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/import-readiness-check.py
```

### Real descriptors and intended call sites

All 64 descriptors were checked against actual old/new Node(import/dist) and
Bun(source) exports; flag signatures are identical across all four combinations.
Every listed target has valueType:function, getter:false/setter:false,
prototypeDepth:0, facadeWrapped:true. Full records: api-descriptors.json; individual
ready events preserve them in the raw chain. Rows below are the 16 unique targets,
NOT a blanket interception claim. Membrane.constructor describes the prototype
constructor descriptor without invoking it. Factory-internal constructors are NOT
harness call sites and remain unobserved at the API layer.

| Owner | Method/constructor | Own vs prototype | configurable | writable | enumerable | Facade call site |
|---|---|---|---|---|---|---|

| JsStore | create | own | false | false | false | yes |

| JsStore | open | own | false | false | false | yes |

| JsStore | appendJson | prototype | true | true | true | yes |

| JsStore | createBranch | prototype | true | true | true | yes |

| JsStore | sync | prototype | true | true | true | yes |

| JsStore | query | prototype | true | true | true | yes |

| JsStore | close | prototype | true | true | true | yes |

| ContextManager | open | own | true | true | false | yes |

| ContextManager | getStore | prototype | true | true | false | yes |

| ContextManager | addMessage | prototype | true | true | false | yes |

| ContextManager | getAllMessages | prototype | true | true | false | yes |

| ContextManager | close | prototype | true | true | false | yes |

| AgentFramework | create | own | true | true | false | yes |

| AgentFramework | getStore | prototype | true | true | false | yes |

| AgentFramework | stop | prototype | true | true | false | yes |

| Membrane | constructor | prototype | true | true | false | yes |


### Node — native-format COMPONENT import probe (compiled/import/dist)

Node22.23.3. Not actual-old-Bun-reader proof. Chronicle uses index.js native wrapper;
CM/AF use their dist/import exports. Readiness only, no store operation.

| Tree / route | Ready | Trace lines | Raw trace bytes | Framed transport bytes | Dropped bytes |
|---|---|---:|---:|---:|---:|

| old / chronicle | yes | 1147 | 188857 | 767795 | 0 |

| old / cm | yes | 2995 | 672041 | 2188233 | 0 |

| old / af | yes | 17659 | 4474275 | 13534957 | 0 |

| new / chronicle | yes | 1117 | 186558 | 753695 | 0 |

| new / cm | yes | 3526 | 819896 | 2615946 | 0 |

| new / af | yes | 20452 | 5201495 | 15706683 | 0 |


### Bun — actual source export condition, old1.4.2/new1.4.4

Actual pinned runtime/entry/native hash checks and source imports succeeded, but
this is ONLY import readiness, not an old-reader→new-store result.

| Tree / route | Ready | Trace lines | Raw trace bytes | Framed transport bytes | Dropped bytes |
|---|---|---:|---:|---:|---:|

| old / chronicle | yes | 484 | 94917 | 350473 | 0 |

| old / cm | yes | 3832 | 701393 | 2581078 | 0 |

| old / af | yes | 9760 | 2069411 | 6931609 | 0 |

| new / chronicle | yes | 508 | 96788 | 363596 | 0 |

| new / cm | yes | 3513 | 729259 | 2482230 | 0 |

| new / af | yes | 12503 | 2769213 | 9034263 | 0 |


### Capture and initializer I/O — imports are NOT I/O-free

Final import checker **COMPLETE**, 12/12 declared PREP cases,
77753 verified records, 57,371,552 stdout bytes,
6.940948257s, exit0, no timeout/filter/drop. Normal terminal and
independent host receipt/inspect match. End inspect confirms stopped/exit0.
Chain head `d08172f5a141170e5e240437c11d9cac87fac9ea1429b0e11b3bce23c3720c9e`.
This means complete import-preflight capture, NOT native compatibility. Chain
proves integrity/truncation, not experimental authenticity or authority.

ALL selected raw syscalls (PID, absolute+relative timestamp, path, flags/mode,
result) remain in import-preflight.stdout.jsonl, not only hashes/summaries.
initializer-io.jsonl preserves candidate I/O records in the import window (last
native-hash event to ready, ±1ms wall-clock tolerance). This includes runtime/
loader/harness work, not unique causal attribution to a single SDK initializer.

- Node: /proc CPU/memory/cgroup reads and /sys frequency probes; AF imports also
  probe/read /etc/resolv.conf, nsswitch.conf, netsvc.conf, svc.conf and module search
  directories (.node_modules/.node_libraries, /node_modules, /runtime/lib/node).
  Paths include failed ENOENT probes as well as successful operations; raw results
  distinguish them. /harness/observer.mjs is expected observer module loading.
- **Bun imports CREATE/WRITE/RENAME cache files** under each declared /work/.../
  scratch/bun-transpiler-cache. Captured openat O_WRONLY|O_CREAT|O_EXCL mode0644,
  pwritev results and renameat from temporary .pile files to .pile targets are
  genuine initializer/runtime file mutation. They are not app stores and do not
  mutate RO inputs. No claim that imports imply no writes.
- Socket REFERENCES occur: inherited UNIX-STREAM API/stdout/stderr descriptors and
  runtime eventfd/pipe traffic are captured. The unchanged selected syscall classes
  are `%file,%desc,%memory,%process`; they are NOT an exhaustive `%network` policy
  audit. Missing socket-creation/network syscall records are NOT proof that no
  socket/network activity occurred. Networknone is enforced; no provider/endpoint
  is deliberately invoked. See actual recorded socket-reference lines, not a
  claimed global no-I/O conclusion.
- Gate asserts no /work/native/master/clone store paths in any raw preflight trace,
  zero api-enter/open/row events, and explicit preflight-complete/finished from
  every child. Inputs were fully rehashed before each child and after the pass.

Candidate counts below are NOT claims of successful filesystem writes (the
mutation bucket also conservatively includes O_RDWR/F_GETFL and eventfd/API writes).
Raw syscall results remain authoritative; full paths/candidate lines are in the
artifacts, not discarded behind a digest.

| Case | Outside-input path syscalls | Write/mutation candidates | Socket-reference syscalls |
|---|---:|---:|---:|

| node/old/chronicle | 34 | 4 | 5 |

| node/old/cm | 34 | 6 | 8 |

| node/old/af | 47 | 6 | 14 |

| node/new/chronicle | 34 | 5 | 5 |

| node/new/cm | 35 | 6 | 11 |

| node/new/af | 47 | 6 | 14 |

| bun/old/chronicle | 9 | 9 | 5 |

| bun/old/cm | 346 | 404 | 5 |

| bun/old/af | 1163 | 1105 | 18 |

| bun/new/chronicle | 11 | 9 | 5 |

| bun/new/cm | 421 | 482 | 5 |

| bun/new/af | 1754 | 1709 | 19 |


### Recomputed measured budget — STOP for any future native matrix

No 4× guessing factor is reused. Exact JSON/base64/chain frame lengths from the
12 import cases, including trace/API/stdout/stderr transport, are reweighted:
**old4 +new3 per runtime×route =42 native children**. Current preflight label/
operation/cwd text differs slightly from native labels/store paths, so this is an
import-volume projection, not a guaranteed upper bound on a future capture.


- Weighted measured imports: **268,365 trace lines,
  198,285,819 framed transport bytes =
  189.10MiB**.
- Existing16MiB non-trace inventory/result/statistics allowance gives
  **215,063,035 bytes =
  205.10MiB** BEFORE any store work.
- Filter threshold192MiB; hard cap256MiB. Bare imports leave only2.90MiB below the
  filter, and imports+allowance EXCEED it by13.10MiB. Store work is UNMEASURED and
  additional; hard-cap headroom is not usable qualification headroom because any
  filter drop is terminal INCOMPLETE.
- **Native budget result: STOP / NOT VIABLE under the current conservative budget.**
  This is the important prep finding, not a passing full-matrix viability gate.
  No native launch/retry, limit increase, trace relaxation, new reserve scheme or
  architecture change was made. New native argv is provided only as NOT RUN and
  MUST NOT be treated as a go. Any future remedy/decision is outside this slice.

budget-recomputed.json and import-readiness-check.py preserve inputs, per-child
frame costs, weights and arithmetic. Readiness-and-budget gate exit0 means the
measurement/arithmetic/STOP finding was recorded correctly, NOT budget approval.

### No-op / kill / deadline-negative proofs

- No-op+exact production-FD smoke: COMPLETE, exit0, all trace/stdout/API channel
  separation assertions true. Kernel limits/NNP/seccomp/zero capabilities pass.
- Kill: exit137, INCOMPLETE with verified prefix and no final; no synthesized close.
- Deadline-negative: actual600s/10s vs manifest1800s/30s; refuses INCOMPLETE despite
  normal mock terminal/full expected mock case, as required.
- All four host receipts including import-preflight are explicitly bound to the
  SAME externally supplied anchor. Negative uses byte-identical manifest copy.

Unchanged envelope: rootless orgasmic; nonroot1000:1000; RO root/code/all11 binds;
4GiB/swap0/2CPU/256PIDs/core0; cap-dropALL/NNP/default seccomp; private PID/cgroup/IPC;
networknone/noports; /work1600+/tmp128+/dev64+/dev-shm16=1808MiB≤1920MiB ceiling,
with /dev and /dev/shm RO. No home/credentials/socket/RW host mounts. Whole1800s+30s,
child90s+5s, capture256/filter192MiB unchanged. No systemd, host config/linger/session
changes, install/hooks/download/network, or copied runtime into the image.

### New native argv JSON — NOT RUN, budget STOP

### `execute-argv-NOT-RUN.json`
```json
[
  "env",
  "-i",
  "HOME=/home/orgasmic",
  "PATH=/usr/bin:/bin",
  "XDG_RUNTIME_DIR=/run/user/1002",
  "DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus",
  "LC_ALL=C",
  "timeout",
  "--kill-after=30s",
  "1800s",
  "podman",
  "run",
  "--name",
  "f-host-2b-task-mhqnz-r6-native-not-run",
  "--pull=never",
  "--network=none",
  "--memory=4g",
  "--memory-swap=4g",
  "--cpus=2",
  "--pids-limit=256",
  "--ulimit",
  "core=0",
  "--read-only",
  "--read-only-tmpfs=false",
  "--tmpfs",
  "/work:rw,nosuid,nodev,size=1600m,mode=1777",
  "--tmpfs",
  "/tmp:rw,nosuid,nodev,noexec,size=128m,mode=1777",
  "--shm-size=16m",
  "--user",
  "1000:1000",
  "--cap-drop=ALL",
  "--security-opt",
  "no-new-privileges",
  "--pid=private",
  "--cgroupns=private",
  "--ipc=private",
  "--workdir=/work",
  "--env",
  "HOME=/work",
  "--env",
  "PROBE_MANIFEST_SHA256=bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0",
  "--env",
  "LC_ALL=C",
  "--env",
  "PATH=/runtime/node:/usr/bin:/bin",
  "--env",
  "LD_LIBRARY_PATH=/tools/lib",
  "--env",
  "BUN_INSTALL=/work/bun",
  "--env",
  "BUN_INSTALL_CACHE_DIR=/work/bun-install-cache",
  "--env",
  "BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache",
  "--env",
  "XDG_CACHE_HOME=/work/cache",
  "--volume",
  "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro",
  "--volume",
  "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r6-final:/inputs/new:ro",
  "--volume",
  "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro",
  "--volume",
  "/opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro",
  "--volume",
  "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro",
  "--volume",
  "/srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro",
  "--volume",
  "/usr/bin/strace:/tools/strace:ro",
  "--volume",
  "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro",
  "--volume",
  "/home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/old-tree.json:/inputs/old-tree.json:ro",
  "--volume",
  "/home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/new-tree.json:/inputs/new-tree.json:ro",
  "--volume",
  "/home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/input-manifest.json:/inputs/input-manifest.json:ro",
  "5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6",
  "/runtime/node/node",
  "/harness/probe.mjs",
  "--phase-e-approved",
  "--section",
  "native"
]
```

### Actually executed import-preflight argv
```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r6-import --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw,nosuid,nodev,size=1600m,mode=1777 --tmpfs /tmp:rw,nosuid,nodev,noexec,size=128m,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r6-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/probe.mjs --phase-e-approved --section import-preflight
```

### `import-preflight-host-receipt.json`
```json
{
  "kind": "import-preflight",
  "exitStatus": 0,
  "started": "1793402544107325064",
  "finished": "1793402551048273321",
  "elapsedSeconds": 6.940948257,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/import-preflight-argv.json",
  "argvSha256": "7f1d556c4b1def3db4aadebe3cc229059ebfafcdbd08897f80844f859cc04656",
  "manifestSha256": "bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/import-preflight.stdout.jsonl",
    "size": 57371552,
    "sha256": "ec9b4881bcbea73abf2f39b2e8638e89282add8988d36091cf378a2468329355"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67HJQMANFVMGDKR7G59MZGH/evidence/import-preflight.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### `import-preflight-check.json`
```json
{
  "classification": "COMPLETE",
  "verifiedRecords": 77753,
  "verifiedBytes": 57371552,
  "verifiedChain": "d08172f5a141170e5e240437c11d9cac87fac9ea1429b0e11b3bce23c3720c9e",
  "caseIds": [
    "node/old/chronicle",
    "node/old/cm",
    "node/old/af",
    "node/new/chronicle",
    "node/new/cm",
    "node/new/af",
    "bun/old/chronicle",
    "bun/old/cm",
    "bun/old/af",
    "bun/new/chronicle",
    "bun/new/cm",
    "bun/new/af"
  ],
  "inspectedContainer": "4b4ec202cbd8c701386e3d025bffde1f4c19774949ea2efd4b15c890abe3a3ba",
  "final": {
    "expectedCaseCount": 12,
    "completedCaseCount": 12,
    "status": "complete",
    "totalCount": 77753,
    "dataChainHash": "16f55fb27206452bb4280aefcb5ed4bb9101e4c3a59b1a1232c7750ab26228b3",
    "bytesBeforeFinal": 57371067,
    "cap": 268435456
  },
  "error": null,
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

### `limits-proof-check.json`
```json
{
  "classification": "COMPLETE",
  "verifiedRecords": 8,
  "verifiedBytes": 12143,
  "verifiedChain": "69cac8c92ab87a5498f149c0189c0fa913dcfea6618f98e348c221a3a7267140",
  "caseIds": [
    "mock/noop"
  ],
  "inspectedContainer": "789541e9c01e0e7eb12116f0724e2507e2a1a7e9bbf7cfc6dd3f56561123970d",
  "final": {
    "expectedCaseCount": 1,
    "completedCaseCount": 1,
    "status": "complete",
    "totalCount": 8,
    "dataChainHash": "e732957d85427dda5a2c274be8b7850f93cb6dd40958688fbc982d7e1b15eeba",
    "bytesBeforeFinal": 11671,
    "cap": 268435456
  },
  "error": null,
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

### `capture-kill-check.json`
```json
{
  "classification": "INCOMPLETE",
  "verifiedRecords": 6,
  "verifiedBytes": 11328,
  "verifiedChain": "8e3de5aef1b83dbdbf2ee11e591204af7bc98653cfc2720e10824b618db4a015",
  "caseIds": [],
  "inspectedContainer": "541889e58d81bc4a5db7b6feb9d257b15985f92cead22bf8c5e9dfb5d2fac368",
  "final": null,
  "error": "Error: INCOMPLETE: no normal terminal record",
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

### `deadline-negative/deadline-negative-result.json`
```json
{
  "launcherExit": 2,
  "actualDeadline": 600,
  "actualKillAfter": 10,
  "declaredDeadline": 1800,
  "declaredKillAfter": 30,
  "checker": {
    "classification": "INCOMPLETE",
    "verifiedRecords": 8,
    "verifiedBytes": 12197,
    "verifiedChain": "eb928728ec6d2d50124f04d6bfbc91ecf56ba7202a8f2f4c9a6d86120956ed5c",
    "caseIds": [
      "mock/noop"
    ],
    "inspectedContainer": "113d485a29fc476132d9b29df57de65444351b73558ab92ee4da69a6a6e7f91e",
    "final": {
      "expectedCaseCount": 1,
      "completedCaseCount": 1,
      "status": "complete",
      "totalCount": 8,
      "dataChainHash": "89b9295f881b728ba1574cd550fe0052692dc5f83ad14132f80bd3e0f67ea506",
      "bytesBeforeFinal": 11725,
      "cap": 268435456
    },
    "error": "Error: INCOMPLETE: deadline differs from approved manifest execution intent",
    "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
  },
  "outcome": "PASS: drifted actual argv refused despite a normal, intact mock capture"
}
```

### `budget-recomputed.json`
```json
{
  "manifestSha256": "bdf4e43833d5409335204b46458d16767b0fcaa9070844535dd0183793321fc0",
  "source": "MEASURED real SDK import-preflight, full unchanged syscall filter and exact JSON/base64 framing; NOT the prior 4x guess",
  "importAll42Lines": 268365,
  "measuredImportTransportAll42Bytes": 198285819,
  "measuredImportTransportAll42MiB": 189.10009288787842,
  "nonTraceAllowanceBytes": 16777216,
  "modelImportPlusNonTraceBytes": 215063035,
  "modelImportPlusNonTraceMiB": 205.10009288787842,
  "filterThresholdBytes": 201326592,
  "stdoutCapBytes": 268435456,
  "storeWorkMeasured": false,
  "nativeBudget": "STOP / NOT VIABLE under current conservative budget",
  "note": "Weighted old4/new3 per runtime/route =42. Preflight labels/operation lengths and future store-path annotations can differ; imports alone approach threshold, +16MiB non-trace allowance exceeds it. Store work is additional and unmeasured. Do NOT run native or raise limits."
}
```

### Image/inherited evidence provenance

Image-inspect-current.json confirms unchanged image identity, Containerfile unchanged.
No rebuild needed. Only historical image-build provenance is inherited; not prior
gate passes. The first build inherited host environment before the binding env-i
condition (pullnever/networknone); its d614334ab968... image is NOT selected.
An intermediate cap-drop mkdir build failed. Selected final no-RUN metadata-only
build used env-i with ONLY HOME/PATH/XDG_RUNTIME_DIR/DBUS_SESSION_BUS_ADDRESS/LC_ALL,
pullnever/networknone/no-cache/http-proxyfalse/capdropALL/no-new-privileges. Both
historical raw build logs are copied here. No exposure/rotation claim is made.
Previous native failure is read-only historical report context; it was not retried.

## Unmet Criteria

Requested bounded r6 PREP implementation, real import readiness, assigned tests,
identities/NOT-RUN argv and budget recomputation are complete. **Future full native
budget viability is NOT met**: projection205.10MiB exceeds192MiB before store work.
STOP applies to native execution, not permission to expand this task or raise caps.
No second store experiment or native section was run. No compatibility/release/
refusal/exposure/mutation result is claimed. Section(b) stays pending A+B+D tarball,
D pin:null; AF0.30.0 remains only a native-format driver, NOT D.

## Residual Risk

- API observations are ONLY harness calls; internal SDK/native calls unknown at API
  layer. Missing hooks/events never imply no read/mutation. Syscall correlation and
  valid controls remain required for any future diagnostic interpretation.
- Imports caused real cache/initializer I/O. Selected file/desc/memory/process trace
  is not exhaustive network-call observation; do not infer no sockets from absence.
- Store constructors/opens/reads/writes, factory-internal construction and future
  actual observer/native call interoperability remain UNTESTED in r6 by design.
  Descriptor readiness and synthetic identity tests are not store compatibility.
- Full42 native capture is not presently viable under unchanged terminal-filter
  policy. No architecture/capture/pin decision is taken here; future work needs a
  separately scoped decision, not a native retry with these argv.
- Host remains trusted; RO is not hostile-host immutability. No concurrent writer
  may alter inputs between untraced parent verification and child use. Manifest,
  recorded argv, inspect and unkeyed chains do not attest a hostile host or consent.
- Other review LOWs (computed-vs-expected runtime digest emission, non-bind mount
  corroboration limits, run-start harness/tracer check, parent ordering test gap)
  were NOT expanded into unrelated fixes. Their prior limitations remain disclosed.
