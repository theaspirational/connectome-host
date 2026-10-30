# TASK-MHQNZ — Phase P revision 3, focused fix slice

## Changed

Local commit **`e3f07c16a3983431b096d747477fda8d20bf3c38`**, directly on
`1bb624822c16ea58971ee1fed031b55b67a81b97`; clean worktree. Vasyl Dovhanchyn
`<48733736+theaspirational@users.noreply.github.com>` with
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`. Sixteen scoped harness/test/doc
files changed, four new helpers; no product, dependency, lockfile or Containerfile
change, no unrelated LOW fixes, push, merge, landing or main-checkout edit.

Read the static review **in full** and reproduced SHA256
**`be6d96a90e958334d7cd2ef4ad2c66fb76ee966bd8862b82b7ba7ec55fa0abef`** (`be6d96a9…`), copied as `static-review.md`.
All changes below map to the five requested items.

**Phase E was NOT run. No store was created/opened/read/written, and no AF, CM or
Chronicle module was imported.** Only data/mock checks and the explicitly requested
strace-on-`/bin/true` no-op ran. No provider, connector or endpoint was contacted.

### Selected approval identities

- **New harness SHA256: `de053eb0ae7298f972a01f4cc3b616192dd8863bce7dbb7b649b211bfab3481b`** (SHA256 of the C/bytewise
  sorted `harness.SHA256SUMS`, all 18 harness files).
- **New input-manifest SHA256: `337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85`**.
- Operator manifest: `/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/input-manifest.json`. Contains complete input
  inventories, unchanged Bun/Node payload identities and runtime route declarations,
  image identities, 18 native case IDs, 42 maximum child operations, 256-MiB stdout
  cap and deadlines sourced from runtime.mjs. Operator paths/inventories, actual
  argv and the deliberately changed launcher exist ONLY in evidence, not git.
- Image unchanged: **ID `5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6`**;
  **OCI digest `sha256:44b688151db6337cfb11114ea43a644311251e7d87c858dc8f252bc906b4a3dd`**. Current identity re-inspected in
  `image-inspect-current.json`; Containerfile unchanged, no rebuild required.
- Same local Ubuntu base ID `6277ab668b2ad3c3ab851d370faa1690d324b30db29d342527f61fed28f20f36`; no pull/network/install,
  privileged/rootful mode, host configuration/session/linger change or systemd
  inside the container.
- Node rows remain **native-format component probes**, not actual-old-Bun-reader
  proof. Old Bun is pinned 1.4.2/source, new Bun 1.4.4/source; all hashes and the
  provenance were rechecked during preparation. AF 0.30.0 remains only a native
  CM/Chronicle driver, NOT D. **(b) pending D integration tarball**, pin null.

### Delta 1 — actual deadline/argv binding (MEDIUM)

- `runtime.mjs` defines wholeDeadlineSeconds and wholeKillAfterSeconds as the
  single normative source. Both preparation and launch.sh load them; no receipt
  or checker asserts matching timeout literals.
- The launcher now records the **exact launched argv array as JSON** alongside
  its human shell rendering. `host-receipt.mjs` parses timeout's actual arguments
  from that JSON and records its SHA256/path plus observed process exit/timing.
- The checker rehashes/reparses that recorded argv, compares its parsed deadlines
  both to the receipt and to the **explicitly hash-bound approved manifest's**
  capture limits, and checks actual image/command against declared execution
  intent. It does not shell-evaluate recorded text.
- The real negative gate edits only a private evidence copy of the launcher's
  timeout invocation to **600s/10s**. The mock container exited **0**, and its
  chained eight-record stream had a **normal terminal and the expected mock
  case**. The receipt correctly parsed 600/10; checker and launcher REFUSED
  against 1800/30 with `deadline differs from approved manifest execution intent`.
  This is actual changed-launcher evidence, not a matching-constants assertion.
  Full result: `deadline-negative/deadline-negative-result.json` below.

### Delta 2 — trace filter accounting / uncertainty

- Added retainedLines/retainedBytes/droppedLines/droppedBytes counters.
  `trace-filter-stats` is emitted at attempt start, once per second while active,
  and at attempt end. The same counters/filter state are included in each result.
- Any dropped line conservatively marks `traceCorrelation: uncertain-filtered`
  and the case `trace-filtered-uncertain-NOT-PASS`; loss is never silently treated
  as correlated safe access. Existing path/mapping/lock retention and stdout cap
  remain unchanged. No trace bytes are replaced by a hash.
- Data-only test feeds retained store-FD and discarded unrelated-FD lines and
  verifies both byte and line totals. No store experiment or real filtered-store
  trace is claimed; the native path is deliberately unexecuted.

### Delta 3 — critical input trust before use

- A stdlib-only bootstrap first verifies the explicit trusted manifest SHA256,
  then verifies old-tree.json/new-tree.json against their **tree-assertion input
  entries**, the complete /harness file set/bytes/sizes/modes, /tools/strace and
  the exact trace-library file set/bytes/sizes/modes.
- The host launch preflight runs before helper loading. In-container no-op and
  orchestrator recheck before dynamically importing helpers or invoking strace;
  each worker rechecks before using its assertion file or importing storage
  packages. The manifest SHA256 is explicitly supplied via the read-only-input
  launch environment, not derived from an untrusted manifest after the fact.
- Any mismatch throws INCOMPLETE before SDK imports; a worker/bootstrap failure
  also stops the native run rather than spending all 42 controls. Tests cover
  tampered assertion/harness/strace/library files and a wrong manifest hash.
- Host preflight receipt: `critical-inputs-host.json`. Captured no-op verifier
  receipt:

```json
{
  "verified": [
    {
      "label": "harness",
      "files": 18
    },
    {
      "label": "strace",
      "files": 1
    },
    {
      "label": "old-tree-assertions",
      "files": 1
    },
    {
      "label": "new-tree-assertions",
      "files": 1
    },
    {
      "label": "trace-lib-libunwind-ptrace.so.0",
      "files": 1
    },
    {
      "label": "trace-lib-libunwind-x86_64.so.8",
      "files": 1
    },
    {
      "label": "trace-lib-libunwind.so.8",
      "files": 1
    },
    {
      "label": "trace-lib-liblzma.so.5",
      "files": 1
    }
  ]
}
```

This is content pinning against accidental/stale input drift, not a hostile-host
or owner-consent authentication claim. The small stdlib verifier is the bootstrap.

### Delta 4 — in-envelope /dev/fd + strace smoke and early native stop

- No-op invokes exactly **`/tools/strace -o /dev/fd/3 /bin/true`** and frames
  /dev/fd existence, exit/error and actual trace bytes. It runs with the identical
  nonroot, cap-drop ALL, no-new-privileges, default-seccomp, private namespaces,
  read-only code/root and network-none envelope; **no relaxation**.
- The smoke discovered an actual transport defect: libuv stdio “pipes” are
  sockets, so strace's fopen(/dev/fd/3) failed with ENXIO. The checker correctly
  marked that pre-commit attempt INCOMPLETE (exit 2); raw failure and manifest are
  preserved as `failed-socket-smoke-*`, gate log
  `/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/gate-phase-p-r3-noop-01M67DSEH3T2M5ZTANCX3X29KW.log`.
- Focused fd/strace fix: a **real OS shell pipe**, with `/bin/bash -o pipefail`,
  supplies fd 3 while cat forwards trace bytes. Native tracee stdout and API
  events remain separate. Pipefail preserves tracer failure; only existing base
  image tools are used, not an install or a permission/capability change.
- The final smoke PASSED: /dev/fd exists, traced `/bin/true` exit 0, empty stderr,
  3030 raw trace bytes streamed. Full smoke below.
- The native entry repeats the smoke before any store work; any ptrace/ABI/loader/
  instrumentation failure stops the native run immediately as INCOMPLETE. Real
  worker, Bun source, NAPI and store behavior remain untested and unauthorized.

### Delta 5 — explicit external authority boundary

README and guard text now state **`--phase-e-approved` is a purpose marker, NOT
an authority boundary or evidence of owner consent**. Neither the manifest hash,
recorded argv nor unkeyed chain authenticates Soma's consent. Owner authority and
Phase E's operational go remain external. No signing/approval service or optional
private nonce/replay scheme was added; no replay/consent authentication is claimed.
Do not execute until external owner OK of the exact manifest above.

## Verification Gates

All four assigned gates were re-run at the clean local commit above; no earlier
round gate pass or limits proof is assumed. Logs/commands:

### phase-p-r3-noop-committed: PASS, exit 0 (reusable)

```sh
orgasmic dispatch gate phase-p-r3-noop-committed -- test/store-probe/launch.sh proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r3-noop-final /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence 337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85 native
```
Log: `/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/gate-phase-p-r3-noop-committed-01M67E017CXXHH1VTAS8N9PA6Q.log`.

### phase-p-r3-kill-committed: PASS, exit 0 (reusable)

```sh
orgasmic dispatch gate phase-p-r3-kill-committed -- test/store-probe/launch.sh capture-kill 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r3-kill /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence 337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85 native
```
Log: `/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/gate-phase-p-r3-kill-committed-01M67E03G15FTYYS7N544HHDSX.log`.

### phase-p-r3-deadline-negative-committed: PASS, exit 0 (reusable)

```sh
orgasmic dispatch gate phase-p-r3-deadline-negative-committed -- python3 test/store-probe/deadline-negative.py proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r3-deadline /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative native
```
Log: `/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/gate-phase-p-r3-deadline-negative-committed-01M67E02D0BS49138GH1G00XBD.log`.

### phase-p-r3-check-prep-committed: PASS, exit 0 (reusable)

```sh
orgasmic dispatch gate phase-p-r3-check-prep-committed -- python3 test/store-probe/check-prep.py /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence
```
Log: `/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/gate-phase-p-r3-check-prep-committed-01M67E04E7C845N1STHAWT292K.log`.

`check-prep`: **16 Node data/mock tests + 5 Python data/archive tests pass, zero
failures**; syntax, all input bytes/modes/sizes, exact harness file set/no committed
host paths, current manifest-derived deadlines, normal + kill capture checks,
actual negative deadline refusal, strace smoke, kernel limits and HostConfig all
checked. It imports only test helpers/stdlib, not storage packages. No full suite
was requested or inferred. Dirty pre-commit input-generation receipts are not
reused as clean gates; final clean verification rehashes their outputs.

### Full launch argvs

The JSON `*-argv.json` sidecars are the machine authority for receipt parsing;
the following are their human shell renderings, plus host redirections. Same
whitelisted empty environment as prior clean-build provenance; no run token,
proxy or credentials is passed. All eleven bind inputs are read-only, with the
manifest SHA256 added as a nonsecret explicit container environment value.

### Normal no-op actually run

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r3-noop-final --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs > /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/limits-proof.stdout.jsonl 2> /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/limits-proof.stderr
```
### Kill demonstration actually run

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r3-kill --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs --mock-kill > /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/capture-kill.stdout.jsonl 2> /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/capture-kill.stderr
```
### 600s/10s deadline-negative actually run

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=10s 600s podman run --name f-host-2b-task-mhqnz-r3-deadline --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs > /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/limits-proof.stdout.jsonl 2> /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/limits-proof.stderr
```
### Native Phase E prospective — NOT RUN / NOT AUTHORIZED

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r3-noop-final --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85 --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/probe.mjs --phase-e-approved --section native > /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/execute.stdout.jsonl 2> /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/execute.stderr
```


Normal and kill whole-command budget is **1800 seconds + 30-second kill grace**,
from runtime.mjs; 90-second (+5 grace) child budgets and all other sandbox limits
are unchanged. The negative changed launcher is intentionally 600s/10s, is NOT
an approved execution recipe and was REFUSED. All demonstrations ran sequentially,
one rootless container at a time; only owned stopped demonstration containers
were removed after saving inspection. The final killed container is stopped.

### All selected host/checker receipts

### Normal no-op: host receipt

```json
{
  "kind": "proof",
  "exitStatus": 0,
  "started": "1793398408530497515",
  "finished": "1793398409281773174",
  "elapsedSeconds": 0.751275659,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/limits-proof-argv.json",
  "argvSha256": "c4288bc26babe6efabfa460e532e3bee73e255e6c9b91c3adc49dfce643ff536",
  "manifestSha256": "337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/limits-proof.stdout.jsonl",
    "size": 17560,
    "sha256": "6fa9e20aef9094ce1945318a66a4a1e220c4205767ed5755299a3767e9a370ed"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/limits-proof.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### Normal no-op: separate checker receipt

```json
{
  "classification": "COMPLETE",
  "verifiedRecords": 8,
  "verifiedBytes": 17560,
  "verifiedChain": "f6f3c25f18d9fe6f42a6f6f0797072dd86bdf67b345d253a8ace50c7fc73445f",
  "caseIds": [
    "mock/noop"
  ],
  "final": {
    "expectedCaseCount": 1,
    "completedCaseCount": 1,
    "status": "complete",
    "totalCount": 8,
    "dataChainHash": "8ae309b53f5c9aa077eebe101f5c7458d86cc29cd2b66afc16e3a74c188a9e53",
    "bytesBeforeFinal": 17088,
    "cap": 268435456
  },
  "error": null,
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```
### Forced kill: host receipt

```json
{
  "kind": "capture-kill",
  "exitStatus": 137,
  "started": "1793398410831751928",
  "finished": "1793398411525097401",
  "elapsedSeconds": 0.693345473,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": true,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/capture-kill-argv.json",
  "argvSha256": "6eacd6d11699c7204a910fb0cb46cfbce96b26d2017636ae0b79d41d8eb37090",
  "manifestSha256": "337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/capture-kill.stdout.jsonl",
    "size": 16745,
    "sha256": "8397c4e0ab6d9266e4078e663902a258b31045e39660f8cd7baa99478c2aa942"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/capture-kill.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### Forced kill: separate checker receipt

```json
{
  "classification": "INCOMPLETE",
  "verifiedRecords": 6,
  "verifiedBytes": 16745,
  "verifiedChain": "6ca16ea162ccf047c865fc16e1630f424b60dd07a1a80a01300166bcd0735916",
  "caseIds": [],
  "final": null,
  "error": "Error: INCOMPLETE: no normal terminal record",
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```
### Deadline-negative mock: host receipt

```json
{
  "kind": "proof",
  "exitStatus": 0,
  "started": "1793398409754964268",
  "finished": "1793398410375047332",
  "elapsedSeconds": 0.620083064,
  "deadlineSeconds": 600,
  "killAfterSeconds": 10,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/limits-proof-argv.json",
  "argvSha256": "91b6fbe84671895aef2ac6727784cd28f091a9e010dbd91e43cace5fb48baff0",
  "manifestSha256": "337e3bf21e2bc5f8186db37742712da522ade03a238f1d82d12d67a26b103a85",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/limits-proof.stdout.jsonl",
    "size": 17614,
    "sha256": "98515c414e55feedfa8a80ee5d1bf124c5b1efd26344a5099599b70ff9183f0b"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/deadline-negative/limits-proof.stderr",
    "size": 0,
    "sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

### Deadline-negative mock: separate checker receipt

```json
{
  "classification": "INCOMPLETE",
  "verifiedRecords": 8,
  "verifiedBytes": 17614,
  "verifiedChain": "e1146a6625fdef0a047203bbc8ca638144d1fb218f17c4bd3ad4ef359c3ce9da",
  "caseIds": [
    "mock/noop"
  ],
  "final": {
    "expectedCaseCount": 1,
    "completedCaseCount": 1,
    "status": "complete",
    "totalCount": 8,
    "dataChainHash": "124399d7cc4f142b8cd4666919691385cf2609d73ffc8b59dfc6bfc907311d0e",
    "bytesBeforeFinal": 17142,
    "cap": 268435456
  },
  "error": "Error: INCOMPLETE: deadline differs from approved manifest execution intent",
  "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
}
```


### Actual changed-launcher negative result

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
    "verifiedBytes": 17614,
    "verifiedChain": "e1146a6625fdef0a047203bbc8ca638144d1fb218f17c4bd3ad4ef359c3ce9da",
    "caseIds": [
      "mock/noop"
    ],
    "final": {
      "expectedCaseCount": 1,
      "completedCaseCount": 1,
      "status": "complete",
      "totalCount": 8,
      "dataChainHash": "124399d7cc4f142b8cd4666919691385cf2609d73ffc8b59dfc6bfc907311d0e",
      "bytesBeforeFinal": 17142,
      "cap": 268435456
    },
    "error": "Error: INCOMPLETE: deadline differs from approved manifest execution intent",
    "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
  },
  "outcome": "PASS: drifted actual argv refused despite a normal, intact mock capture"
}
```

The private copy is `deadline-negative/deadline-negative-launcher.sh` (mode 0700).
Its ONLY source replacement is timeout's actual invocation. Approved manifest,
original mounted harness and all other envelope parameters remain unchanged.
No normal/mock terminal was synthesized on the host.

### Full selected no-op strace smoke receipt (raw trace, not a digest)

```json
{
  "devFdExists": true,
  "transportArgv": [
    "/bin/bash",
    "-o",
    "pipefail",
    "-c",
    "\"$@\" 3>&1 | /bin/cat",
    "probe-smoke",
    "/tools/strace",
    "-o",
    "/dev/fd/3",
    "/bin/true"
  ],
  "argv": [
    "/tools/strace",
    "-o",
    "/dev/fd/3",
    "/bin/true"
  ],
  "exitStatus": 0,
  "signal": null,
  "error": null,
  "stdout": "execve(\"/bin/true\", [\"/bin/true\"], 0x7fffda5fd550 /* 14 vars */) = 0\nbrk(NULL)                               = 0x5f81c995c000\nmmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f6860fd3000\naccess(\"/etc/ld.so.preload\", R_OK)      = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v4/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v4/\", 0x7ffd6dcf19a0, 0) = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v3/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v3/\", 0x7ffd6dcf19a0, 0) = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v2/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v2/\", 0x7ffd6dcf19a0, 0) = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/\", {st_mode=S_IFDIR|0775, st_size=4096, ...}, 0) = 0\nopenat(AT_FDCWD, \"/etc/ld.so.cache\", O_RDONLY|O_CLOEXEC) = 4\nfstat(4, {st_mode=S_IFREG|0644, st_size=5067, ...}) = 0\nmmap(NULL, 5067, PROT_READ, MAP_PRIVATE, 4, 0) = 0x7f6860fd1000\nclose(4)                                = 0\nopenat(AT_FDCWD, \"/lib/x86_64-linux-gnu/libc.so.6\", O_RDONLY|O_CLOEXEC) = 4\nread(4, \"\\177ELF\\2\\1\\1\\3\\0\\0\\0\\0\\0\\0\\0\\0\\3\\0>\\0\\1\\0\\0\\0\\220\\243\\2\\0\\0\\0\\0\\0\"..., 832) = 832\npread64(4, \"\\6\\0\\0\\0\\4\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0\"..., 784, 64) = 784\nfstat(4, {st_mode=S_IFREG|0755, st_size=2129424, ...}) = 0\npread64(4, \"\\6\\0\\0\\0\\4\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0\"..., 784, 64) = 784\nmmap(NULL, 2174352, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 4, 0) = 0x7f6860dbe000\nmmap(0x7f6860de6000, 1609728, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x28000) = 0x7f6860de6000\nmmap(0x7f6860f6f000, 323584, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x1b1000) = 0x7f6860f6f000\nmmap(0x7f6860fbe000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x1ff000) = 0x7f6860fbe000\nmmap(0x7f6860fc4000, 52624, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f6860fc4000\nclose(4)                                = 0\nmmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f6860dbb000\narch_prctl(ARCH_SET_FS, 0x7f6860dbb740) = 0\nset_tid_address(0x7f6860dbba10)         = 29\nset_robust_list(0x7f6860dbba20, 24)     = 0\nrseq(0x7f6860dbc060, 0x20, 0, 0x53053053) = 0\nmprotect(0x7f6860fbe000, 16384, PROT_READ) = 0\nmprotect(0x5f81a6fcb000, 4096, PROT_READ) = 0\nmprotect(0x7f686100b000, 8192, PROT_READ) = 0\nprlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0\nmunmap(0x7f6860fd1000, 5067)            = 0\nexit_group(0)                           = ?\n+++ exited with 0 +++\n",
  "stderr": "",
  "trace": "execve(\"/bin/true\", [\"/bin/true\"], 0x7fffda5fd550 /* 14 vars */) = 0\nbrk(NULL)                               = 0x5f81c995c000\nmmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f6860fd3000\naccess(\"/etc/ld.so.preload\", R_OK)      = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v4/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v4/\", 0x7ffd6dcf19a0, 0) = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v3/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v3/\", 0x7ffd6dcf19a0, 0) = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v2/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/glibc-hwcaps/x86-64-v2/\", 0x7ffd6dcf19a0, 0) = -1 ENOENT (No such file or directory)\nopenat(AT_FDCWD, \"/tools/lib/libc.so.6\", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)\nnewfstatat(AT_FDCWD, \"/tools/lib/\", {st_mode=S_IFDIR|0775, st_size=4096, ...}, 0) = 0\nopenat(AT_FDCWD, \"/etc/ld.so.cache\", O_RDONLY|O_CLOEXEC) = 4\nfstat(4, {st_mode=S_IFREG|0644, st_size=5067, ...}) = 0\nmmap(NULL, 5067, PROT_READ, MAP_PRIVATE, 4, 0) = 0x7f6860fd1000\nclose(4)                                = 0\nopenat(AT_FDCWD, \"/lib/x86_64-linux-gnu/libc.so.6\", O_RDONLY|O_CLOEXEC) = 4\nread(4, \"\\177ELF\\2\\1\\1\\3\\0\\0\\0\\0\\0\\0\\0\\0\\3\\0>\\0\\1\\0\\0\\0\\220\\243\\2\\0\\0\\0\\0\\0\"..., 832) = 832\npread64(4, \"\\6\\0\\0\\0\\4\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0\"..., 784, 64) = 784\nfstat(4, {st_mode=S_IFREG|0755, st_size=2129424, ...}) = 0\npread64(4, \"\\6\\0\\0\\0\\4\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0@\\0\\0\\0\\0\\0\\0\\0\"..., 784, 64) = 784\nmmap(NULL, 2174352, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 4, 0) = 0x7f6860dbe000\nmmap(0x7f6860de6000, 1609728, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x28000) = 0x7f6860de6000\nmmap(0x7f6860f6f000, 323584, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x1b1000) = 0x7f6860f6f000\nmmap(0x7f6860fbe000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 4, 0x1ff000) = 0x7f6860fbe000\nmmap(0x7f6860fc4000, 52624, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f6860fc4000\nclose(4)                                = 0\nmmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f6860dbb000\narch_prctl(ARCH_SET_FS, 0x7f6860dbb740) = 0\nset_tid_address(0x7f6860dbba10)         = 29\nset_robust_list(0x7f6860dbba20, 24)     = 0\nrseq(0x7f6860dbc060, 0x20, 0, 0x53053053) = 0\nmprotect(0x7f6860fbe000, 16384, PROT_READ) = 0\nmprotect(0x5f81a6fcb000, 4096, PROT_READ) = 0\nmprotect(0x7f686100b000, 8192, PROT_READ) = 0\nprlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0\nmunmap(0x7f6860fd1000, 5067)            = 0\nexit_group(0)                           = ?\n+++ exited with 0 +++\n",
  "passed": true
}
```

### Full current limits proof

Decoded from the chain-verified normal stream, not inherited:

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
998 343 0:57 / / ro,relatime - overlay overlay rw,lowerdir=/home/orgasmic/.local/share/containers/storage/overlay/l/NP42E625VWWJYXWBFHJBCAONY6,upperdir=/home/orgasmic/.local/share/containers/storage/overlay/be4301218a6d3f4ff6c40fedcddc7361ba70babe4a196b8442146473126951b9/diff,workdir=/home/orgasmic/.local/share/containers/storage/overlay/be4301218a6d3f4ff6c40fedcddc7361ba70babe4a196b8442146473126951b9/work,redirect_dir=nofollow,uuid=on,userxattr
1001 998 0:65 / /work rw,nosuid,nodev,relatime - tmpfs tmpfs rw,size=1638400k,uid=1002,gid=1002,inode64
1000 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /harness ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1003 998 0:66 / /tmp rw,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=131072k,uid=1002,gid=1002,inode64
1002 998 0:67 / /sys ro,nosuid,nodev,noexec,relatime - sysfs sysfs ro
1004 998 0:68 / /dev ro,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,uid=1002,gid=1002,inode64
1005 998 0:69 / /proc rw,nosuid,nodev,noexec,relatime - proc proc rw
1006 1004 0:70 / /dev/pts rw,nosuid,noexec,relatime - devpts devpts rw,gid=231076,mode=620,ptmxmode=666
1007 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /tools/lib ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1008 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/new-tree.json /inputs/new-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1009 998 0:46 /containers/overlay-containers/824dda38ac87f6ed66d15b174394cb44975ab40ef6abbb4344398fdefcb1c872/userdata/.containerenv /run/.containerenv ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1010 1004 0:49 / /dev/shm ro,nosuid,nodev,noexec,relatime - tmpfs shm rw,size=16384k,uid=1002,gid=1002,inode64
1011 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final /inputs/new ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1012 998 0:46 /containers/overlay-containers/824dda38ac87f6ed66d15b174394cb44975ab40ef6abbb4344398fdefcb1c872/userdata/hosts /etc/hosts ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1013 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/input-manifest.json /inputs/input-manifest.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1014 998 0:46 /containers/overlay-containers/824dda38ac87f6ed66d15b174394cb44975ab40ef6abbb4344398fdefcb1c872/userdata/hostname /etc/hostname ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1015 998 252:1 /usr/bin/strace /tools/strace ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1016 1004 0:63 / /dev/mqueue rw,nosuid,nodev,noexec,relatime - mqueue mqueue rw
1017 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/old-tree.json /inputs/old-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1018 1002 0:29 / /sys/fs/cgroup ro,nosuid,nodev,noexec,relatime - cgroup2 cgroup rw,nsdelegate,memory_recursiveprot
1019 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /runtime/bun-old/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1020 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /inputs/old/node_modules ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1021 998 252:1 /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /runtime/bun-new/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1022 998 252:1 /opt/node-v22.23.3-linux-x64/bin/node /runtime/node/node ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1023 1004 0:5 /null /dev/null rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1024 1004 0:5 /random /dev/random rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1025 1004 0:5 /full /dev/full rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1026 1004 0:5 /tty /dev/tty rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1027 1004 0:5 /zero /dev/zero rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1028 1004 0:5 /urandom /dev/urandom rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
344 1005 0:69 /bus /proc/bus ro,nosuid,nodev,noexec,relatime - proc proc rw
345 1005 0:69 /fs /proc/fs ro,nosuid,nodev,noexec,relatime - proc proc rw
349 1005 0:69 /irq /proc/irq ro,nosuid,nodev,noexec,relatime - proc proc rw
350 1005 0:69 /sys /proc/sys ro,nosuid,nodev,noexec,relatime - proc proc rw
351 1005 0:69 /sysrq-trigger /proc/sysrq-trigger ro,nosuid,nodev,noexec,relatime - proc proc rw
375 1005 0:71 / /proc/acpi ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
400 1005 0:5 /null /proc/kcore rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
445 1005 0:5 /null /proc/keys rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
447 1005 0:5 /null /proc/latency_stats rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
449 1005 0:5 /null /proc/timer_list rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
453 1005 0:75 / /proc/scsi ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
549 1002 0:76 / /sys/firmware ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
554 1002 0:77 / /sys/dev/block ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo:       0       0    0    0    0     0          0         0        0       0    0    0    0     0       0          0
```

### Full selected normal HostConfig

```json
{
  "Binds": [
    "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/input-manifest.json:/inputs/input-manifest.json:ro,rprivate,rbind",
    "/opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro,rprivate,rbind",
    "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro,rprivate,rbind",
    "/usr/bin/strace:/tools/strace:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro,rprivate,rbind",
    "/srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r3-final:/inputs/new:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/old-tree.json:/inputs/old-tree.json:ro,rprivate,rbind",
    "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67DABP7KS1CNCSB0SPAW9BW/evidence/new-tree.json:/inputs/new-tree.json:ro,rprivate,rbind"
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

Full inspections: `container-inspect.json` (normal `824dda38ac87f6ed66d15b174394cb44975ab40ef6abbb4344398fdefcb1c872`, exit 0),
`capture-kill-container-inspect.json` (kill `31b863ba30391d25292ca5c2dd6d9e92a65ef10da7c3df8f2d8be5c6ba1d1b1f`, exit 137), and
`deadline-negative/container-inspect.json` (otherwise normal negative, exit 0).
Normal: eight verified frames, one mock case and terminal. Kill: six verified
prefix frames, no case/final, exit 137, INCOMPLETE; prefix remains in
`capture-kill.stdout.jsonl`. No host closing record was added. Negative: eight
verified frames with a normal terminal, but INCOMPLETE specifically due to the
actual timeout drift.

Kernel proof remains: memory.max=4294967296, memory.swap.max=0,
cpu.max=200000/100000, pids.max=256, core soft/hard=0, five empty capability sets,
NoNewPrivs=1, Seccomp=2, only lo. Private PID/cgroup/IPC, network none, no ports,
read-only root/code. Writable /work=1600MiB + /tmp=128MiB; even counting read-only
/dev=64MiB and /dev/shm=16MiB, total 1808MiB ≤1920MiB. No unenforced limit observed.

### Valid inherited evidence / unchanged scope

Only unchanged image/build provenance is inherited from the prior round and
re-inspected now: `inherited-image-build-first-env.log`,
`inherited-image-build-empty-env-final.log`, `image-inspect-current.json`.
The first build's inherited-environment disclosure is preserved: it preceded the
binding env-i condition, used --pull=never and --network=none, and is NOT the
selected clean-build receipt. The selected final image was built with no RUN,
installs, downloads or runtime copies, under:

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C /usr/bin/podman build --pull=never --network=none --no-cache --http-proxy=false --cap-drop=ALL --security-opt=no-new-privileges -f test/store-probe/Containerfile -t localhost/f-host-2b-phase-p:task-mhqnz test/store-probe
```

Only HOME, PATH, XDG_RUNTIME_DIR, DBUS_SESSION_BUS_ADDRESS and LC_ALL were
preserved for rootless local Podman. No rootful/privileged/systemd-in-container,
home/credential/socket mount, auto-pull, network, host config/session/linger change
or cgroup relaxation. Existing rootless delegation sufficed; no new systemd-run
scope was needed. All oracle/cache/driver/runtime hashes were freshly rechecked;
no earlier result is treated as old-reader compatibility evidence.

## Unmet Criteria

No requested revision-3 preparation item is known unmet. Phase E is intentionally
not run: zero native experiment cases, no storage-package imports or store
operations, no exposure/rotation/refusal/compatibility conclusion. **(b) remains
pending the A+B+D integration tarball with dIntegrationPin null.**

## Residual Risk

- /bin/true smoke proves the basic tracer loader, default-seccomp ptrace and fd 3
  transport only; real workers, ABI/NAPI, Bun source resolution and store/API
  instrumentation remain unexecuted. Failures stop the native run as inconclusive,
  never justify privilege/ptrace/seccomp relaxation.
- Filter counters are data-tested but no real store tracing/correlation happened.
  Any dropped lines conservatively keep the affected case uncertain. Writable mmap
  and restored-byte writes still require full syscall/API review; final hashes
  alone cannot prove no write. RefusalBeforeMutation remains null pending review.
- The chain/receipts prove integrity, truncation and declared capture completeness,
  NOT that the intended experiment happened, NOT authenticity against the host,
  and NOT owner consent. --phase-e-approved confers no authority; no optional
  replay nonce guard was added. Owner approval and operational go remain external.
- Aggregate deadline/stdout cap can yield a valid prefix but INCOMPLETE run. No
  no-fence decision, D enablement, deployment, rollback or real-store operation is
  authorized. **Stop here for external exact-manifest OK.**
