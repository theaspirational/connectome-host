# TASK-MHQNZ — Phase P revision 5

## Changed

Local commit **1cfe32a1c381a2d67731a6c2b8e3989874947df9**, directly on
**bac2ebb2e9f634a877bb7422ff5542160cbc08dc**. Seven harness files changed, explicit
staging, Vasyl noreply identity and GPT-6.1-Sol trailer. No push/merge/landing,
product/dependency/lockfile/Containerfile change, new service or architecture.

Read `/srv/review-drop/TASK-MHQNZ-R4-DELTA-REVIEW.md` IN FULL (HIGH-1 and LOWs);
SHA256 `ea8081d3f3d8be4599a7d7f757e36af64f00056d7d9b7479d75deb0d075dfa0a`;
copy `delta-review.md`. Applied Soma's later preference for verification before
EACH child, not just once per matrix. No other LOW fixes were added.

**PREP ONLY. No Phase E; no store created/opened; no AF/CM/Chronicle/SDK imported.**
The optional measurement resolves declared entries and hashes their bytes but exits
before ALL SDK imports. It uses a deliberately nonexistent store placeholder.
Code preparation only hashes/unpacks code; no package installs/lifecycle hooks.

### Delta per item

- **HIGH-1:** full tree walks/lstat/hashes run in the UNTRACED Node parent.
  `probe.mjs:138-141` verifies the external manifest, critical inputs and both
  full trees; line157 emits ONE initial `input-trees-verified` frame containing
  counts/bytes/aggregate hashes. `probe.mjs:70-72` additionally verifies the entire
  selected tree immediately before EVERY child spawn and emits a compact
  `input-tree-verified` summary. No per-file bulk-verification trace frames.
  `verify-inputs.mjs` reuses the manifest's pinned full inventory. The aggregate
  SHA256 is of the verified pinned file records (paths/hash/size/mode), not a claim
  of host immutability. Mismatch refuses before child spawn.
- The tracee no longer invokes verifyTree, verifyCriticalInputs, readdir or lstat
  walks. It keeps the anchored manifest read, executable path/hash/version,
  conditional-entry resolution/hash, version/transitive-resolution checks and
  native-payload hash (`worker.mjs:51-86`). Actual entry/native hashes are now
  compared to pinned manifest leaf records, not just logged. The duplicate runtime
  binary hash was removed; the verified hash is logged once.
- **Trace boundary:** `probe.mjs:65-67,72` still places the ENTIRE worker under
  strace. Its SDK imports start at `worker.mjs:93` (route imports100/106), AFTER the
  retained runtime/leaf assertions. Moving parent verification does NOT hide SDK
  import-time or any store-access effects. Full production syscall/API capture
  is unchanged. The no-store `prep-assertions` branch exits at lines87-90, strictly
  before those imports; it is not one of the 18 experiment cases.
- **LOW-1:** inspect CapDrop must equal the pinned full expected 11-capability
  list and length (order irrelevant); a partial set refuses. CapAdd remains empty.
- **LOW-2:** collect ALL split/equals-form `--security-opt` values and require
  inspect SecurityOpt to be a superset. A missing second option refuses; a
  superset passes. Kernel Seccomp2 remains the primary default-seccomp proof.
- **LOW-3:** filter Mounts to `Type === 'bind'` before bind count/matching.
  A tmpfs entry no longer counts as an extra bind. Tests cover all three LOWs.

### Explicit trust boundary

The host is trusted. Every input bind remains **:ro**, and there must be NO
concurrent host writer to `/inputs/old` or `/inputs/new` between each full parent
verification and the corresponding child's use. **RO inside the container is NOT
hostile-host immutability.** Per-child binary/entry/native hashes detect substitution
of those payloads actually checked/loaded; they do not individually rehash all
transitive modules or authenticate a hostile host. Recorded argv is trusted-launcher
self-attestation; inspect corroborates container shape, not the host timeout.
`--phase-e-approved` is only a purpose marker, NOT authority/owner consent. Owner
authority and operational go remain external; no attestation/auth/approval service.
**Results remain UNQUALIFIED until control/API/syscall correlation.**

### Exact identities

- Harness aggregate SHA256 (`harness.SHA256SUMS`, 19 files):
  `7f5abf40a4e61c1a72ccfbe2287ab6f8af467402b7f16f80a9a546e3214e22c3`.
- Input manifest / externally passed approval anchor: `3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2`.
- Image ID (unchanged): `5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6`.
- OCI manifest digest (unchanged): `sha256:44b688151db6337cfb11114ea43a644311251e7d87c858dc8f252bc906b4a3dd`.
- Local Ubuntu24.04 base ID: `6277ab668b2ad3c3ab851d370faa1690d324b30db29d342527f61fed28f20f36`.

Only relative/container argument paths and generic instructions are committed.
Operator manifest/inventories/real host argv remain in this evidence directory.

## Verification Gates

All four REQUIRED gates passed at CLEAN committed HEAD **1cfe32a1c381a2d67731a6c2b8e3989874947df9**.
`check-prep` was given the external anchor above, identical to all launchers;
25 data/mock tests pass (19 Node +6 Python), syntax/input hashes/modes/private-path
scan pass. Every normal/kill/negative receipt's manifestSha256 equals that anchor;
negative has an explicitly bound byte-identical approved manifest copy. No earlier
round gate pass is reused.

- `phase-p-r5-noop-committed`: exit0; `gate-phase-p-r5-noop-committed-01M67G6PGEKDN20BWM4R2PJW7Y.log`.

- `phase-p-r5-kill-committed`: exit0; `gate-phase-p-r5-kill-committed-01M67G70Z1SHDJ45NMS87TFN10.log`.

- `phase-p-r5-deadline-negative-committed`: exit0; `gate-phase-p-r5-deadline-negative-committed-01M67G6ZFY5KP6E93S4WBV675A.log`.

- `phase-p-r5-check-prep-committed`: exit0; `gate-phase-p-r5-check-prep-committed-01M67G723PWMW9KCE5KY1TYTZB.log`.

- `phase-p-r5-leaf-measure-committed`: exit0; `gate-phase-p-r5-leaf-measure-committed-01M67G7762B5HVH1RDAQMCK5JM.log`.

- `phase-p-r5-budget-estimate-committed`: exit0; `gate-phase-p-r5-budget-estimate-committed-01M67G84B0T0A4YNFVDSZE6XKM.log`.


Optional leaf measurement and computed budget gates are also CLEAN-HEAD passes,
not compatibility probes. Four no-store runtime/tree controls exit0. Their exact
argv/scripts and raw syscall/API frames are in `measure-leaf-control-argv.json`,
`measure-leaf-control.mjs`, `measure-leaf-control.py`,
`measure-leaf-control.stdout.jsonl` and `measure-leaf-control.results.json`.
`budget-check.py` verifies ALL four auxiliary prefix chains and computes the model.
Auxiliary prefixes intentionally have no experiment terminal/case declaration and
are NOT labeled COMPLETE compatibility captures.

Development-only failure: the first optional evidence-side measurement script had
a shell/JS newline-escape SyntaxError before worker execution (gate
`phase-p-r5-leaf-measure`, exit1). Corrected the evidence script, no permissions or
product/harness behavior relaxed; the later committed measurement passed. Initial
code prep was dirty; `phase-p-r5-inputs-final` froze the final harness bytes and new
RO code tree. A local Python __pycache__ created during preparation was removed
before the final manifest; no generated cache remains in harness or git. Ignored
build directories new-r5/new-r5-final are unpacked code, not stores/build evidence.

### Exact required gate commands

```sh
orgasmic dispatch gate phase-p-r5-noop-committed -- test/store-probe/launch.sh proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r5-noop /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence 3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 native
orgasmic dispatch gate phase-p-r5-deadline-negative-committed -- python3 test/store-probe/deadline-negative.py proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r5-deadline /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/deadline-negative 3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 native
orgasmic dispatch gate phase-p-r5-kill-committed -- test/store-probe/launch.sh capture-kill 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r5-kill /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence 3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 native
orgasmic dispatch gate phase-p-r5-check-prep-committed -- python3 test/store-probe/check-prep.py /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence 3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2
```

### Computed post-fix budget

The old tree is 10,628 files /204,435,070 bytes; new is7,149 files /88,620,916 bytes.
Bulk verification contributes **ZERO traced syscalls per child now**: both the
initial check and each selected-tree recheck run in the untraced parent. Compact
summary frames replace ~68k/44k raw lines per child; the HIGH-1 ~1.5–1.8GB mandatory
trace demand is removed, not filtered away. Hashing roughly6.5GB across all42
children remains parent CPU/I/O work, still subject to the unchanged whole deadline.

Measured the actual verifyTree-free worker pre-import path in the SAME envelope,
full production `%file,%desc,%memory,%process` trace flags and shared FD shape.
Bun1.4.2/1.4.4 and Node22.23.3 all passed runtime/version/entry/transitive/native hash
assertions. No SDK import/store work. The frame byte counts below use production
EvidenceStream JSON/base64/SHA256 framing for EVERY raw trace line and API event,
not raw-byte counts alone.

| Runtime/tree | Measured lines | Raw bytes | Framed bytes | Count over42 | Modeled lines/child | Modeled bytes/child |
|---|---:|---:|---:|---:|---:|---:|

| node/old | 882 | 139276 | 582500 | 12 | 3528 | 2330000 |

| node/new | 882 | 139095 | 582239 | 9 | 3528 | 2328956 |

| bun/old | 428 | 78731 | 300910 | 12 | 1712 | 1203640 |

| bun/new | 451 | 80196 | 313002 | 9 | 1804 | 1252008 |


Weighted retained-verification baseline over all42 = **27,717
lines /18,658,089 framed bytes (17.79MiB)**.
Explicit sensitivity estimate: **4× that measured baseline +16MiB** for non-trace
inventories/results/API/statistics/summary frames. The extra 3× is an allowance for
SDK loading and tiny synthetic canary operations, NOT measured SDK/store behavior
and NOT a rigorous upper bound. The model yields **110,868 trace
lines /91,409,572 bytes = 87.17MiB** overall,
**104.83MiB below192MiB**, ~45.4% of the filter threshold.
The computed gate requires the model <128MiB <192MiB and passes; no limits raised.
This adds no runtime reserve architecture or new capture budget.

Code/frame metadata and the measured exact lines, byte costs, counts, assumptions,
external manifest anchor and reproducible arithmetic are in `budget-estimate.json`
and `budget-check.py`. The estimate is deliberately separate from experimental
qualification. Actual SDK/store volume is UNKNOWN until separately authorized E;
no claim that it is upper-bounded by the model. The estimate shows the retained
verification path is not itself guaranteed to exhaust capture anymore. If actual
volume approaches192MiB it remains unqualifiable: filter activation with ANY drop
is terminal INCOMPLETE. **The budget must not be approached**, and neither this
model nor a prefix chain is a compatibility result.

### Targeted no-op/kill/deadline proofs

| Control | Actual exit | Checker | Evidence |
|---|---:|---|---|
| No-op + production-FD smoke | 0 | COMPLETE |8records, exact one mock case +normal terminal; four disjoint smoke checks true |
| Forced kill after flushed mock inventory |137| INCOMPLETE |6records preserved, no final; no host-synthesized closing record |
| Changed timeout600s/10s |0 (launcher2)| INCOMPLETE |8intact records/full mock case/normal terminal; refuses deadline drift against1800/30 |

Each host receipt records actual argv hash and stdout/stderr hashes and actual
exit/deadline. All three inspector cross-checks succeeded before completeness/
deadline checks. The normal smoke proves `/bin/true` execve in trace, exact stdout
marker only on fd5, exact API marker only on fd4, no marker in exec-only trace,
/dev/fd exists and exit0. No capability/seccomp/ptrace relaxation.

SHA256 chains prove integrity/truncation, **NOT that the intended experiment
happened**, owner consent, compatibility, exposure, rotation or mutation safety.
Normal COMPLETE here describes only a no-op/mock capture. Completeness in E still
requires normal terminal +exact18 case IDs/count +successful host deadline/exit
receipt, with control/API/syscall correlation needed for any qualification.

### Unchanged launch envelope / image provenance

1800s whole command +30s grace, runtime.mjs single source; up to42 children at90s+5s
individual budgets. Aggregate timeout is INCOMPLETE. Doubled-matrix budget need not
sum every child worst case. 18cases and three routes ×two runtimes ×three scenarios
unchanged; fresh writable synthetic clones remain planned ONLY for E.

Rootless orgasmic uid1002, container1000:1000; memory4GiB, extra swap0, 2CPU,
pids256/core0, read-only root/code, ALL caps dropped, NNP/default seccomp,
private PID/cgroup/IPC, networknone/no ports. All11 bindsRO, no hostRW volume,
home/credential/socket mounts. HOME in host environment is not a home mount.
/work1600 +/tmp128 +runtime /dev64 +/dev/shm16=1808MiB≤1920MiB (latter two RO).
Bun writable/cache paths stay under/work. No networking/install or missing public
dependency. No host session/linger/podman changes or new systemd delegation scope.
Stdout/stderr redirected directly to evidence, not post-exit podman cp.

Image/Containerfile unchanged; `image-inspect-current.json` checks current identity.
Only inherited evidence is unchanged image-build provenance (not earlier gates):
`inherited-image-build-first-env.log`, `inherited-image-build-empty-env-final.log`.
The FIRST historical build inherited host environment before the binding env-i
condition, despite networknone/pullnever, and produced NOT-selected image
`d614334ab9680804a227546ff29b7cb36ac1aa06eeda6332ff1c2fccebd9c945`.
An intermediate cap-drop mkdir build failed. Selected final metadata-only image
has no RUN/install/hooks/copies; clean build command:

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C /usr/bin/podman build --pull=never --network=none --no-cache --http-proxy=false --cap-drop=ALL --security-opt=no-new-privileges -f test/store-probe/Containerfile -t localhost/f-host-2b-phase-p:task-mhqnz test/store-probe
```

Only HOME/PATH/XDG_RUNTIME_DIR/DBUS_SESSION_BUS_ADDRESS/LC_ALL retained for rootless
local Podman. No rebuild needed this round. No exposure/rotation claim.

### Full recorded argv (operator-only; JSON arrays are authoritative)

### `limits-proof-argv.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r5-noop --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs
```

### `capture-kill-argv.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r5-kill --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs --mock-kill
```

### `deadline-negative/limits-proof-argv.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=10s 600s podman run --name f-host-2b-task-mhqnz-r5-deadline --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/deadline-negative/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/deadline-negative/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/deadline-negative/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs
```

### `execute-argv-NOT-RUN.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r5-noop --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/probe.mjs --phase-e-approved --section native
```

The last argv is generated/prospective **NOT RUN**, not consent or authorization. Optional leaf measurement uses the same envelope/binds, with only container name and no-SDK command tail changed; its full argv is `measure-leaf-control-argv.json`/`.txt`.

### Limits proof and receipts
Full HostConfig is retained independently in `container-inspect.json`, `capture-kill-container-inspect.json` and `deadline-negative/container-inspect.json`.

### `limits-proof.stdout`

```text
memory.max=4294967296
memory.swap.max=0
cpu.max=200000 100000
pids.max=256
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu)
CapInh:	0000000000000000
CapPrm:	0000000000000000
CapEff:	0000000000000000
CapBnd:	0000000000000000
CapAmb:	0000000000000000
NoNewPrivs:	1
Seccomp:	2
Seccomp_filters:	1
Limit                     Soft Limit           Hard Limit           Units     
Max cpu time              unlimited            unlimited            seconds   
Max file size             unlimited            unlimited            bytes     
Max data size             unlimited            unlimited            bytes     
Max stack size            8388608              unlimited            bytes     
Max core file size        0                    0                    bytes     
Max resident set          unlimited            unlimited            bytes     
Max processes             353396               353396               processes 
Max open files            1048576              1048576              files     
Max locked memory         11596443648          11596443648          bytes     
Max address space         unlimited            unlimited            bytes     
Max file locks            unlimited            unlimited            locks     
Max pending signals       353396               353396               signals   
Max msgqueue size         819200               819200               bytes     
Max nice priority         0                    0                    
Max realtime priority     0                    0                    
Max realtime timeout      unlimited            unlimited            us        
998 343 0:57 / / ro,relatime - overlay overlay rw,lowerdir=/home/orgasmic/.local/share/containers/storage/overlay/l/NP42E625VWWJYXWBFHJBCAONY6,upperdir=/home/orgasmic/.local/share/containers/storage/overlay/ab37a4e2f0d78d577c8c3b0518be33eab6c4faa7b964f1f855568922efba0c8e/diff,workdir=/home/orgasmic/.local/share/containers/storage/overlay/ab37a4e2f0d78d577c8c3b0518be33eab6c4faa7b964f1f855568922efba0c8e/work,redirect_dir=nofollow,uuid=on,userxattr
1001 998 0:65 / /work rw,nosuid,nodev,relatime - tmpfs tmpfs rw,size=1638400k,uid=1002,gid=1002,inode64
1000 998 0:66 / /sys ro,nosuid,nodev,noexec,relatime - sysfs sysfs ro
1002 998 0:67 / /dev ro,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,uid=1002,gid=1002,inode64
1003 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /harness ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1004 998 0:68 / /proc rw,nosuid,nodev,noexec,relatime - proc proc rw
1006 998 0:69 / /tmp rw,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=131072k,uid=1002,gid=1002,inode64
1005 1002 0:70 / /dev/pts rw,nosuid,noexec,relatime - devpts devpts rw,gid=231076,mode=620,ptmxmode=666
1007 998 0:46 /containers/overlay-containers/9160e29813853f142cbbfc3e62e8216d9fd3cf5fad13142c124ec3d4d0bebe91/userdata/.containerenv /run/.containerenv ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1008 1002 0:49 / /dev/shm ro,nosuid,nodev,noexec,relatime - tmpfs shm rw,size=16384k,uid=1002,gid=1002,inode64
1009 998 252:1 /usr/bin/strace /tools/strace ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1010 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /tools/lib ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1011 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/old-tree.json /inputs/old-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1012 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r5-final /inputs/new ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1013 998 0:46 /containers/overlay-containers/9160e29813853f142cbbfc3e62e8216d9fd3cf5fad13142c124ec3d4d0bebe91/userdata/hosts /etc/hosts ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1014 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/new-tree.json /inputs/new-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1015 998 0:46 /containers/overlay-containers/9160e29813853f142cbbfc3e62e8216d9fd3cf5fad13142c124ec3d4d0bebe91/userdata/hostname /etc/hostname ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1016 1002 0:63 / /dev/mqueue rw,nosuid,nodev,noexec,relatime - mqueue mqueue rw
1017 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/input-manifest.json /inputs/input-manifest.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1018 998 252:1 /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /runtime/bun-new/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1019 1000 0:29 / /sys/fs/cgroup ro,nosuid,nodev,noexec,relatime - cgroup2 cgroup rw,nsdelegate,memory_recursiveprot
1020 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /runtime/bun-old/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1021 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /inputs/old/node_modules ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1022 998 252:1 /opt/node-v22.23.3-linux-x64/bin/node /runtime/node/node ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1023 1002 0:5 /null /dev/null rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1024 1002 0:5 /random /dev/random rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1025 1002 0:5 /full /dev/full rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1026 1002 0:5 /tty /dev/tty rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1027 1002 0:5 /zero /dev/zero rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1028 1002 0:5 /urandom /dev/urandom rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
344 1004 0:68 /bus /proc/bus ro,nosuid,nodev,noexec,relatime - proc proc rw
345 1004 0:68 /fs /proc/fs ro,nosuid,nodev,noexec,relatime - proc proc rw
349 1004 0:68 /irq /proc/irq ro,nosuid,nodev,noexec,relatime - proc proc rw
350 1004 0:68 /sys /proc/sys ro,nosuid,nodev,noexec,relatime - proc proc rw
351 1004 0:68 /sysrq-trigger /proc/sysrq-trigger ro,nosuid,nodev,noexec,relatime - proc proc rw
375 1004 0:71 / /proc/acpi ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
400 1004 0:5 /null /proc/kcore rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
445 1004 0:5 /null /proc/keys rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
447 1004 0:5 /null /proc/latency_stats rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
449 1004 0:5 /null /proc/timer_list rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
453 1004 0:75 / /proc/scsi ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
549 1000 0:76 / /sys/firmware ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
554 1000 0:77 / /sys/dev/block ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo:       0       0    0    0    0     0          0         0        0       0    0    0    0     0       0          0
```

### Streamed production-FD smoke
```json
{
  "devFdExists": true,
  "transportArgv": [
    "/bin/bash",
    "-o",
    "pipefail",
    "-c",
    "\"$@\" 3>&1 1>&5 | /bin/cat",
    "probe-smoke",
    "/tools/strace",
    "-f",
    "-e",
    "trace=execve",
    "-o",
    "/dev/fd/3",
    "/runtime/node/node",
    "/harness/transport-control.mjs"
  ],
  "stdio": [
    "ignore",
    "pipe",
    "pipe",
    "ignore",
    "pipe",
    "pipe"
  ],
  "argv": [
    "/tools/strace",
    "-f",
    "-e",
    "trace=execve",
    "-o",
    "/dev/fd/3",
    "/runtime/node/node",
    "/harness/transport-control.mjs"
  ],
  "exitStatus": 0,
  "signal": null,
  "error": null,
  "stdout": "PROBE_SMOKE_STDOUT_v1\n",
  "api": "{\"kind\":\"smoke-api\",\"marker\":\"PROBE_SMOKE_API_v1\"}\n",
  "stderr": "",
  "trace": "29    execve(\"/runtime/node/node\", [\"/runtime/node/node\", \"/harness/transport-control.mjs\"], 0x7ffdd218fab0 /* 14 vars */) = 0\n36    execve(\"/bin/true\", [\"/bin/true\"], 0xdebbde0 /* 14 vars */) = 0\n36    +++ exited with 0 +++\n29    --- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=36, si_uid=1000, si_status=0, si_utime=0, si_stime=0} ---\n34    +++ exited with 0 +++\n33    +++ exited with 0 +++\n32    +++ exited with 0 +++\n31    +++ exited with 0 +++\n30    +++ exited with 0 +++\n35    +++ exited with 0 +++\n29    +++ exited with 0 +++\n",
  "checks": {
    "traceHasTrueExec": true,
    "stdoutMarkerOnly": true,
    "apiMarkerOnly": true,
    "traceHasNoMarkers": true
  },
  "passed": true
}
```

### `limits-proof-host-receipt.json`

```json
{
  "kind": "proof",
  "exitStatus": 0,
  "started": "1793400724079810554",
  "finished": "1793400725292211298",
  "elapsedSeconds": 1.212400744,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/limits-proof-argv.json",
  "argvSha256": "f79f21ae0b4d2dd90b97dabcfba80adb010396c4a96b6bceea83facb5beabc7e",
  "manifestSha256": "3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/limits-proof.stdout.jsonl",
    "size": 12142,
    "sha256": "f28b5808b761f27aade29fa951652ca765ba3d2d461fc7572e9c2f51dc0b21db"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/limits-proof.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### `limits-proof-check.json`

```json
{
  "classification": "COMPLETE",
  "verifiedRecords": 8,
  "verifiedBytes": 12142,
  "verifiedChain": "9077fa72577b1c8a497c32039a9750212a361b7ff438f3c519ffc8bbbf57d58f",
  "caseIds": [
    "mock/noop"
  ],
  "inspectedContainer": "9160e29813853f142cbbfc3e62e8216d9fd3cf5fad13142c124ec3d4d0bebe91",
  "final": {
    "expectedCaseCount": 1,
    "completedCaseCount": 1,
    "status": "complete",
    "totalCount": 8,
    "dataChainHash": "c2940239ad7a2c2f426a96499eb9f11024afc7106647f1baa5d5c5849a8e2a4b",
    "bytesBeforeFinal": 11670,
    "cap": 268435456
  },
  "error": null,
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

### `capture-kill-host-receipt.json`

```json
{
  "kind": "capture-kill",
  "exitStatus": 137,
  "started": "1793400734758139766",
  "finished": "1793400735678118031",
  "elapsedSeconds": 0.919978265,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": true,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/capture-kill-argv.json",
  "argvSha256": "a80d254589585e2b070e913e9505e700ef1db6da15b6e73f438d153c1124ffbb",
  "manifestSha256": "3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/capture-kill.stdout.jsonl",
    "size": 11328,
    "sha256": "d2245fb1761ba9b696173e2170a02c276c03bc88bed9b20a39aa3a498d81cbc9"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/capture-kill.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### `capture-kill-check.json`

```json
{
  "classification": "INCOMPLETE",
  "verifiedRecords": 6,
  "verifiedBytes": 11328,
  "verifiedChain": "f8412deadb829e1bdd102e59c45786b7c20dd3cf708b4b176295d3b2c182339e",
  "caseIds": [],
  "inspectedContainer": "29a7db768d3ff67890c4a528b145faa94e73424fae8c52676605357983abcb0b",
  "final": null,
  "error": "Error: INCOMPLETE: no normal terminal record",
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

### `deadline-negative/limits-proof-host-receipt.json`

```json
{
  "kind": "proof",
  "exitStatus": 0,
  "started": "1793400733299654300",
  "finished": "1793400734281851189",
  "elapsedSeconds": 0.982196889,
  "deadlineSeconds": 600,
  "killAfterSeconds": 10,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/deadline-negative/limits-proof-argv.json",
  "argvSha256": "79efd3548db57aa5b5cec0a9e05056475da0511a5eb0aebd2735b51398e48c8d",
  "manifestSha256": "3b4f6aa9c31ac28a345872e6a6152052dbe40f6d9ae1d57a0889e5e26135d9c2",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/deadline-negative/limits-proof.stdout.jsonl",
    "size": 12196,
    "sha256": "f7700934ed0ecba6a47c0df7d8fdcbcc1654dd111712e6df6916e3e19583ac92"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67FZBDAY9S8PY6V45EEXF07/evidence/deadline-negative/limits-proof.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
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
    "verifiedBytes": 12196,
    "verifiedChain": "06d36e113f6bdd3a18c49eb698f5a9f5e34230b8f8b92f0a4c845b90916ee3c1",
    "caseIds": [
      "mock/noop"
    ],
    "inspectedContainer": "5d02eb4a2fc1f7666111b560969f007f3fa45e769b3f9076b9b76b9d1936f360",
    "final": {
      "expectedCaseCount": 1,
      "completedCaseCount": 1,
      "status": "complete",
      "totalCount": 8,
      "dataChainHash": "6b627a49887e875b16a22e8b35f40225c835f0f004ba2972f85221d2a4b45d66",
      "bytesBeforeFinal": 11724,
      "cap": 268435456
    },
    "error": "Error: INCOMPLETE: deadline differs from approved manifest execution intent",
    "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
  },
  "outcome": "PASS: drifted actual argv refused despite a normal, intact mock capture"
}
```

## Unmet Criteria

None for the bounded Phase-P revision5 slice. No actual matrix/SDK/store experiment
was run or qualified. Phase E still requires external exact-manifest owner OK.
Section(b) remains **pending A+B+D integration tarball**, dIntegrationPin:null;
AF0.30.0 is ONLY a native CM/Chronicle driver, NOT D. No D floor/enablement claim.
Node rows remain **native-format component probes, NOT actual-old-reader proof**;
frozen actual old runtime is Bun1.4.2/source and new Bun1.4.4 remains distinct.
No probe of real stores, providers or endpoints.

## Residual Risk

- Computed87.17MiB budget is a sensitivity estimate, not an SDK/store-volume bound.
  SDK/import/store behavior is intentionally unmeasured; actual E can be incomplete.
  No volume-filter end-to-end run; ≥192MiB filtering with any drop is terminal
  INCOMPLETE, unchanged. Results require control/API/syscall correlation.
- Trust the host and no concurrent input writer during verification→child-use;
  RO mounts do not prevent hostile-host mutation. Per-child leaf checks are not
  full hostile-host attestation or authentication of all transitive code.
- Other LOW findings unchanged, no code work: inspect failure under set-e may lose
  a checker artifact (r4 LOW-4); receipt/anchor mismatch and unexpected receipt-set
  branches lack negative selftests (LOW-5); normal smoke uses exec-only trace while
  production uses file/desc/memory/process (LOW-6; optional leaf measurement now
  measures that full filter but no SDK/store workload); pread64/pwrite64/readv/writev
  retain regex is not exhaustive, decoded store paths are retained (LOW-7).
- Prior known limits unchanged: one environment/instrumentation failure stops the
  native matrix; command expectation remains a hash-bound checker literal; parser
  selects first timeout and a modified launcher can deceive self-attestation.
  The previous repeated bulk-tree/critical-input trace-cost limit IS removed here.
- No false barrier inference from EACCES/import/ABI/EROFS/ptrace errors, constructor
  exceptions or unchanged before/after inventory. Native import compatibility,
  real control behavior and syscall correlation remain for authorized E only.
