# TASK-MHQNZ — F-host.2b Phase P only

## Changed

Local commit `75d1d4d5e89d7b5efbbd39afc6416b778cf05a92` (clean worktree), Vasyl Dovhanchyn noreply identity,
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`. No push, landing, merge or
main-checkout edits. Nine test-only files under `test/store-probe/`:
Containerfile, README, prepare.py, launch.sh, probe.mjs, worker.mjs,
check-prep.py, selftest.mjs, selftest.py. No product/dependency/lockfile changes;
no changelog needed for test-only changes. Supplied project baseline read;
conventions/gotchas explicitly absent. Read §5.1 and the complete public old-reader
fixture at `90fb9ef6`; all old hashes match that baseline, not the stale npm lock.

The native harness defines independent Chronicle, CM and real AF.create routes;
old→old, new→new, old-reader→new-store on fresh writable generated clones;
closed/reopened/sealed masters; separate scratch homes; API/native syscall traces;
before/after names, SHA256, sizes, modes, identities and timestamps. Mutation/refusal
claims remain conservative and require trace review; errors are not passes.

**NO store has been created, opened, read or written. Phase E has NOT run.**
No provider/connector/endpoint was contacted. Only package metadata/code was read
and unpacked, and only the no-op shell was started inside the proof container.

### Frozen identities (approval scope)

- Harness SHA256 (SHA256 of bytewise-sorted `harness.SHA256SUMS`): `3d8c6d6b89e075c18e07eadf411c185aa0e729b1a14a8ff3ae42e23130b0aaa0`.
- Operator input manifest: `/home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/input-manifest.json`.
- Input-manifest SHA256: **`89fd0574adb7978587e9a1f2e7a0719a6927791a546be4e90aeeb82b30fb5fd0`**.
- Manifest enumerates 17945 input file/link records,
  including all old runtime dependencies, all sealed-cache files/tarballs,
  the separately supplied AF driver tarball, 45 exactly lock-resolved new
  packages, all harness files, runtime/tracer/libraries and assertion files.
  Symlink records hash the link target text; public runtime-root assertions reject
  symlinks/special files as specified. Image identities below are SHA256 content IDs.
- New native inputs: CM **0.24.0** tarball `ee1e7682c09a26b9db0cef45c51467df654f8f7f6a91f8fd843caf697097cf24`;
  Chronicle **0.5.1** `fd4aa39ede74308d251349ac639ff92eafd0bd8fdfbacfd92382ad384ea5c50a`;
  AF **0.30.0** driver `f8d1d65f07a00745d05dcb9bcbd5e5c65d8dfaccbb617de9b4e01b977f5a0989`.
  AF 0.30.0 is **NOT D**. Published tarballs do not embed a source gitHead;
  no source commit is invented. These are artifact-pinned native-format inputs,
  not the unprovided final integration release.
- Old: frozen patched AF 0.22.2 / CM 0.14.1 / Chronicle 0.4.0.
  Public fixture SHA256 `8f70b5cdab01eacdfb207698179c154ce171412ed417ef630fa8f4c18a06aeb5`;
  oracle artifact-manifest SHA256 `8153e7b84442cc11d94ace5c108ea05825ea0a8902b508056288154bedde00b6`.
- Image ID: `5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6`.
- Image OCI manifest digest: `sha256:44b688151db6337cfb11114ea43a644311251e7d87c858dc8f252bc906b4a3dd`.
- Only base: local `docker.io/library/ubuntu:24.04`, ID `6277ab668b2ad3c3ab851d370faa1690d324b30db29d342527f61fed28f20f36`;
  base repository digests: `docker.io/library/ubuntu@sha256:68ee22004a9109b44973313b02ff7b1de250f1de0f5ecb628d73f579aaff33ac`, `docker.io/library/ubuntu@sha256:a2d43bffc46670d7108bb3c0468c67c5557c4c92e83e1e34cbff1322ad1b94dc`.
- Runtime: Node `v22.23.3` Linux x64, mounted binary
  read-only from `/opt/node-v22.23.3-linux-x64/bin/node`, not copied into image.
  Strace plus four content-hashed library files are narrow read-only inputs;
  the library copies are in ignored build scratch, not the image or evidence.
  Base-layer static inspection found the Node/Chronicle shared dependencies.

### Offline image build — exact final argv

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C /usr/bin/podman build --pull=never --network=none --no-cache --http-proxy=false --cap-drop=ALL --security-opt=no-new-privileges -f test/store-probe/Containerfile -t localhost/f-host-2b-phase-p:task-mhqnz test/store-probe
```

Empty environment retains **only** HOME (rootless storage discovery),
PATH (local tools), XDG_RUNTIME_DIR and DBUS_SESSION_BUS_ADDRESS (existing
rootless user-service/cgroup access), LC_ALL=C (stable sorting/diagnostics).
No proxies, credentials or run token are passed. Container environment is only
image defaults plus the four explicit safe `--env` values in launch argv.
No apt/npm/package install, lifecycle hooks, downloads or RUN steps. No privileged
build/run and no host session/linger/Podman configuration changes. No systemd in
the container. No new systemd-run scope was needed: existing rootless delegation
worked and the container's own cgroup values prove enforcement.

Final build succeeded: `image-build-empty-env-final.log`; full identity receipt
`image-inspect-final.json`. Earlier superseded build receipt used inherited env
before the added condition arrived; a subsequent cap-drop-all mkdir build failed
with permission denied. The final metadata-only Containerfile eliminates RUN and
satisfies the stricter empty-environment condition. Both earlier logs are retained;
neither is the selected image receipt. Preparation also rejected a single-root
DefinitelyTyped archive layout on its first pass; the safe extractor now accepts
any one validated root while still refusing links/traversal. Final preparation
completed without missing inputs or networking (`preparation-final.log`).

### Exact no-op launch argv — actually run

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 900s podman run --name f-host-2b-task-mhqnz-proof-final --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /bin/sh -c printf\ \"memory.max=\"\;\ cat\ /sys/fs/cgroup/memory.max\;\ printf\ \"memory.swap.max=\"\;\ cat\ /sys/fs/cgroup/memory.swap.max\;\ printf\ \"cpu.max=\"\;\ cat\ /sys/fs/cgroup/cpu.max\;\ printf\ \"pids.max=\"\;\ cat\ /sys/fs/cgroup/pids.max\;\ id\;\ cat\ /proc/self/status\ \|\ grep\ -E\ \"NoNewPrivs\|Seccomp\|Cap\"\;\ cat\ /proc/self/limits\;\ cat\ /proc/self/mountinfo\;\ cat\ /proc/net/dev
```

Host command ran as `uid=1002(orgasmic)`, rootless Podman; container UID/GID
1000:1000. No whole-home/credential/socket mounts, host ports, rootful mode,
privilege, host PID/cgroup namespace, or automatic pulls. All nine bind inputs
are read-only; no whole evidence-directory mount. Narrow code/evidence paths
beneath a home directory are not a mount of that home.

**Whole-command deadline: 900 seconds, SIGTERM then SIGKILL after 30 seconds.**
The same budget is proposed for Phase E. Up to 21 native child operations each
have a 90-second deadline (+5-second kill grace); 900 seconds bounds the whole
matrix, not their sum. Any partial matrix/timeout is inconclusive, never a pass.
Writable file tmpfs caps: /work 1600 MiB + /tmp 128 MiB = 1728 MiB. Even counting
read-only runtime /dev (64 MiB) and /dev/shm (16 MiB), total is **1808 MiB ≤1920 MiB**.
No automatic writable /run and no writable host work volume. Rootfs is read-only.

### Prospective native Phase E argv — NOT RUN, NOT AUTHORIZED

After Soma's explicit OK of this exact manifest only. The retained stopped
no-op container currently owns this name; its inspect/kernel evidence is already
saved. Remove that stopped proof container before reusing the same name. No
execution is implicit in this report or in a successful prep gate.

```sh
env -i HOME=/home/orgasmic PATH=/usr/bin:/bin XDG_RUNTIME_DIR=/run/user/1002 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus LC_ALL=C timeout --kill-after=30s 900s podman run --name f-host-2b-task-mhqnz-proof-final --pull=never --network=none --memory=4g --memory-swap=4g --cpus=2 --pids-limit=256 --ulimit core=0 --read-only --read-only-tmpfs=false --tmpfs /work:rw\,nosuid\,nodev\,size=1600m\,mode=1777 --tmpfs /tmp:rw\,nosuid\,nodev\,noexec\,size=128m\,mode=1777 --shm-size=16m --user 1000:1000 --cap-drop=ALL --security-opt no-new-privileges --pid=private --cgroupns=private --ipc=private --workdir=/work --env HOME=/work --env LC_ALL=C --env PATH=/runtime/node:/usr/bin:/bin --env LD_LIBRARY_PATH=/tools/lib --volume /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-final:/inputs/new:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro --volume /opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro --volume /usr/bin/strace:/tools/strace:ro --volume /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/old-tree.json:/inputs/old-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/new-tree.json:/inputs/new-tree.json:ro --volume /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/input-manifest.json:/inputs/input-manifest.json:ro 5d9c60eec8a75261ab22ccc88b46d5d6605351c72b0ae642a56cef648b8a78e6 /runtime/node/node /harness/probe.mjs --phase-e-approved --section native
```

Phase E results would remain in bounded /work tmpfs for later `podman cp`;
no output host mount was granted. Native argv has only Node compiled routes.

## Verification Gates

At local commit `75d1d4d5e89d7b5efbbd39afc6416b778cf05a92`:

```sh
orgasmic dispatch gate phase-p-prep -- python3 test/store-probe/check-prep.py /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence
```

**PASS**, exit 0, reusable gate receipt. Log:
`/home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/gate-phase-p-prep-01M67BDTCGEAEXQYXQQ7W21E3Y.log`.
Five Node mock/data tests + four Python data/archive tests pass, zero failures.
Gate also checks syntax, absence of committed private host paths, all declared
input bytes/modes/sizes, exact harness file set, kernel limits and HostConfig.
No supplied storage module is imported by this gate. Worktree clean after commit.

Independent GNU `find -type f -print0 | LC_ALL=C sort -z` + `sha256sum` reproduces
all **14** public old-runtime-root inventory assertions; transcript
`old-inventory-independent.log`. Both lock hashes and installed package versions,
entry-point/native payload hashes, artifact manifest and entire transitive input
inventory were checked. All sealed cache SHA256SUMS passed before unpacking;
no package-manager reconstruction or hook ran. CM 0.24.0 source was rechecked at
its path/store config and open/openOn API (193–216, 518–580): no application-reader
floor argument. This is source evidence only, NOT a native compatibility result.

### Full no-op limits-proof stdout

Container `f184f96dc607bfaa27275477d9915e47ec354f817a97fac015aa4b023498abbd`; exit 0, stopped; stderr empty.
Final raw files: `limits-proof.stdout`, `limits-proof.stderr`, `limits-proof.exit`,
`container-inspect.json`, `proof-argv.txt`. An earlier no-op was rerun after the
final harness/manifest freeze; its superseded evidence is named `*proof1*`, and
that stopped container was removed. At no point did a compatibility probe run.

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
998 343 0:57 / / ro,relatime - overlay overlay rw,lowerdir=/home/orgasmic/.local/share/containers/storage/overlay/l/NP42E625VWWJYXWBFHJBCAONY6,upperdir=/home/orgasmic/.local/share/containers/storage/overlay/2ecf54b6dee93b3db020025edbec2263a68a78f9790a5460b249aee2b2724e51/diff,workdir=/home/orgasmic/.local/share/containers/storage/overlay/2ecf54b6dee93b3db020025edbec2263a68a78f9790a5460b249aee2b2724e51/work,redirect_dir=nofollow,uuid=on,userxattr
1000 998 0:65 / /sys ro,nosuid,nodev,noexec,relatime - sysfs sysfs ro
1002 998 0:66 / /work rw,nosuid,nodev,relatime - tmpfs tmpfs rw,size=1638400k,uid=1002,gid=1002,inode64
1003 998 0:67 / /tmp rw,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=131072k,uid=1002,gid=1002,inode64
1001 998 0:68 / /dev ro,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,uid=1002,gid=1002,inode64
1004 998 0:69 / /proc rw,nosuid,nodev,noexec,relatime - proc proc rw
1005 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe /harness ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1006 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/new-tree.json /inputs/new-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1007 998 252:1 /usr/bin/strace /tools/strace ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1008 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs /tools/lib ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1009 998 0:46 /containers/overlay-containers/f184f96dc607bfaa27275477d9915e47ec354f817a97fac015aa4b023498abbd/userdata/hosts /etc/hosts ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1010 998 252:1 /home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-final /inputs/new ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1011 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/old-tree.json /inputs/old-tree.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1012 998 252:1 /home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/input-manifest.json /inputs/input-manifest.json ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1013 1001 0:70 / /dev/pts rw,nosuid,noexec,relatime - devpts devpts rw,gid=231076,mode=620,ptmxmode=666
1014 1001 0:63 / /dev/mqueue rw,nosuid,nodev,noexec,relatime - mqueue mqueue rw
1015 998 0:46 /containers/overlay-containers/f184f96dc607bfaa27275477d9915e47ec354f817a97fac015aa4b023498abbd/userdata/.containerenv /run/.containerenv ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1016 1001 0:49 / /dev/shm ro,nosuid,nodev,noexec,relatime - tmpfs shm rw,size=16384k,uid=1002,gid=1002,inode64
1017 998 0:46 /containers/overlay-containers/f184f96dc607bfaa27275477d9915e47ec354f817a97fac015aa4b023498abbd/userdata/hostname /etc/hostname ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,size=9059720k,nr_inodes=2264930,mode=700,uid=1002,gid=1002,inode64
1018 998 252:1 /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules /inputs/old/node_modules ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1019 1000 0:29 / /sys/fs/cgroup ro,nosuid,nodev,noexec,relatime - cgroup2 cgroup rw,nsdelegate,memory_recursiveprot
1020 998 252:1 /opt/node-v22.23.3-linux-x64/bin/node /runtime/node/node ro,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1021 1001 0:5 /null /dev/null rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1022 1001 0:5 /random /dev/random rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1023 1001 0:5 /full /dev/full rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1024 1001 0:5 /tty /dev/tty rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1025 1001 0:5 /zero /dev/zero rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
1026 1001 0:5 /urandom /dev/urandom rw,nosuid,relatime master:2 - devtmpfs udev rw,size=45234728k,nr_inodes=11308682,mode=755,inode64
344 1004 0:69 /bus /proc/bus ro,nosuid,nodev,noexec,relatime - proc proc rw
345 1004 0:69 /fs /proc/fs ro,nosuid,nodev,noexec,relatime - proc proc rw
349 1004 0:69 /irq /proc/irq ro,nosuid,nodev,noexec,relatime - proc proc rw
350 1004 0:69 /sys /proc/sys ro,nosuid,nodev,noexec,relatime - proc proc rw
351 1004 0:69 /sysrq-trigger /proc/sysrq-trigger ro,nosuid,nodev,noexec,relatime - proc proc rw
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

### Full final HostConfig

```json
{
  "Binds": [
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/trace-libs:/tools/lib:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/old-tree.json:/inputs/old-tree.json:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/tmp-store-probe/new-final:/inputs/new:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-mhqnz/test/store-probe:/harness:ro,rprivate,rbind",
    "/opt/node-v22.23.3-linux-x64/bin/node:/runtime/node/node:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/new-tree.json:/inputs/new-tree.json:ro,rprivate,rbind",
    "/home/orgasmic/.orgasmic/runs/run-01M67ARH8PK8QF691QCJA440VW/evidence/input-manifest.json:/inputs/input-manifest.json:ro,rprivate,rbind",
    "/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/node_modules:/inputs/old/node_modules:ro,rprivate,rbind",
    "/usr/bin/strace:/tools/strace:ro,rprivate,rbind"
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

Rootless `podman-info.txt` and full `container-inspect.json` additionally retain
runtime/OS/kernel, mount RW flags, container user and network settings. Kernel
memory.max=4294967296, memory.swap.max=0, cpu.max=200000/100000, pids.max=256;
core soft/hard=0; all five capability sets zero; NoNewPrivs=1; Seccomp=2 with one
filter. Only lo is present. No unenforced limit was observed.

## Unmet Criteria

- **No Phase P isolation, image, manifest, hashing or native harness-preparation
  criterion is known unmet.** Behavioral compatibility is deliberately untested:
  Phase E requires Soma's OK on the exact manifest above.
- **(b) pending D integration tarball** — exactly as recorded in the manifest;
  `dIntegrationPin: null`. The D section is separately parameterized by
  `--af-tarball`, hashes that supplied artifact, and fails closed (exit 78).
  It does NOT yet instantiate a D writer or run a D matrix. The final A+B+D
  tarball, source/integration commit, full release/dependency manifest and
  source/compiled export identities are absent; no pin is assigned now.
  Once supplied, its real generic-D selector/stamp API and release-specific
  adapter must be qualified for normal/context-only rows, actual oversized
  sharding, deferred fresh-process recovery, L1/higher summaries, floorless vs
  stamped branches, compaction, restart and classification/lineage. That requires
  a revised Phase P manifest and explicit owner OK, not substitution of AF 0.30.0
  or handcrafted inboundDelivery metadata. This is the declared remaining
  integration dependency, not a completed application-floor result.

## Residual Risk

- No storage runtime/import/ABI, writable-clone, shutdown or syscall tracing path
  has been exercised, by design. Default-seccomp strace support remains a Phase E
  control: any ptrace/import/ABI/EACCES/EROFS failure is environment-blocked or
  inconclusive, never a native barrier. Do not relax the approved sandbox silently.
- Final hashes cannot reveal transient/native mmap writes. The harness retains
  syscall/API timelines and leaves refusalBeforeMutation null until successful
  OS operations are manually correlated with refusal. Header reads are not
  zero-read. State reads conservatively count as possible row exposure.
- Node compiled/Linux x64 is the only declared runtime/entry-point route. Bun
  source and other platforms are unsupported here; they require separate
  qualification/manifests. No arbitrary-reader or future-format guarantee.
- No native result, D enablement, no-fence decision, deployment, rollback or real
  store operation is authorized. Keep the synthetic compatibility execution stopped
  until the exact native manifest is approved; keep section (b) pending.
