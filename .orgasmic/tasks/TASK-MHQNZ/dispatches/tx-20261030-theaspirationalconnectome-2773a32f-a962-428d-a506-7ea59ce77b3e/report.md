# TASK-MHQNZ — Phase P revision 4

## Changed

Local commit **bac2ebb2e9f634a877bb7422ff5542160cbc08dc**, directly on
**e3f07c16a3983431b096d747477fda8d20bf3c38**. Vasyl noreply identity and
GPT-6.1-Sol trailer retained. Ten harness files changed; no product, dependency,
lockfile, Containerfile or image change. No operator manifest/private host paths
are committed. Operator artifacts exist only in this evidence directory.

Read the delta review in full; SHA256:
`7d71340338190cffc5570a0f84ca62abe4a9eccdb80a0626683d5446fadbee1b`
(`delta-review.md`, source `/srv/review-drop/TASK-MHQNZ-R3-DELTA-REVIEW.md`).

**Prep only:** no Phase E, no store creation/open, no AF/CM/Chronicle/SDK import.
Code preparation hashes/unpacks code only; mock records are labeled mock. The
smoke executes a tiny Node-stdlib-only marker control and `/bin/true`.

### Bounded delta by requested item

- **M-2:** `check-prep.py` requires a mandatory externally supplied
  `approved_sha256`. Before proceeding it compares manifest bytes to that anchor;
  it binds EVERY discovered host receipt to an explicit expected manifest and
  hash. Normal, kill and negative have explicit bindings; negative's approved
  manifest is a byte-identical copy. Both launcher and final dispatch gate received
  the same external value below, not a hash recomputed as a trusted CLI argument.
  Missing/wrong anchors refuse in a data-only selftest.
- **M-3:** smoke and `probe.mjs runChild` share `productionTransport` and
  `productionStdio`. The exact script is `"$@" 3>&1 1>&5 | /bin/cat`, stdio is
  `[ignore,pipe,pipe,ignore,pipe,pipe]`. Gate pins transportArgv to the shared helper
  and script. Separate assertions prove bash fd1 trace contains successful
  `/bin/true` execve, fd5 stdout equals `PROBE_SMOKE_STDOUT_v1\n`, fd4 API equals
  the marker JSON event, and neither marker appears in trace. All four assertions
  passed in the unchanged no-op envelope. Full trace/channel bytes are streamed.
- **M-1:** stream checker cross-checks inspect Cmd/Image/User, resource limits,
  namespaces, read-only root, capabilities/security, ports/core, RO binds and
  tmpfs against recorded argv. Cmd/Image/Memory mismatch selftest refuses. Inspect
  is saved before checker invocation. **The launcher is trusted; recorded argv is
  its self-attestation**, not protection against a modified host/launcher. Inspect
  does not independently attest the host timeout. No attestation/auth service.
- **M-4:** explicit conservative documentation: filtering at **≥192 MiB with ANY
  drop makes the run terminal INCOMPLETE**. It cannot rescue qualification.
  **The budget must not be approached.** No reserve architecture/per-case budgets
  were added; cap remains 256 MiB, terminal reserve 4096 bytes, frame cap 1 MiB.
- **LOW-2:** retain regex now catches `newfstatat` and related `*at` access/chmod/
  chown/utime variants; one data-only variant selftest added.

### Selected identities

- Harness aggregate (`SHA256(harness.SHA256SUMS)`, 19 files):
  `015a6aea66876016b5293af5512c1c53b70f6c09c5a7d3e198c4a2870929aa80`.
- Input manifest / external approval identity: `d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea`.
- Image ID: `5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6`.
- OCI manifest digest: `sha256:44b688151db6337cfb11114ea43a644311251e7d87c858dc8f252bc906b4a3dd`.
- Unchanged local Ubuntu 24.04 base ID: `6277ab668b2ad3c3ab851d370faa1690d324b30db29d342527f61fed28f20f36`.

### Inherited evidence (unchanged scope only)

The image/Containerfile are unchanged; `image-inspect-current.json` verifies the
selected image. No earlier gate pass is reused. Only image-build provenance is
inherited: `inherited-image-build-first-env.log` and
`inherited-image-build-empty-env-final.log`.

**Disclosure:** the first historical build inherited the host environment (before
that extra condition arrived), despite pull-never/network-none. Its image
`d614334ab9680804a227546ff29b7cb36ac1aa06eeda6332ff1c2fccebd9c945` is NOT selected.
An intermediate cap-drop mkdir build failed; the selected clean build uses the
final no-RUN Containerfile and empty environment. No exposure or rotation claims
are made. Clean-build command:

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C /usr/bin/podman build --pull=never --network=none --no-cache --http-proxy=false --cap-drop=ALL --security-opt=no-new-privileges -f test/store-probe/Containerfile -t localhost/f-host-2b-phase-p:task-mhqnz test/store-probe
```

Only HOME/PATH/XDG_RUNTIME_DIR/DBUS_SESSION_BUS_ADDRESS/LC_ALL are retained for
rootless local Podman. No apt/npm/install/hooks/downloads, host configuration,
linger/session changes, systemd-in-container, privilege or seccomp relaxation.
No rebuild was necessary this round. No public dependency was missing.

## Verification Gates

All four assigned gates ran at **clean committed HEAD bac2ebb2**; exit 0 and
reusable receipts. Earlier dirty development passes are not the selected gates.
`check-prep` passed 18 Node + 6 Python data/mock tests, syntax, external anchors,
all input hashes/modes, no private paths, smoke channels, receipts, kernel limits
and HostConfig. Product-wide tests were not assigned.

- `phase-p-r4-noop-committed`: exit 0; `gate-phase-p-r4-noop-committed-01M67FBDVS5YYCWGD032XPC52Q.log`.

- `phase-p-r4-kill-committed`: exit 0; `gate-phase-p-r4-kill-committed-01M67FBRMYK512E8HTM7V67P1E.log`.

- `phase-p-r4-deadline-negative-committed`: exit 0; `gate-phase-p-r4-deadline-negative-committed-01M67FBKDVCPMFQPPR8ECVYJK5.log`.

- `phase-p-r4-check-prep-committed`: exit 0; `gate-phase-p-r4-check-prep-committed-01M67FBST5M5DRPKX9RQ47VRBW.log`.

### Exact dispatch gate invocations

The following shell values are explicit argument abbreviations, not defaults;
all file paths below are operator evidence only.

```sh
orgasmic dispatch gate phase-p-r4-noop-committed -- test/store-probe/launch.sh proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r4-noop /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea native
orgasmic dispatch gate phase-p-r4-deadline-negative-committed -- python3 test/store-probe/deadline-negative.py proof 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r4-deadline /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/deadline-negative d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea native
orgasmic dispatch gate phase-p-r4-kill-committed -- test/store-probe/launch.sh capture-kill 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 f-host-2b-task-mhqnz-r4-kill /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /opt/node-v22.23.3-linux-x64/bin/node /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /usr/bin/strace /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea native
orgasmic dispatch gate phase-p-r4-check-prep-committed -- python3 test/store-probe/check-prep.py /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea
```

### No-op / kill / negative results

| Control | Container exit | Host launcher / gate | Checker | Verified prefix |
|---|---:|---|---|---|
| Normal mock/no-op + smoke | 0 | 0 / 0 | COMPLETE | 8 records, one expected mock case, normal final |
| Forced SIGKILL after flushed mock inventory | 137 | 0 / 0 (expected negative) | INCOMPLETE | 6 records, no terminal; original bytes preserved |
| Actual timeout changed to 600s/10s | 0 | 2 / 0 (expected refusal) | INCOMPLETE | 8 records, normal terminal/full mock case; refused solely for deadline drift |

Every receipt's `manifestSha256` is the external approved hash. Normal and kill
receipt deadlines parsed from recorded actual argv are 1800s/30s; negative is
600s/10s, compared against approved manifest.capture 1800s/30s. Each receipt hashes
its own argv, stdout and stderr. All three have non-null inspectedContainer in
checker output, so inspect corroboration executed, including the refusal path.
Host never synthesized a closing frame after SIGKILL.

**The unkeyed chain proves integrity/truncation, not that the intended experiment
happened, compatibility, exposure, rotation, mutation safety or consent.** COMPLETE
here means a complete mock transport/limits control, never a native-format result.

### Whole-command deadline and envelope

1800s plus 30s termination grace comes solely from runtime.mjs. It budgets the
doubled Node/Bun matrix of 18 cases / up to 42 child operations without summing all
90s+5s child worst cases. Any aggregate timeout is INCOMPLETE. The deadline-negative
is the deliberately altered 600s+10s control, not an authorized Phase-E deadline.

Rootless orgasmic uid1002; nonroot container1000:1000, 4GiB memory, additional swap0,
2CPU, pids256, core0; read-only root/code, cap-dropALL, NNP/default seccomp;
PID/cgroup/IPC private; network none/no host ports. All 11 input binds RO, no host
RW mounts, home/credentials/sockets. Host HOME in env only is not a home mount.
No cgroup-delegation workaround/systemd scope or host changes were needed.
/work1600 + /tmp128 + runtime /dev64 + /dev/shm16 =1808MiB ≤1920MiB (the latter
two RO). All Bun homes/caches reside in /work; no install or network.

Stdout/stderr are redirected directly to evidence outside the container, not
recovered after tmpfs disappears. Frame cap/output cap handling remains fail-closed.
Full kernel mount list, interfaces and security proof follow; independent full
HostConfig artifacts are `container-inspect.json`,
`capture-kill-container-inspect.json`, and `deadline-negative/container-inspect.json`.

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
998 343 0:62 / / ro,relatime - overlay overlay rw,lowerdir=/home/orgasmic/.local/share/containers/storage/overlay/l/NP42E625VWWJYXWBFHJBCAONY6,upperdir=/home/orgasmic/.local/share/containers/storage/overlay/af231831b981c9f4f9901ba24be25272fcdae8e483e9234166b2e2ede175aa6f/diff,workdir=/home/orgasmic/.local/share/containers/storage/overlay/af231831b981c9f4f9901ba24be25272fcdae8e483e9234166b2e2ede175aa6f/work,redirect_dir=nofollow,uuid=on,userxattr
1000 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /harness ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1002 998 0:65 / /tmp rw,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=131072k,uid=1002,gid=1002,inode64
1003 998 0:66 / /work rw,nosuid,nodev,relatime - tmpfs tmpfs rw,size=1638400k,uid=1002,gid=1002,inode64
1001 998 0:67 / /sys ro,nosuid,nodev,noexec,relatime - sysfs sysfs ro
1004 998 0:68 / /dev ro,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,uid=1002,gid=1002,inode64
1005 998 0:69 / /proc rw,nosuid,nodev,noexec,relatime - proc proc rw
1006 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/old-tree.json /inputs/old-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1007 1004 0:70 / /dev/pts rw,nosuid,noexec,relatime - devpts devpts rw,gid=231076,mode=620,ptmxmode=666
1008 998 252:1 /usr/bin/strace /tools/strace ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1009 998 0:46 /containers/overlay-containers/a52d0aef60df144fd068b5f80845517390b5ee71ac400dfbb3a0cc392a43c349/userdata/hostname /etc/hostname ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1010 998 0:46 /containers/overlay-containers/a52d0aef60df144fd068b5f80845517390b5ee71ac400dfbb3a0cc392a43c349/userdata/.containerenv /run/.containerenv ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1011 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4 /inputs/new ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1012 1004 0:57 / /dev/shm ro,nosuid,nodev,noexec,relatime - tmpfs shm rw,size=16384k,uid=1002,gid=1002,inode64
1013 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /tools/lib ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1014 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/input-manifest.json /inputs/input-manifest.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1015 998 0:46 /containers/overlay-containers/a52d0aef60df144fd068b5f80845517390b5ee71ac400dfbb3a0cc392a43c349/userdata/hosts /etc/hosts ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1016 1004 0:61 / /dev/mqueue rw,nosuid,nodev,noexec,relatime - mqueue mqueue rw
1017 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/new-tree.json /inputs/new-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1018 1001 0:29 / /sys/fs/cgroup ro,nosuid,nodev,noexec,relatime - cgroup2 cgroup rw,nsdelegate,memory_recursiveprot
1019 998 252:1 /srv/ci-fixtures/bun-1.4.4-linux-x64/bun /runtime/bun-new/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1020 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun /runtime/bun-old/bun ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1021 998 252:1 /opt/node-v22.23.3-linux-x64/bin/node /runtime/node/node ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1022 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /inputs/old/node_modules ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
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
549 1001 0:76 / /sys/firmware ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
554 1001 0:77 / /sys/dev/block ro,relatime - tmpfs tmpfs ro,uid=1002,gid=1002,inode64
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
    lo:       0       0    0    0    0     0          0         0        0       0    0    0    0     0       0          0
```

### Exact production-shape smoke (normal control)

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
  "trace": "29    execve(\"/runtime/node/node\", [\"/runtime/node/node\", \"/harness/transport-control.mjs\"], 0x7ffe887f4680 /* 14 vars */) = 0\n36    execve(\"/bin/true\", [\"/bin/true\"], 0x17bb4de0 /* 14 vars */) = 0\n36    +++ exited with 0 +++\n29    --- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=36, si_uid=1000, si_status=0, si_utime=0, si_stime=0} ---\n34    +++ exited with 0 +++\n31    +++ exited with 0 +++\n32    +++ exited with 0 +++\n30    +++ exited with 0 +++\n33    +++ exited with 0 +++\n35    +++ exited with 0 +++\n29    +++ exited with 0 +++\n",
  "checks": {
    "traceHasTrueExec": true,
    "stdoutMarkerOnly": true,
    "apiMarkerOnly": true,
    "traceHasNoMarkers": true
  },
  "passed": true
}
```

### Full recorded launch argv (shell escaped; JSON arrays are authoritative)

### `limits-proof-argv.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r4-noop --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs
```

### `capture-kill-argv.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r4-kill --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs --mock-kill
```

### `deadline-negative/limits-proof-argv.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=10s 600s podman run --name f-host-2b-task-mhqnz-r4-deadline --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/deadline-negative/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/deadline-negative/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/deadline-negative/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/noop.mjs
```

### `execute-argv-NOT-RUN.txt`

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 1800s podman run --name f-host-2b-task-mhqnz-r4-noop --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env PROBE_MANIFEST_SHA256=d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --env BUN_INSTALL=/work/bun --env BUN_INSTALL_CACHE_DIR=/work/bun-install-cache --env BUN_RUNTIME_TRANSPILER_CACHE_PATH=/work/bun-transpiler-cache --env XDG_CACHE_HOME=/work/cache --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-r4:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/bin/bun:/runtime/bun-old/bun:ro --volume /srv/ci-fixtures/bun-1.4.4-linux-x64/bun:/runtime/bun-new/bun:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/probe.mjs --phase-e-approved --section native
```

The last argv is **prospective only, NOT RUN**. `--phase-e-approved` is a purpose marker, NOT an authority boundary. Owner authority and operational go remain external; no signing/approval/replay service was added.

### Host and checker receipts

### `limits-proof-host-receipt.json`

```json
{
  "kind": "proof",
  "exitStatus": 0,
  "started": "1793399830508000598",
  "finished": "1793399831480422671",
  "elapsedSeconds": 0.972422073,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/limits-proof-argv.json",
  "argvSha256": "9be7df6e884647269eebc5abce23a094c3c681e963f9a33f1c93445ec73090d1",
  "manifestSha256": "d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/limits-proof.stdout.jsonl",
    "size": 12137,
    "sha256": "2b3ca32a92fe383cea2aa9b368b8395e42e7ebff0480221ad71419c94aace47b"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/limits-proof.stderr",
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
  "verifiedBytes": 12137,
  "verifiedChain": "a1679e5c86e39300e28ce2708a7501fff4599cde4785f79b08d1ca31e2645dc0",
  "caseIds": [
    "mock/noop"
  ],
  "inspectedContainer": "a52d0aef60df144fd068b5f80845517390b5ee71ac400dfbb3a0cc392a43c349",
  "final": {
    "expectedCaseCount": 1,
    "completedCaseCount": 1,
    "status": "complete",
    "totalCount": 8,
    "dataChainHash": "5e006fcc3583e2d79ae8016b41f0d2302ed9380884651d1cbf74a3d70a710545",
    "bytesBeforeFinal": 11665,
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
  "started": "1793399841508433993",
  "finished": "1793399842382051138",
  "elapsedSeconds": 0.873617145,
  "deadlineSeconds": 1800,
  "killAfterSeconds": 30,
  "timeoutReported": false,
  "sigkillExit": true,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/capture-kill-argv.json",
  "argvSha256": "a9e71fee9b9b026ee73ac0a824ea77dc4c46ca41dbc5741e9f4d169baff37d2d",
  "manifestSha256": "d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/capture-kill.stdout.jsonl",
    "size": 11322,
    "sha256": "3428631ec123eca8b5589009f5453d68254073568fa716bfd6a1744d785996a9"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/capture-kill.stderr",
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
  "verifiedBytes": 11322,
  "verifiedChain": "f9979d7feb50bf473818aa5c3fb894178e13d4a0779ca6ff342d427c8dd1621b",
  "caseIds": [],
  "inspectedContainer": "3c8a90f73c56ed2cf5f3a624841e520d9100a210cb860053618d5315cedabf09",
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
  "started": "1793399836214426143",
  "finished": "1793399836906853526",
  "elapsedSeconds": 0.692427383,
  "deadlineSeconds": 600,
  "killAfterSeconds": 10,
  "timeoutReported": false,
  "sigkillExit": false,
  "argvPath": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/deadline-negative/limits-proof-argv.json",
  "argvSha256": "3d5c1133f406683cbb2fd41714a1513bab503b1dece4155fcf4bd5731f2b0b30",
  "manifestSha256": "d9b0eea04c176b9d3d01378418d91705772a945f21c85af562daab01946432ea",
  "stdout": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/deadline-negative/limits-proof.stdout.jsonl",
    "size": 12190,
    "sha256": "e7c646f17271c7d2df853aada35bc397d813781bb819ab023c7ab5e1c23baf3d"
  },
  "stderr": {
    "path": "/home/orgasmic/.orgasmic/runs/run-01M67ETEA7QWVQJVGY66ZMVC3J/evidence/deadline-negative/limits-proof.stderr",
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
    "verifiedBytes": 12190,
    "verifiedChain": "949f0e060aa8af17a478446b3a7782c83cdf79b1f5a6bcf1d398bd57128ce8ae",
    "caseIds": [
      "mock/noop"
    ],
    "inspectedContainer": "f0e7125a3dcaf3a2f8b6c7e787e6aca95c21515be933a5ca45d01222322208c9",
    "final": {
      "expectedCaseCount": 1,
      "completedCaseCount": 1,
      "status": "complete",
      "totalCount": 8,
      "dataChainHash": "bef053bd9c0547ace0088eab92350f0d3213460ead753cd4de8562868d809a58",
      "bytesBeforeFinal": 11718,
      "cap": 268435456
    },
    "error": "Error: INCOMPLETE: deadline differs from approved manifest execution intent",
    "meaning": "Integrity/truncation only; does NOT prove the intended experiment happened or compatibility."
  },
  "outcome": "PASS: drifted actual argv refused despite a normal, intact mock capture"
}
```

### Runtime × route declarations (NOT resolution/import results)

Node rows are a **native-format component probe, not actual-old-reader proof**.
Frozen old reader is Bun1.4.2/source condition; new Bun1.4.4 is distinct. These are
manifest export declarations verified as bytes only; actual imports await Phase E.
Old→old uses old executable/entry; new→new uses new; old→new seeds with new and
attempts with old. Every runtime/route plans those three distinct fresh-copy cases.

| Runtime/tree | Route/condition | Executable | Entry / SHA256 |
|---|---|---|---|

| node/old | chronicle/import | `/runtime/node/node` | `/inputs/old/node_modules/@animalabs/chronicle/index.js` / `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |

| node/old | cm/import | `/runtime/node/node` | `/inputs/old/node_modules/@animalabs/context-manager/dist/src/index.js` / `05c4b50d7cdd72f4ce8b9919b6680d363497757bbe096606eac3fe78a0f4166c` |

| node/old | af/import | `/runtime/node/node` | `/inputs/old/node_modules/@animalabs/agent-framework/dist/src/index.js` / `4621b06d07da7a638b03a97e04dfd447bceb744daeeef32fd3ccf82d30a02810` |

| node/new | chronicle/import | `/runtime/node/node` | `/inputs/new/node_modules/@animalabs/chronicle/index.js` / `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |

| node/new | cm/import | `/runtime/node/node` | `/inputs/new/node_modules/@animalabs/context-manager/dist/src/index.js` / `42496c855b540c66746df2653e459dc8b1de9d876d6f74fd9a72b0b9541adc17` |

| node/new | af/import | `/runtime/node/node` | `/inputs/new/node_modules/@animalabs/agent-framework/dist/src/index.js` / `50a7a8cb245004ef0b311d17208ef6086d7ef3b3f084954243633cf10391427a` |

| bun/old | chronicle/bun | `/runtime/bun-old/bun` | `/inputs/old/node_modules/@animalabs/chronicle/index.js` / `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |

| bun/old | cm/bun | `/runtime/bun-old/bun` | `/inputs/old/node_modules/@animalabs/context-manager/src/index.ts` / `1582e6e315bbdc3fe53ed2bf83d3e8210ce957605d11becb38fc4b82d1fcccbc` |

| bun/old | af/bun | `/runtime/bun-old/bun` | `/inputs/old/node_modules/@animalabs/agent-framework/src/index.ts` / `19fa4aa213eb137d6264676bad5a3e9e27b6f90a6794a7f73dd4f1beb531bed8` |

| bun/new | chronicle/bun | `/runtime/bun-new/bun` | `/inputs/new/node_modules/@animalabs/chronicle/index.js` / `7fb5de87fc84694d07dda6178ad1cc82a7e92cbc14deb547be691af4100886d1` |

| bun/new | cm/bun | `/runtime/bun-new/bun` | `/inputs/new/node_modules/@animalabs/context-manager/src/index.ts` / `6b1cb6b25f20f06f9b7f7c47c3e6705f8adf285a292f27b8673584209934e0e2` |

| bun/new | af/bun | `/runtime/bun-new/bun` | `/inputs/new/node_modules/@animalabs/agent-framework/src/index.ts` / `8a5c3eebea3887b74f03dce4afc62db3b852c0b8975f1be9903fd513b96b044f` |


## Unmet Criteria

None for this bounded Phase-P revision. Phase E is deliberately NOT RUN and no
native compatibility/refusal/exposure/mutation verdict is made. Section (b) is
**pending the A+B+D integration tarball**, `dIntegrationPin: null`. AF0.30.0 is
only a CM/Chronicle driver and is NOT D; it is not substituted for that dependency.
No new pin, floor claim or authorization is inferred.

## Residual Risk

### Known limits left unchanged (report only, as directed)

- **LOW-1:** one environment/instrumentation/ABI/loader/ptrace failure aborts the
  native matrix; remaining cases are not collected. Diagnostic yield is sacrificed,
  never converted to PASS.
- **LOW-3:** expected command remains a literal in the hash-bound checker rather
  than a manifest.capture.command field; reviewability is limited.
- **LOW-4:** every one of up to42 traced workers rehashes ~10MB of critical inputs
  and parses the manifest, adding overhead and trace volume before store operations.
- **LOW-5:** timeout parser selects the first timeout element; a modified launcher
  can supply a decoy. This is inside the trusted-host/launcher boundary, not an
  authenticated host attestation.

Filter activation ≥192MiB with any drop is terminal INCOMPLETE; no new reserve
architecture was added and the budget must not be approached. The high-volume
path was not exercised end to end in prep (counters/filter decision are data-tested).
The smoke proves FD transport under the envelope, not actual Bun/SDK/native/store
ABI or runtime resolution. Bash/cat come from the pinned image. Owner consent is
external, and neither argv, inspect, manifest SHA nor chain authenticates it.
No permissions/ptrace/seccomp relaxation is available if Phase E fails.
