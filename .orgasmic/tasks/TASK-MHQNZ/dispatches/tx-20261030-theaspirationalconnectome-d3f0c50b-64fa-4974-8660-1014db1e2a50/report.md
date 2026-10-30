# TASK-MHQNZ — Phase P revision 2 (prep only)

## Changed

Local commit **`1bb624822c16ea58971ee1fed031b55b67a81b97`**, directly on `75d1d4d5`; clean worktree.
Vasyl Dovhanchyn `<48733736+theaspirational@users.noreply.github.com>`,
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`. No push, merge, landing,
dispatch closure, product changes or changes to the read-only main checkout.
Thirteen test-harness files changed under `test/store-probe/`; Containerfile is
unchanged. New runtime policy, live evidence framing, host checker/receipt and
no-op demonstration helpers accompany the revised harness, tests and README.
No dependency/lockfile changes; no changelog needed for test-only changes.

**No store was created, opened, read or written. Phase E was NOT run.**
No AF/CM/Chronicle source or compiled storage module was imported. No providers,
connectors or endpoints were contacted. Preparation read hashes, manifests and
package export declarations, and unpacked the sealed code cache without installs
or hooks. The only container commands were the no-store kernel/mock demonstrations.

### Correct runtime scope

The oracle's `host/package.json` SHA256 is
`5d27a2d6bf5bd61fd3c03b81d3450d30ac80a8ca628a17b54c4454ff613ea057`, matches artifact-manifest, and declares
`start: bun src/index.ts`. Old AF and CM export `bun: ./src/index.ts` versus
`import: ./dist/src/index.js`. Thus **Node rows are a native-format component
probe, NOT actual-old-reader proof**. The separate intended old-reader route is
the frozen Bun/source route. No execution result for either runtime is claimed.

- Old Bun **1.4.2**: `/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun`,
  SHA256 **`a83d263767d839e4d2649ca8e35d07159c7afc99afdc96d731ced29e056dda0c`**, verified against BOTH
  artifact-manifest `bunSha256` and `sha256["bin/bun"]`; declared bunVersion=1.4.2.
- New Bun **1.4.4**: `/srv/ci-fixtures/bun-1.4.4-linux-x64/bun`,
  SHA256 **`10702245766b225e70bbf6235fd6b47214fd94b4760d00ffaed8ab0df57ac235`**, exactly matches the
  supplied expected hash and adjacent `PROVENANCE.txt`. A provenance/hash mismatch
  aborts preparation before unpacking; its failure path is data-tested.
- Node **22.23.3** remains a separate `import`/compiled native-format component
  route; binary SHA256 `fde6a4bf8d0562f7751d1a2d6cb9b417c4cfe107bbcb0aa3e9a24e125e348f48`.
- All three runtime binaries are narrow read-only mounts. Bun gets `--no-install`.
  HOME, TMPDIR, XDG_CACHE_HOME, BUN_INSTALL, BUN_INSTALL_CACHE_DIR and
  BUN_RUNTIME_TRANSPILER_CACHE_PATH are within `/work/.../scratch` for every child.
  No new writable mount, package install, network, privilege, capability or
  seccomp/ptrace exception is granted. Bun/strace failures remain inconclusive.

Each runtime has its own Chronicle / CM / real `AgentFramework.create` routes,
with independent old→old, new→new, old-reader→new-store cases and fresh writable
synthetic clones. The predeclared plan has **18 unique runtime×route×scenario
cases**, plus seed/reopen operations for **42 maximum child operations**.
Masters, clones, scratch homes and result matrices are segregated by runtime.
In Phase E every child verifies actual runtime/version/executable SHA256 before
imports, resolves using Bun's native `bun` condition or Node ESM `import`, asserts
the exact conditional entry, and emits runtime/condition/executable on every
resolved/transitive-resolved/API event. Actual module entry files and SHA256s are
logged for each seed, reopen/control and attempt, not inferred from semver.

### Runtime × tree × route → executable / entry table

This is the **declared/hashed package-export table**, not a runtime import result.
Actual runtime resolution and transitive imports are intentionally unexecuted
until Phase E. AF/CM Bun rows select source; Chronicle has the same JS loader
entry in both conditions but different versioned native binaries, all hashed in
the complete input inventory. Cross-version cases use the OLD-side executable
and OLD-side entry on a clone created by the NEW-side writer of that same runtime.

| Runtime | Tree | Route | Version | Condition | Executable | Entry file | Entry SHA256 |
|---|---|---|---|---|---|---|---|
| `node` | `old` | `chronicle` | `22.23.3` | `import` | `/runtime/node/node` | `/inputs/old/node_modules/@animalabs/chronicle/index.js` | `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |
| `node` | `old` | `cm` | `22.23.3` | `import` | `/runtime/node/node` | `/inputs/old/node_modules/@animalabs/context-manager/dist/src/index.js` | `05c4b50d7cdd72f4ce8b9919b6680d363497757bbe096606eac3fe78a0f4166c` |
| `node` | `old` | `af` | `22.23.3` | `import` | `/runtime/node/node` | `/inputs/old/node_modules/@animalabs/agent-framework/dist/src/index.js` | `4621b06d07da7a638b03a97e04dfd447bceb744daeeef32fd3ccf82d30a02810` |
| `node` | `new` | `chronicle` | `22.23.3` | `import` | `/runtime/node/node` | `/inputs/new/node_modules/@animalabs/chronicle/index.js` | `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |
| `node` | `new` | `cm` | `22.23.3` | `import` | `/runtime/node/node` | `/inputs/new/node_modules/@animalabs/context-manager/dist/src/index.js` | `42496c855b540c66746df2653e459dc8b1de9d876d6f74fd9a72b0b9541adc17` |
| `node` | `new` | `af` | `22.23.3` | `import` | `/runtime/node/node` | `/inputs/new/node_modules/@animalabs/agent-framework/dist/src/index.js` | `50a7a8cb245004ef0b311d17208ef6086d7ef3b3f084954243633cf10391427a` |
| `bun` | `old` | `chronicle` | `1.4.2` | `bun` | `/runtime/bun-old/bun` | `/inputs/old/node_modules/@animalabs/chronicle/index.js` | `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |
| `bun` | `old` | `cm` | `1.4.2` | `bun` | `/runtime/bun-old/bun` | `/inputs/old/node_modules/@animalabs/context-manager/src/index.ts` | `1582e6e315bbdc3fe53ed2bf83d3e8210ce957605d11becb38fc4b82d1fcccbc` |
| `bun` | `old` | `af` | `1.4.2` | `bun` | `/runtime/bun-old/bun` | `/inputs/old/node_modules/@animalabs/agent-framework/src/index.ts` | `19fa4aa213eb137d6264676bad5a3e9e27b6f90a6794a7f73dd4f1beb531bed8` |
| `bun` | `new` | `chronicle` | `1.4.4` | `bun` | `/runtime/bun-new/bun` | `/inputs/new/node_modules/@animalabs/chronicle/index.js` | `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |
| `bun` | `new` | `cm` | `1.4.4` | `bun` | `/runtime/bun-new/bun` | `/inputs/new/node_modules/@animalabs/context-manager/src/index.ts` | `6b1cb6b25f20f06f9b7f7c47c3e6705f8adf285a292f27b8673584209934e0e2` |
| `bun` | `new` | `af` | `1.4.4` | `bun` | `/runtime/bun-new/bun` | `/inputs/new/node_modules/@animalabs/agent-framework/src/index.ts` | `8a5c3eebea3887b74f03dce4afc62db3b852c0b8975f1be9903fd513b96b044f` |

### Approval identities

- **Harness SHA256: `eb9c709aab8c3545c7200122a583efbdc5423f1dfa97aefdb50ae74106016aad`** — SHA256 of the bytewise
  sorted `harness.SHA256SUMS` records, including all 14 harness files.
- **Input-manifest SHA256: `7cb8c032748d678d5502c682520d52d903a05d4a41102ac5e09417e6447f3af5`**.
- Operator manifest: `/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/input-manifest.json`; schema 2;
  17954 input file/link records, including the
  old runtime roots/dependencies, both Bun binaries, new provenance, old host
  package.json, both locks, artifact manifest, all sealed cache files/tarballs,
  driver tarball, 45 exactly resolved new packages, fixture, harness, runtime/
  tracer/library inputs and per-attempt assertion manifests. Modes and sizes are
  recorded too. All operator paths, argv, inventories and private traceability
  are ONLY in evidence, not committed. The final gate rejects host paths in git.
- **Image ID: `5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6`**.
- **Image OCI manifest digest: `sha256:44b688151db6337cfb11114ea43a644311251e7d87c858dc8f252bc906b4a3dd`**.
- Base remains local `docker.io/library/ubuntu:24.04`, ID
  `6277ab668b2ad3c3ab851d370faa1690d324b30db29d342527f61fed28f20f36`. No Containerfile or image change in revision 2;
  identity re-inspected in `image-inspect-current.json`.
- Old payload: frozen patched AF 0.22.2 / CM 0.14.1 / Chronicle 0.4.0,
  reasserted against the public baseline fixture (`90fb9ef6`), not its stale npm
  lock. Runtime-root inventory sort remains full relative UTF-8 bytewise C order.
- New native payload: CM **0.24.0**, Chronicle **0.5.1**, sealed-cache SHA256SUMS
  rechecked. AF **0.30.0 is only a driver, NOT D**. Its tarball hash and all storage
  inputs remain recorded; published packages have no embedded source gitHead,
  so no source commit is invented.
- **(b) pending D integration tarball**, `dIntegrationPin: null`. No AF 0.30.0
  substitution, application-floor, D classification or enablement claim.

### Live evidence, caps and completeness

No post-exit `podman cp` dependency remains. The orchestrator immediately streams
child stdout/stderr, API events and strace lines to stdout; inventories/results
are framed when produced, and each completed attempt emits an unbuffered
`attempt-flush`. Current-attempt API/syscall bytes therefore reach the host before
an outer timeout/kill, not only when a temporary trace file is collected.

Every JSON line has sequence, type/data, its own SHA256, previous-chain hash and
new chained hash. A normal terminal has total count, data-chain hash and expected/
completed case counts; its envelope contains the final full-chain hash. Larger
objects are ordered base64 chunks. Raw traces retain syscall, path/decoded FD,
flags/mode, result, PID and absolute + relative timing (`-f -ttt -r -T -yy`) for
correlation with API refusal/exposure/mutation timestamps, NOT just trace hashes.

**Stdout maximum: 256 MiB (268435456 bytes), including a reserved terminal budget.**
Individual frames are ≤1 MiB. At 192 MiB, capture filters unrelated descriptor
traffic while retaining store-path/decoded-store-FD lines and all path, mapping,
locking and close operations. If filtered evidence still reaches the cap, it
refuses more work, stops its owned child group, emits a bounded
`limit-exceeded-inconclusive` terminal and exits nonzero. No discarded-trace hash
is substituted for syscall evidence. Unit tests exercise the cap/refusal path.

The host launcher redirects stdout/stderr into this evidence directory WITHOUT
any RW container volume. It then writes an independent process exit/deadline/
argv/capture-hash receipt and runs the separate stream checker.
**COMPLETE requires all three**: normal terminal, exact predeclared case IDs/count
(18 for native Phase E; one clearly labeled mock case for no-op), and a successful
host exit/deadline receipt. Breaks, missing/duplicate cases, truncated lines,
missing terminal, nonzero exit, cap refusal or timeout mean **INCOMPLETE**. A hard
kill's prefix is retained; the host NEVER synthesizes a closing record.

**The hash chain proves integrity/truncation, NOT that the intended experiment
happened, and NOT compatibility, row exposure, rotation or mutation safety.**

### Deadline and envelope

**1800 seconds whole-command deadline + 30-second SIGKILL grace**, doubling the
previous 900-second budget for the doubled matrix. Each child retains 90 seconds
(+5-second kill grace); the aggregate budget is intentionally smaller than the
sum of all 42 worst cases. A partial matrix is INCOMPLETE, never a compatibility
pass. The no-op demonstrates the same envelope; it does not run those cases.

Rootless as orgasmic (host uid=1002), one container at a time, container uid/gid
1000:1000, 4GiB RAM, zero additional swap, two CPUs, 256 PIDs, core soft/hard=0,
read-only root/code, cap-drop ALL, no-new-privileges, default seccomp, private
PID/cgroup/IPC, network none, no ports, no whole-home/credential/socket mounts.
Writable /work=1600MiB and /tmp=128MiB total 1728MiB; even counting read-only
/dev=64MiB and /dev/shm=16MiB gives **1808MiB ≤1920MiB**. All eleven bind inputs
are read-only. No automatic writable /run or writable host work mount. No host
session/linger/Podman config changes, privileged/rootful mode, pull or networking.
No systemd in the container; existing cgroup delegation sufficed without a new
systemd-run scope. The container's own cgroup values, not inspect alone, prove it.

### Full no-op launch argv — actually run at the committed revision

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r2-noop-final --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r2-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs > /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/limits-proof.stdout.jsonl 2> /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/limits-proof.stderr
```

### Full forced-kill capture argv — actually run, NO STORE OPERATIONS

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r2-kill-final --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r2-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs --mock-kill > /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/capture-kill.stdout.jsonl 2> /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/capture-kill.stderr
```

The host watches for the already-flushed `mock-inventory` marker, then issues
rootless `podman kill --signal KILL f-host-2b-task-mhqnz-r2-kill-final` with the
same minimal empty environment. `capture-kill-owner.txt` records command ownership;
`capture-kill-trigger.log` records the exact owned container. Host and container
both report exit **137**. Normal no-op container was removed only after its full
inspect/output receipt was saved; the killed container is stopped, not running.

### Prospective Phase E argv — NOT RUN, NOT AUTHORIZED

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r2-noop-final --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r2-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/probe.mjs --phase-e-approved --section native > /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/execute.stdout.jsonl 2> /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/execute.stderr
```

The exact manifest above must receive Soma's OK before this launch. Its name is
the removed normal no-op name and is reusable. Host `launch.sh` supplies the
receipt/checker steps automatically; an exit 0 without their complete checks is
not accepted. The D section remains fail-closed and requires a new prep manifest.

## Verification Gates

At clean local commit `1bb624822c16ea58971ee1fed031b55b67a81b97` (all reusable, exit 0):

1. `orgasmic dispatch gate phase-p-r2-noop-committed -- test/store-probe/launch.sh proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r2-noop-final /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r2-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence 7cb8c032748d678d5502c682520d52d903a05d4a41102ac5e09417e6447f3af5 native`
   — full arguments are the argv-generation call in the receipt/log, with exact
   container argv above. Kernel limits enforced; mock stream COMPLETE.
   Log: `/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/gate-phase-p-r2-noop-committed-01M67CGE4V8VCA1S8HFERNSQ2T.log`.
2. `orgasmic dispatch gate phase-p-r2-kill-committed -- test/store-probe/launch.sh capture-kill 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r2-kill-final /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r2-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence 7cb8c032748d678d5502c682520d52d903a05d4a41102ac5e09417e6447f3af5 native`
   — deliberately killed mock container returns 137; separate checker returns 2
   and INCOMPLETE with intact prefix. Gate passes because that is the required
   failure detection, NOT a successful experiment.
   Log: `/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/gate-phase-p-r2-kill-committed-01M67CGFRETXW0R8T2RXAS9MER.log`.
3. `orgasmic dispatch gate phase-p-r2-prep-committed -- python3 test/store-probe/check-prep.py /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence`
   — **12 Node data/mock tests + 5 Python data/archive tests pass, 0 failures**;
   syntax, no committed host paths, all input bytes/modes/sizes, exact harness
   file set, runtime/conditional-entry declarations, 18-case/42-operation plan,
   both capture receipts/checker outcomes, kernel limits and HostConfig checked.
   No storage SDK import or store operation.
   Log: `/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/gate-phase-p-r2-prep-committed-01M67CGH37SHA28WAA7Y21ZA9G.log`.

Fresh-input generation was also run through `phase-p-r2-inputs-final` (dirty
pre-commit receipt, not reused as a clean gate): verified both Bun inputs,
provenance, oracle hashes and all cache checksums, unpacked into ignored
`tmp-store-probe/new-r2-final`, generated this exact manifest/table.
Log: `/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/gate-phase-p-r2-inputs-final-01M67CDVDGS1TCCM9JZEVXSHNZ.log`. Final clean gate rehashed the resulting
inputs. No project-wide suite was assigned or inferred.

### Normal no-op mock capture and host receipt

Container `7562c2311ca77efd15c9178eb8099927c93bdfafd2aaf6327493c48eb27ef3ce`; kernel reads plus mocks only; exit 0; stderr empty.
Full framed capture: `limits-proof.stdout.jsonl` (including the limits-proof
frame); below are its plan/mock/result/final frames, with their own hashes:

```jsonl
{"sequence":1,"type":"plan","data":{"kind":"mock","expectedCases":["mock/noop"]},"sha256":"6df06df947e6134874bf7a1d86bbed8bd533a8679f536e39de6599f64ad5cc2d","previous":"0000000000000000000000000000000000000000000000000000000000000000","chain":"86bdba3a4940314e5d8f4a29fa2e751281e7fa3f6568ee4fa92f154d117cb4ab"}
{"sequence":3,"type":"mock-event","data":{"synthetic":true,"stage":"before-attempt"},"sha256":"aaadee10f6fb8fc723f9a905a536e31c259f376eeaba92120bbfa40e89d4f144","previous":"168b1c7da5d0d0b297575b094181a98cc314c9f80f184a17d43f972937c02636","chain":"f7fc286c20a8a9931829dc5391c592616d5b41f8c07dca3a8f1b9dafae5d3f7b"}
{"sequence":4,"type":"mock-inventory","data":{"synthetic":true,"files":[]},"sha256":"910633f9c53a9c867c3dc0d02e67ef5ae8860a95c4fa0d188bb2a7d22c0e3dae","previous":"f7fc286c20a8a9931829dc5391c592616d5b41f8c07dca3a8f1b9dafae5d3f7b","chain":"5d66d571635ef5dd7a4740f00ec1d1ff465817643ea443acbab0a70d6b04e118"}
{"sequence":5,"type":"case-result","data":{"caseId":"mock/noop","synthetic":true,"result":"NO STORE OPERATIONS"},"sha256":"a843141662c3e31e4727307b230904a1532dbf240f9e65640234c66c24f8e9a7","previous":"5d66d571635ef5dd7a4740f00ec1d1ff465817643ea443acbab0a70d6b04e118","chain":"be5d0beb03e5f5b29527c7f42bc0aa626c58dfab8397c92af54bfd9d57ea9f18"}
{"sequence":6,"type":"final","data":{"expectedCaseCount":1,"completedCaseCount":1,"status":"complete","totalCount":6,"dataChainHash":"be5d0beb03e5f5b29527c7f42bc0aa626c58dfab8397c92af54bfd9d57ea9f18","bytesBeforeFinal":9496,"cap":268435456},"sha256":"69f357384cab8f7732c0807171efc7dab795eb5a13b6ecef1a0c758639bf6c4d","previous":"be5d0beb03e5f5b29527c7f42bc0aa626c58dfab8397c92af54bfd9d57ea9f18","chain":"8828c514edacecabb7e5e6bbdb0c1fd8272cd102ffbeb59a7c4f64db5a766f31"}
```

Independent checker:
```json
{
  "classification": "COMPLETE",
  "verifiedRecords": 6,
  "verifiedBytes": 9967,
  "verifiedChain": "8828c514edacecabb7e5e6bbdb0c1fd8272cd102ffbeb59a7c4f64db5a766f31",
  "caseIds": [
    "mock/noop"
  ],
  "final": {
    "expectedCaseCount": 1,
    "completedCaseCount": 1,
    "status": "complete",
    "totalCount": 6,
    "dataChainHash": "be5d0beb03e5f5b29527c7f42bc0aa626c58dfab8397c92af54bfd9d57ea9f18",
    "bytesBeforeFinal": 9496,
    "cap": 268435456
  },
  "error": null,
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

Host exit/deadline/capture receipt:
```json
{
  "kind": "proof",
  "exitStatus": 0,
  "started": "1793396848802083412",
  "finished": "1793396850012186268",
  "elapsedSeconds": 1.210102856,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvSha256": "2b087eebaa09c915fd30f101188ff39b9a706a08a686831d0b9c1bdde59c5b1e",
  "manifestSha256": "7cb8c032748d678d5502c682520d52d903a05d4a41102ac5e09417e6447f3af5",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/limits-proof.stdout.jsonl",
    "size": 9967,
    "sha256": "91a61e562d5d17ca8cde891664be73861b0f65170f416e2d04b85c2477bbf046"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/limits-proof.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### Forced kill in the middle: preserved prefix, INCOMPLETE

Container `404eeb3ad6829e338240b7975d7f16e96ac01e53f6e373bbf61ea50a3c4ab210`; four full verified frames (plan, limits, mock event,
mock inventory), **no case result and no final**; raw prefix remains untouched
in `capture-kill.stdout.jsonl`. This demonstrates evidence already outside the
container despite the lost tmpfs. Non-limits mock prefix:

```jsonl
{"sequence":1,"type":"plan","data":{"kind":"mock","expectedCases":["mock/noop"]},"sha256":"6df06df947e6134874bf7a1d86bbed8bd533a8679f536e39de6599f64ad5cc2d","previous":"0000000000000000000000000000000000000000000000000000000000000000","chain":"86bdba3a4940314e5d8f4a29fa2e751281e7fa3f6568ee4fa92f154d117cb4ab"}
{"sequence":3,"type":"mock-event","data":{"synthetic":true,"stage":"before-attempt"},"sha256":"aaadee10f6fb8fc723f9a905a536e31c259f376eeaba92120bbfa40e89d4f144","previous":"f6d57810d2e4034dec9b6d36c2d4769b000c716350c387547d4e2261ee2e8fcb","chain":"e72fbf8f191e943b3e0b0df05d27c34c6b98a0b42855465415ee2d37a6e8e8cf"}
{"sequence":4,"type":"mock-inventory","data":{"synthetic":true,"files":[]},"sha256":"910633f9c53a9c867c3dc0d02e67ef5ae8860a95c4fa0d188bb2a7d22c0e3dae","previous":"e72fbf8f191e943b3e0b0df05d27c34c6b98a0b42855465415ee2d37a6e8e8cf","chain":"b01bc312b54b28613e45d44747ffc788c8793b25054c31627afd9d25ebeaac84"}
```

Checker (separate checker exit **2**, deliberately INCOMPLETE):
```json
{
  "classification": "INCOMPLETE",
  "verifiedRecords": 4,
  "verifiedBytes": 9153,
  "verifiedChain": "b01bc312b54b28613e45d44747ffc788c8793b25054c31627afd9d25ebeaac84",
  "caseIds": [],
  "final": null,
  "error": "Error: INCOMPLETE: no normal terminal record",
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```

Host exit/deadline/capture receipt:
```json
{
  "kind": "capture-kill",
  "exitStatus": 137,
  "started": "1793396850452699494",
  "finished": "1793396851561488225",
  "elapsedSeconds": 1.108788731,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": true,
  "argvSha256": "ee8d1a2699deb4502b01d663190b37817619e0bd9cb51c08ad6c0351575e5c4a",
  "manifestSha256": "7cb8c032748d678d5502c682520d52d903a05d4a41102ac5e09417e6447f3af5",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/capture-kill.stdout.jsonl",
    "size": 9153,
    "sha256": "1ef0e69b416e90a629f7ea6ff9666fbf30b1576655ba248c24b4832bb1c66e95"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/capture-kill.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

Initial pre-commit self-SIGKILL mock attempt did NOT actually kill namespace PID 1:
Podman returned 0 but no terminal was emitted. Crucially the checker still
reported INCOMPLETE, and the demo gate failed rather than passing on exit 0.
That mock-method defect was corrected to an observed-prefix **host** SIGKILL,
without relaxing any sandbox setting. Failed gate log:
`/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/gate-phase-p-r2-capture-kill-01M67CC9MYEX0B7Q0TB6F8788E.log`. Raw failed/superseded captures, receipts and
manifest are retained as `superseded-selfkill-*`. They are not selected results.

### Full final kernel limits-proof output

Decoded only from the hash-verified normal capture (`limits-proof.stdout`):

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
998 343 0:57 / / ro,relatime - overlay overlay rw,lowerdir=/home/orgasmic/.local/share/containers/storage/overlay/l/NP42E625VWWJYXWBFHJBCAONY6,upperdir=/home/orgasmic/.local/share/containers/storage/overlay/9444b676d4649de8dcee287618371bd1478b7d0d4e6caadb1d723380981df72a/diff,workdir=/home/orgasmic/.local/share/containers/storage/overlay/9444b676d4649de8dcee287618371bd1478b7d0d4e6caadb1d723380981df72a/work,redirect_dir=nofollow,uuid=on,userxattr
1001 998 0:65 / /tmp rw,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=131072k,uid=1002,gid=1002,inode64
1000 998 0:66 / /sys ro,nosuid,nodev,noexec,relatime - sysfs sysfs ro
1002 998 0:67 / /dev ro,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,uid=1002,gid=1002,inode64
1003 998 0:68 / /proc rw,nosuid,nodev,noexec,relatime - proc proc rw
1005 998 0:69 / /work rw,nosuid,nodev,relatime - tmpfs tmpfs rw,size=1638400k,uid=1002,gid=1002,inode64
1004 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /harness ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1006 1002 0:70 / /dev/pts rw,nosuid,noexec,relatime - devpts devpts rw,gid=231076,mode=620,ptmxmode=666
1007 998 0:46 /containers/overlay-containers/7562c2311ca77efd15c9178eb8099927c93bdfafd2aaf6327493c48eb27ef3ce/userdata/hosts /etc/hosts ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1008 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/input-manifest.json /inputs/input-manifest.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1009 998 0:46 /containers/overlay-containers/7562c2311ca77efd15c9178eb8099927c93bdfafd2aaf6327493c48eb27ef3ce/userdata/hostname /etc/hostname ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1010 998 0:46 /containers/overlay-containers/7562c2311ca77efd15c9178eb8099927c93bdfafd2aaf6327493c48eb27ef3ce/userdata/.containerenv /run/.containerenv ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1011 1002 0:49 / /dev/shm ro,nosuid,nodev,noexec,relatime - tmpfs shm rw,size=16384k,uid=1002,gid=1002,inode64
1012 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /tools/lib ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1013 998 252:1 /usr/bin/strace /tools/strace ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1014 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/new-tree.json /inputs/new-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1015 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r2-final /inputs/new ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1016 1002 0:63 / /dev/mqueue rw,nosuid,nodev,noexec,relatime - mqueue mqueue rw
1017 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/old-tree.json /inputs/old-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1018 1000 0:29 / /sys/fs/cgroup ro,nosuid,nodev,noexec,relatime - cgroup2 cgroup rw,nsdelegate,memory_recursiveprot
1019 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /inputs/old/node_modules ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1020 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /runtime/bun-old/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1021 998 252:1 /opt/node-v22.23.3-linux-x64/bin/node /runtime/node/node ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1022 998 252:1 /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /runtime/bun-new/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1023 1002 0:5 /null /dev/null rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1024 1002 0:5 /random /dev/random rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1025 1002 0:5 /full /dev/full rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1026 1002 0:5 /tty /dev/tty rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1027 1002 0:5 /zero /dev/zero rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1028 1002 0:5 /urandom /dev/urandom rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
344 1003 0:68 /bus /proc/bus ro,nosuid,nodev,noexec,relatime - proc proc rw
345 1003 0:68 /fs /proc/fs ro,nosuid,nodev,noexec,relatime - proc proc rw
349 1003 0:68 /irq /proc/irq ro,nosuid,nodev,noexec,relatime - proc proc rw
350 1003 0:68 /sys /proc/sys ro,nosuid,nodev,noexec,relatime - proc proc rw
351 1003 0:68 /sysrq-trigger /proc/sysrq-trigger ro,nosuid,nodev,noexec,relatime - proc proc rw
375 1003 0:71 / /proc/acpi ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
400 1003 0:5 /null /proc/kcore rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
445 1003 0:5 /null /proc/keys rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
447 1003 0:5 /null /proc/latency_stats rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
449 1003 0:5 /null /proc/timer_list rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
453 1003 0:75 / /proc/scsi ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
549 1000 0:76 / /sys/firmware ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
554 1000 0:77 / /sys/dev/block ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo:       0       0    0    0    0     0          0         0        0       0    0    0    0     0       0          0
```

### Full normal no-op HostConfig

```json
{
  "Binds": [
    "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro,rprivate,rbind",
    "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/old-tree.json:/inputs/old-tree.json:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/new-tree.json:/inputs/new-tree.json:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r2-final:/inputs/new:ro,rprivate,rbind",
    "/usr/bin/strace:/tools/strace:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67BPB295V5D0XX7A2BKCTVQ/evidence/input-manifest.json:/inputs/input-manifest.json:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro,rprivate,rbind",
    "/opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro,rprivate,rbind",
    "/srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro,rprivate,rbind"
  ],
  "CgroupManager": "systemd",
  "CgroupMode": "private",
  "ContainerIDFile": "",
  "LogConfig": {
    "Type": "journald",
    "Config": null,
    "Path": "",
    "Tag": "",
    "Size": "0B"
  },
  "NetworkMode": "none",
  "PortBindings": {},
  "RestartPolicy": {
    "Name": "",
    "MaximumRetryCount": 0
  },
  "AutoRemove": false,
  "VolumeDriver": "",
  "VolumesFrom": null,
  "CapAdd": [],
  "CapDrop": [
    "CAP_CHOWN",
    "CAP_DAC_OVERRIDE",
    "CAP_FOWNER",
    "CAP_FSETID",
    "CAP_KILL",
    "CAP_NET_BIND_SERVICE",
    "CAP_SETFCAP",
    "CAP_SETGID",
    "CAP_SETPCAP",
    "CAP_SETUID",
    "CAP_SYS_CHROOT"
  ],
  "Dns": [],
  "DnsOptions": [],
  "DnsSearch": [],
  "ExtraHosts": [],
  "GroupAdd": [],
  "IpcMode": "private",
  "Cgroup": "",
  "Cgroups": "default",
  "Links": null,
  "OomScoreAdj": 0,
  "PidMode": "private",
  "Privileged": false,
  "PublishAllPorts": false,
  "ReadonlyRootfs": true,
  "SecurityOpt": [
    "no-new-privileges"
  ],
  "Tmpfs": {
    "/tmp": "rw,nosuid,nodev,noexec,size=128m,mode=1777,rprivate,tmpcopyup",
    "/work": "rw,nosuid,nodev,size=1600m,mode=1777,rprivate,tmpcopyup"
  },
  "UTSMode": "private",
  "UsernsMode": "",
  "ShmSize": 16777216,
  "Runtime": "oci",
  "ConsoleSize": [
    0,
    0
  ],
  "Isolation": "",
  "CpuShares": 0,
  "Memory": 4294967296,
  "NanoCpus": 2000000000,
  "CgroupParent": "user.slice",
  "BlkioWeight": 0,
  "BlkioWeightDevice": null,
  "BlkioDeviceReadBps": null,
  "BlkioDeviceWriteBps": null,
  "BlkioDeviceReadIOps": null,
  "BlkioDeviceWriteIOps": null,
  "CpuPeriod": 100000,
  "CpuQuota": 200000,
  "CpuRealtimePeriod": 0,
  "CpuRealtimeRuntime": 0,
  "CpusetCpus": "",
  "CpusetMems": "",
  "Devices": [],
  "DiskQuota": 0,
  "KernelMemory": 0,
  "MemoryReservation": 0,
  "MemorySwap": 4294967296,
  "MemorySwappiness": -1,
  "OomKillDisable": false,
  "PidsLimit": 256,
  "Ulimits": [
    {
      "Name": "RLIMIT_CORE",
      "Soft": 0,
      "Hard": 0
    },
    {
      "Name": "RLIMIT_NOFILE",
      "Soft": 1048576,
      "Hard": 1048576
    },
    {
      "Name": "RLIMIT_NPROC",
      "Soft": 353396,
      "Hard": 353396
    }
  ],
  "CpuCount": 0,
  "CpuPercent": 0,
  "IOMaximumIOps": 0,
  "IOMaximumBandwidth": 0,
  "CgroupConf": null
}
```

`container-inspect.json` and `capture-kill-container-inspect.json` additionally
retain user, mount RW flags, network settings and final states. memory.max is
4294967296, memory.swap.max=0, cpu.max=200000/100000, pids.max=256; all five
capability sets zero; NoNewPrivs=1; Seccomp=2; core limits zero; only lo present.
No unenforced limit was observed in either envelope.

### Valid inherited evidence and clean-build provenance

No earlier gate pass or limits proof is reused. New argv, Bun inputs and streaming
protocol were proven anew in this round. Unchanged image-build provenance only
is inherited from `/home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence`; copies are in this round as
`inherited-image-build-first-env.log`, `inherited-image-build-empty-env-failed.log`,
`inherited-image-build-empty-env-final.log`, `inherited-image-inspect.json`.
Current image inspect matches the inherited config ID/manifest digest; identical
Containerfile has no RUN/install/copy steps, so no rebuild is required.

**Inherited-environment disclosure preserved:** the first build preceded the
binding empty-environment message and inherited the worker environment. It still
used --pull=never and --network=none; it is NOT the selected clean-build receipt.
The subsequent cap-drop-all mkdir RUN attempt failed with permission denied.
The selected final metadata-only build removed RUN and succeeded with:

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C /usr/bin/podman build --pull=never --network=none --no-cache --http-proxy=false --cap-drop=ALL --security-opt=no-new-privileges -f test/store-probe/Containerfile -t localhost/f-host-2b-phase-p:task-mhqnz test/store-probe
```

These are the ONLY preserved environment keys: HOME (rootless storage discovery),
PATH (local tools), XDG_RUNTIME_DIR and DBUS_SESSION_BUS_ADDRESS (existing user
service/cgroup access), LC_ALL=C (determinism). No run token/proxy/credentials
were passed. No apt/npm/package install, lifecycle hook, network, auto-pull or
systemd-in-container occurred. Missing public input would mean STOP and an
acquisition-method report, never enabling networking; no missing input was found.

## Unmet Criteria

No revision-2 preparation criterion is known unmet. Phase E is deliberately not
run, so the declared native matrix has **zero experimental results** and no
exposure, rotation, mutation-safety, old-reader-refusal or compatibility claim.

**(b) pending D integration tarball; pin remains null.** The distinct tarball
parameter accepts/hashes an artifact but refuses execution (exit 78) until the
one A+B+D release, source/integration commit, complete dependency/export manifest
and real D adapter can be qualified. Neither runtime's AF 0.30.0 native driver
is D. This remains the explicitly allowed integration dependency, requiring a
new exact Phase P manifest and owner OK; no fabricated stamp/floor result.

## Residual Risk

- Real Bun/source and Node/compiled imports, native ABI, strace/ptrace piping,
  store opens, mutations, close paths and production API behavior have NOT been
  exercised. Any import/ABI/EACCES/EROFS/Bun/ptrace control failure is inconclusive,
  never a native barrier and never permission to relax the envelope.
- Hash chains/counts and host receipts establish capture integrity/completeness
  only, not that the intended experiment happened. Phase E must inspect actual
  executable/entry events, both positive controls and native syscall/API detail.
- Kernel/native writable mmap and restored-byte writes require trace correlation;
  final inventories alone prove no absence of writes. RefusalBeforeMutation stays
  null pending that review. Header reads are not zero-read or zero-exposure proof.
- The aggregate deadline or stdout cap can stop a slow/noisy matrix. A verified
  prefix is retained and labeled INCOMPLETE; it cannot be a no-fence decision.
- Linux x64 glibc is the only declared native platform. No D enablement,
  deployment, rollback, live store operation or arbitrary/future reader guarantee.
  **STOP here for Soma's exact-manifest OK; keep (b) pending.**
