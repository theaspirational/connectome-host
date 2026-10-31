# Review fix round 1 — compiler-core scaffolding, LOCAL ONLY

## Changed

- Local commit **`c9f0991d20e9e2476f7ff57b09f1f2685671d97a`**, parent `9c6f6ea1de60f2ac749f9f3184adcc71f128253f`. Required author/committer and coauthor trailer verified. Eight tracked files, +200/-18. No push, main-checkout edit, merge, install, lock/CI/permission change or live config/store operation.
- Read `/srv/review-drop/TASK-6JAEQ-REVIEW1.md` in full and verified SHA256 **`25bea49aeecea0f21b2ed504936e9d5818dbe0c9699ba306f67826c780e44740`**. Read all four associated reviewer probes (fetch path, inference, scanner, mutants); hashes in `review-inputs.sha256`. Reproduced the original inference/tombstone findings before changing the isolated head (`review-probe-baseline`).

### Finding-by-finding disposition

**MEDIUM — inference contract:** Checked the frozen H brief, host design and T1 first. Host **204–208** explicitly separates channel-less push wake policy from inference explicitly allowed by lane; T1 **229–233** preserves those baseline lane-specific rules. No contradictory rule was found in those supplied documents. Operator confirmation `ask-0831d79e7de14bde9d792dbec07ddf49` and its addition require preserving this rule, not borrowing the push ceiling.

- `CompiledRecipientAdmissionPolicy` now explicitly documents **NOT all-lane ceilings**, with host/T1 citations: inference requires explicit lane allowance, the common exact-issued-key/abort guard, and featureSet/messages schema validation. `read`, `wake`, `wakeWithoutChannel` and addressed tags do not apply. No reply intent or destination is produced. The existing branch behavior is intentionally preserved, not widened.
- A's feature-grant, hook-loop, render, queue and real epoch/currentness guards remain external to this host compiler. Their implementation is **outside the copied A contract sources**; the operator identified framework.ts ~34932–34946 and inference-router.ts ~257–311. That is attributed engineering input, not a new independently reproduced real-A receipt. No host grant check was invented or duplicated, and no A guard was removed/bypassed. No different maintenance/scheduled meaning is asserted from this dead compiler.
- Replaced the old combined inference assertion with discriminating controls: (a) no configured inference lane refuses store/wake, even with a valid item; (b) configured inference permits store/mayWake despite read/wake=false, wakeWithoutChannel=false and missing scope; (c) wrong issued key, reconstructed equal key, stale-epoch key, abort and malformed featureSet/messages refuse; (d) exact closed results contain no reply intent/destination.
- Added **three inference mutants**: unconditional true **before** common guards; dropping the lane check; borrowing the push wake/addressed ceiling. All three killed by the new named inference witness; all old 21 mutants still killed. Total **24**.

**LOW-1:** `readOverlayEntry` checks policy before the disabled tombstone return. Policy-bearing tombstones (including null/false/undefined) loudly refuse; policy-free tombstones retain disable behavior. Direct/compose tests added.

**LOW-2:** `readAgentOverlayFile` now uses `parseRecipientConfigJson`. Hidden policies behind duplicate maps/ids and escaped/BOM input refuse. Only SyntaxError is wrapped as not-JSON; a policy refusal retains its named reason. Policy-free overlay readings/unknown keys/BOM and ordinary grant drop-with-note remain unchanged, covered by tests and the matched probe.

**LOW-3:** Added `check-v1-compiler-controls.mjs`, plus the empty-object v1 negative site. Removes each of the three directives individually and requires one diagnostic at its exact file/line: **TS2353**, **TS2339**, **TS2741** respectively. Temporary controls are removed and original source preserved. Pinned compiler remains TS **7.0.2**.

**LOW-4:** Expanded the in-tree feature-off probe to actual `loadRecipeDetailed` file **and HTTP** paths and `loadSavedRecipe`, each with plain/BOM/invalid/unknown-key cases. Saved BOM refusal is preserved, not changed. Also covers policy-free overlay-file reading and an unconfigured inference connection retaining its original feature/grant config and neither callback. Captures values/errors/warnings, normalizing only temporary probe-directory and HTTP-endpoint text. Outputs are byte-identical against original pre-scaffolding base, SHA256 **`5af23aaa62c8996f61dc32caf50443b233f92b6e0675a68f98cbb88d97936e82`**. This proves production functions/configuration, not real AF inference traffic/no-await/transport effects.

Docs now explicitly record the two unchanged limitations: protected-overlay guards have **unit-only** coverage pending runtime-binding production-path reproof; configured policy blocks server composition at `bug-report.ts:346`. That caller records a composition note instead of obtaining the protected config. `src/bug-report.ts` was not changed and no bypass was added.

## Verification Gates

### Three distinct binding classes

No new root or install. Per `ask-a7622379be9e483da12c90f8c007e13c` and its closed-inventory condition:

| Class | Source binding | Source inventory / use |
| --- | --- | --- |
| **Original base** | existing `tmp-recipient/build/base`, unchanged **d7154d79b161e0827637e715b0952001b010b393** | closed src/test/stubs **423** files; SHA256 **fa5dbd0ae41b51d6195a9797b74b19273f54fca03d71d29d9e7d07d89fd091b1**; fresh matched type/v0/control/suite/probe gates. No v1/core exists here, so v1 gates are N/A, not a claimed base pass. |
| **REUSED old-head observations** | former existing head **9c6f6ea1de60f2ac749f9f3184adcc71f128253f**, measured before staging | closed **436** files; SHA256 **8b6fd6c560d372f1d13586be8d2176dd4ebd557f53decb40930707d12c9a604c**. Historical reference only: types/v0/v1 passed, suite **4597**, focused **90**, mutants **21**, old narrower feature-off probe hash 685a9496… . Not proof of the fix head. Exact logs + SHA256 in `reused-old-head-observations.json`. |
| **Fresh fix-head** | existing `tmp-recipient/build/head`, source/test/stubs now **c9f0991d20e9e2476f7ff57b09f1f2685671d97a** | closed **437** files; SHA256 **0872e0cedbbe693f44e08efa4ced1832f3fe6be12901693289256469605eb543** before/after gates; fresh checks below. Changed v1 types and **NEW head-only v1 control** separately labelled. |

**Identical installed/toolchain bindings for all three classes:**

- Installed closure manifest (root + separately locked web dependencies): **16820** entries, SHA256 **6b42678559f175f926db4a39c1527f2415c37bab1392be6a74ae95446233ed64**. Before stage, after stage and after gates: exact equality to inherited per-root manifests, with no symlink escaping the corresponding installed root.
- Host lock **7f0e4849a8238b90e9d782ffef558a3e5b80fe6252154d2372f42f9a5c09e020**; web lock **0617247d4e5a2d86a86886ec98e3dd3464a9250ce8b8b14dd8d7f9fb856fb0c3**. Manifest/locks/node_modules untouched. No npm ci, Bun install, lifecycle scripts or dependency regeneration this round.
- Node **22.23.3**, `/opt/node-v22.23.3-linux-x64/bin/node`, SHA256 **fde6a4bf8d0562f7751d1a2d6cb9b417c4cfe107bbcb0aa3e9a24e125e348f48**; npm **10.9.9**.
- Bun **1.4.3**, existing official-checksum-qualified binary SHA256 **7ce7d6b654eddeec20afddf2396317fba6dc19ab52788a27e894e1806bf8f24b**. Acquisition/checksum qualification is inherited unchanged, not a new download/receipt.
- TS **7.0.2**, selected native SHA256 **4f2de678286401759b3fb4475bafe35b8f32b4b3a07d92642bbf37eadc9b34a4** in each root. Actual versions/hashes: `actual-runtime-versions.txt`, `toolchain-bindings.txt`.
- Same underlying command for reused old-head suite and fresh matched suites: **`bash scripts/test-on-tmpfs.sh` → `npm test -- --parallel=4`**. Old-head receipt used the prior evidence `bounded.sh`/`cell.sh`; copies used this round are byte-identical (`harness-bindings.txt`). The expanded current probe has different inputs/coverage from the historical probe; its new hash is not compared to the old hash.

### Controlled synthetic staging and output provenance

`staged-inputs.json` records each of the **7 changed tracked src/test files**, before/after SHA256 (new file has null before). Only these files were copied; the changed documentation stays in the actual worktree, not an ad-hoc build overlay. There are **0 tracked deletions** this round. Full closed-set checks enumerate every file under src/test (including stubs) against `git ls-tree HEAD`: all tracked files present with matching SHA256, no extra/stale files, no compiler-control leftovers. Same check repeated after gates. This is synthetic source staging, not shipping an overlay or production support.

No host `dist`, `web/dist`, `out`, `build` or `target` outputs exist in either root. Gates use `tsc --noEmit` and Bun source execution/transpilation from the verified staged source; no leftover host output was trusted. Consumed dependency source/dist/native payloads remain covered by the unchanged installed manifest. No new bundle build/install was introduced. See `host-output-bindings.json`, `source-*-*.json`, `installed-*-*.json`.

Staging was held until the operator-approved visibility procedure completed:

- First `lsof +D`: status 1, no holders, unrelated Docker overlay/netns stat warnings. **INCOMPLETE**, retained in `head-quiescence.txt` and failed `stage-bindings` receipt. No staging occurred.
- First supplementary helper had an empty-Groups-line parser bug, failed before the scan; retained `supplementary-quiescence` receipt. Corrected parser then found zero references but stopped on three same-user unreadable services. Neither result was relabelled PASS; no package/test gate was retried.
- Per `ask-6c5124aac9ac4208b3a019bebd1767b3` and `ask-5a0bd504382c48249e91443b97154a60`, direct scans cover **all visible pids** cwd/root/exe/fd/maps and head symlink targets. Path-limited lsof excludes only the unrelated mounts named in the retained warning, never the head filesystem; no warning suppression. It returned no holders/no stderr.
- **Worker measurement:** zero head references immediately before and immediately after the **first actual staging** (04:24Z), and after gates. New unreadable same-user pids or references would stop staging. Known same-user pid/start ticks pinned: 1830/39736, 1907/39737, 3182/40128.
- **Separately attributed operator witness, not my measurement:** root observation at **2026-10-31T04:23:31Z** checked those three services' cwd/root/exe/fd/maps (39/7/11 fds), zero head references; operator also reported an all-pid root scan circa **04:19Z**, zero references. Inaccessible root-owned pids remain a worker visibility gap covered only by that attributed point-in-time observation. Other unreadable unprivileged users lack traversal through `/home/orgasmic` mode 750 (their groups exclude its owning group). Service processes were not touched.
- These observations are **not a lock**. Staging→gates had one controlled owner: first stage complete before any head gate, compiler controls/mutants serial with source restoration, full suite only after those writers exited, then closed source/dependency proof again. No concurrent source writer was observed or introduced.

### Fresh final-commit gates

All below recorded through `orgasmic dispatch gate` at clean final commit. Command template:

`orgasmic dispatch gate <name> -- bash "$ORGASMIC_EVIDENCE_DIR/bounded.sh" bash "$ORGASMIC_EVIDENCE_DIR/cell.sh" <base|head> <command>`

`cell.sh` keeps the existing roots and scrubbed env/scratch HOME/config/cache/Git ceiling. `bounded.sh`: **16G scope**, swap=0 (memory.max=17179869184), `timeout --signal=TERM --kill-after=60s 3000s`, **64 MiB** cap, PIPESTATUS; 124/137/141/cap = **INCONCLUSIVE**, never PASS/refusal. No assigned test/type/suite/mutant/probe gate was retried, timed out or capped. Raw bounded logs and byte counts are named in each receipt. Log paths below are relative to this run's evidence directory.

| Gate | Command after wrapper | Result | Log |
| --- | --- | --- | --- |
| fix-base-types | `base: npx tsc --noEmit` | PASS | `gate-fix-base-types-01M6830S3QTPBR40D7J7SXJW8J.log` |
| fix-base-v0-types | `base: npx tsc --noEmit -p test/stubs/f-host-2a/v0/tsconfig.json` | PASS | `gate-fix-base-v0-types-01M6830SJ8VWHNDH7H2CTXH355.log` |
| fix-base-v0-controls | `base: node <worktree>/test/stubs/f-host-3/check-compiler-controls.mjs` | PASS: four pinned sites, including isolated TS2542 | `gate-fix-base-v0-controls-01M6835Y1ZTW7SK0CDGNN1YB15.log` |
| fix-base-suite | `base: bash scripts/test-on-tmpfs.sh` | 4579 pass / 0 fail | `gate-fix-base-suite-01M6830SXSZGBR6FXBJ6155SX8.log` |
| fix-head-types | `head: npx tsc --noEmit` | PASS | `gate-fix-head-types-01M6838M35C1X9W1Q0A3CR12AP.log` |
| fix-head-v0-types | `head: npx tsc --noEmit -p test/stubs/f-host-2a/v0/tsconfig.json` | PASS | `gate-fix-head-v0-types-01M6838MHRHYR7S5D5C7HTXC74.log` |
| fix-head-v1-types | `head: npx tsc --noEmit -p test/stubs/f-host-3/v1/tsconfig.json` | PASS; head-only changed v1 input | `gate-fix-head-v1-types-01M6838N0JCFMNFP40RY032Y9M.log` |
| fix-head-v0-controls | `head: node test/stubs/f-host-3/check-compiler-controls.mjs` | PASS | `gate-fix-head-v0-controls-01M6838NFA1BP8HGGZ5TDTT66T.log` |
| fix-head-v1-controls | `head: node test/stubs/f-host-3/check-v1-compiler-controls.mjs` | PASS: NEW head-only gate; TS2353/TS2339/TS2741 | `gate-fix-head-v1-controls-01M6838PVVE3ZCJWPG2DGE62GT.log` |
| fix-head-focused | `head: bun test test/recipient-policy.test.ts test/recipe-unknown-keys.test.ts test/mcpl-agent-overlay.test.ts` | 93 pass / 0 fail | `gate-fix-head-focused-01M6838QT87FE9DEWBBRJKH717.log` |
| fix-head-mutants | `head: BUN_BIN=$(command -v bun) node test/stubs/f-host-3/run-mutants.mjs` | 24 killed: old 21 + 3 inference mutants; source restored | `gate-fix-head-mutants-01M6838R5KWQTGW9BSBYDVGXKZ.log` |
| fix-head-suite | `head: bash scripts/test-on-tmpfs.sh` | 4600 pass / 0 fail | `gate-fix-head-suite-01M683A24J5A0NWBFBNGXSQW5Z.log` |
| fix-feature-off | `both cells: bun <worktree>/test/recipient-feature-off.probe.ts <cell-root>; cmp JSON outputs` | BYTE-IDENTICAL: expanded loader/overlay/inference-config probe | `gate-fix-feature-off-01M6838WM536HF4P8ZCRNJB5YG.log` |
| fix-base-self-test | `base: node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: docs oracle only | `gate-fix-base-self-test-01M6835Z9XG847RQZMXHFJFNPK.log` |
| fix-head-self-test | `head: same --self-test command` | PASS: docs oracle only | `gate-fix-head-self-test-01M6838W4KRM182WVSXA3ST3MD.log` |
| fix-base-docs-contract | `base: node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | exit 0; PARTIAL naming | `gate-fix-base-docs-contract-01M6835ZEYXZKC2ZMEYQ55C7KR.log` |
| fix-head-docs-contract | `head: same --docs-contract command` | exit 0; PARTIAL naming | `gate-fix-head-docs-contract-01M6838W9MKEES8JGSAP22B13A.log` |
| fix-diff-check | `git diff --check 9c6f6ea1 HEAD` | PASS | `gate-fix-diff-check-01M6838WET3XFYJZCZ7XVX8WTZ.log` |
| fix-diff-inspection | `python3 -B <evidence>/check-fix-diff.py` | PASS: targeted names/paths and unchanged frozen inputs only | `gate-fix-diff-inspection-01M683HSGCZV2ZA2QCCBQHB75Y.log` |
| operator-witness-quiescence | `python3 <evidence>/quiescence.py` | 0 visible references + path-limited lsof; operator witness separately attributed | `gate-operator-witness-quiescence-01M683754QEJZGQVYXCPXQX6ZW.log` |
| controlled-first-staging | `python3 <evidence>/controlled-stage.py` | first actual staging: 7 changed src/test files; hashes + closed inventory + unchanged closure | `gate-controlled-first-staging-01M68378E37AXWKSMNX9XMDHM0.log` |
| fix-final-bindings | `python3 -B <evidence>/verify-final-bindings.py` | post-gates closed inventory, no extra/stale sources, same installed manifests | `gate-fix-final-bindings-01M683FEFRR7RDJTK81AH5DW67.log` |

Docs-contract remains **PARTIAL**, its existing nine inputs only; the new staging document is outside that scan. The targeted 8-file diff inspection is not full naming-policy or whole-history privacy qualification.

## Unmet Criteria

No fix-round finding remains unaddressed in the bounded compiler-core slice. **Do not read this as functional H.3/runtime-binding completion.** The inherited runtime criteria stay PENDING:

- Canonical selector representation INPUT GAP; exact composed INT-1 release/artifact/exports and reviewed S4Q8F scope integration/rebuild. Installed AF is still **0.30.0**, not that release.
- Real trusted scope verifier/constructors/accessors, runtime identity/generation/currentness/ancestry invalidation; nominal opacity is compile-time only.
- Real A startup/transport/grant/hook/render/queue/epoch/abort/64-cap/post-await/effect observations and feature-off no-await/wire qualification; B/.4, D/.5, storage/native/dynamic parity and release qualification remain outside these tests.
- **Protected-overlay guards are proven only by unit calls.** Loader fail-closed prevents protected definitions from reaching them in production. Re-prove their production wiring at runtime-binding before lifting interim refusal.
- **Configured policy blocks bug-report server composition** at bug-report.ts:346; that flow records a note/missing protected configuration. Intentionally unchanged; no bug-report bypass or silent policy stripping.
- Full private naming-policy input is still absent; local-only/private ancestry hold remains.

## Residual Risk

- Inference's lane exemption is now explicit and mutant-pinned, not a claim that A's external guards have been runtime-tested. No new grant or scheduled/maintenance semantics are invented. Compiler remains unreachable through production policy loading.
- Staging quiescence is point-in-time evidence, not a filesystem/process lock. Root/service visibility is separately attributed to the operator; raw warnings, parser failure, unreadable pid counts and before/after scans are retained rather than hidden.
- The protected-overlay production-path and bug-report composition limitations above remain operationally important when runtime binding is implemented.
- Own helper processes reconciled by pid/start/cgroup in `process-reconciliation.json` / `.txt`; no remaining owned helpers or head readers. Source/installed trees retained in the assigned ignored scratch roots, no build caches in evidence.
- **LOCAL ONLY; no push.** Private refs/ancestry retained. Public submission still needs a fresh clean tree on a public base and a privacy check of the whole publication range; scrubbed additions do not clean history.
