# Changed

**TASK-4ZM52 — F-host.4 compiler-core delivered, LOCAL ONLY.** Runtime binding is PENDING, not claimed. Task operator: Tarn; programme qualifier: Soma. No operator disposition below is a decision by owner Vasyl.

## Commit and delta

- Frozen start: `c353e1c8d19a11697b5515bf3b03110de5dd85de`, initially clean.
- First implementation head: `536b2e236518ddd1d763e61f39814c93d5a282dd` — retained, including its failed type gates.
- Corrected final head: `e276024acf7c073ad21b42ec5132d96c332a2102`, separate child commit, clean after gates.
- Reported delta: **`c353e1c8..e276024a`**, 15 files, +751/-8. Both commits have author/committer `Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>` and trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
- No main-checkout edits/commits, push, PR, merge, install, live config/store/runtime operation, lockfile/dependency regeneration, wire field, grant or RefusalCode. No public/privacy approval implied. Local HTTP loader tests are isolated localhost fixtures, not live deployment operations.

Changed paths:
```
 docs/design/recipient-reply-compiler.md         |  71 +++++++
 src/recipient-admission.ts                      |  85 +++++++-
 test/recipient-policy.test.ts                   |  11 +-
 test/recipient-reply-loading.test.ts            |  26 +++
 test/recipient-reply.test.ts                    | 248 ++++++++++++++++++++++++
 test/stubs/f-host-3/run-mutants.mjs             |   3 +-
 test/stubs/f-host-4/b-model.ts                  |  71 +++++++
 test/stubs/f-host-4/check-b-contract.mjs        |  22 +++
 test/stubs/f-host-4/check-compiler-controls.mjs |  34 ++++
 test/stubs/f-host-4/check-return-controls.mjs   |  28 +++
 test/stubs/f-host-4/run-mutants.mjs             |  54 ++++++
 test/stubs/f-host-4/v2/b-contract.ts            |  27 +++
 test/stubs/f-host-4/v2/static-policy.ts         |  59 ++++++
 test/stubs/f-host-4/v2/tsconfig.json            |   7 +
 test/stubs/f-host-4/v2/typecheck.ts             |  13 ++
 15 files changed, 751 insertions(+), 8 deletions(-)
```

`src/recipient-admission.ts` adds optional fail-closed `reply`, eligible-only intent, B install iff present policy and `automaticReplies !== false`, lane-first resolver and synchronous literal-true authorize. Private weak approval bookkeeping associates the resolved input/key with this compiler; authorize rechecks the exact connection-id/channel destination, original account and current ceiling via `trusted.lookup`. No input/scope/store/external state is written. Unique scope projection is a versioned double; AF owns registered descriptor/grant/incarnation validation and the frozen binding.

New v2 fixture compiler maps superseded test-only `replyChannels` to `reply`; the v1 tree stays byte-identical. The pinned B type surface, byte checks, assignability, excess/missing negative controls, host tests, B model and mutants are all explicitly bounded. **Every B-behavior result is stub-of-B.** Production `recipient_policy_integration_pending` remains unchanged, with a real loader negative over recipe validation, file, HTTP, saved recipe, operator server file and overlay-file paths.

The second commit changes only the explicit structural return annotation in the runtime file, plus its new compiler-control script. There is no cast, suppression or weakened nested return shape: only the two conditional callbacks are optional; their required members have exact types. Four discriminating controls remove a required inbound attestation / B timeout and change each callback's timeout to string; inner tsc exits 1 with TS2741/TS2322 as expected. `final-bindings.py` reconstructs the first-head source bytes exactly by deleting only that annotation; runtime body is identical.

## Input and instruction check

Read full immutable v4.3 brief, full CHECK1/RECHECK1, supplied project baseline, applicable skill door, command ownership and gate-receipt pages. No conventions/gotchas supplied; no applicable AGENTS.md found on ancestor paths or within the worktree. No independent prior result is treated as completion proof.

Verified **before use** with `sha256sum` and `(cd <drop> && sha256sum -c SHA256SUMS)`:
- Brief: `2fafa3d4a2e16d969596086cca5d8b00b0a5c032edd38e0736e91430757c2e84`.
- A SHA256SUMS: `1a8406fa18e4f2b5c777ae773c68772eacea50a5a6c2f0882bd0e1fa6ffec2b2`; 5/5 files OK.
- B **v2** SHA256SUMS: `d9c5164818dd166ae62418fc3ce6060345ad2ec212e3e9190e6f49d5c9a796fe`; 10/10 files OK.

Final integrity gate independently records these checks and the supplied root-witness hash. Only B v2 signatures are used; no historical `27a32127` semantics. B root exports types only. Host destination serverId is the connection id, not a configured server id. Byte check pins ReplyAuthorityConfig/ReplyDestination/ChannelRef, transitive AdmissionInput and the exact notice literal against cited source ranges and full hashes.

## ENGINEERING STOP DISPOSITION — activation blocker, not an owner decision

I **stopped before implementation** when the mandatory inventory found the conversation timer/home dependency. `ask-428cedb00fc6453e9f115919dcf66ef0` stdout supplied the following amendment by Tarn, accepted by Soma:

> Continue .4 as compiler-core only. Production stays fail-closed: recipient_policy_integration_pending is NOT lifted and stays a negative on all real loader paths (with a test).
>
> The found cases — src/recipe.ts:1528–1536 (conversations timer closure; test/conversations-recipe.test.ts:24) and :1476–1482 (speech home/latest-inbound), src/index.ts:503,525,530 — are a named ACTIVATION BLOCKER. These are the concretely found cases, NOT an exhaustive activation predicate. Don't claim that "default delivery without a locus = private" covers the locus case, other origins or disabled prose; keep the mode/effect distinctions. Don't claim which agent is affected.
>
> In .4: keep the §2.4 inventory (file:line) as a named control + one clearly labelled stub-of-B demonstration: under B opt-in such prose → private + notice (per B docs:90–98, the literal from the v2 drop). NO changes to recipe.ts/index.ts, no rerouting, no real notices.
>
> Acceptance EXCLUDES these unbound automatic-output paths; their activation needs an owner decision (owner list items 6/7).

That named control is in `docs/design/recipient-reply-compiler.md`, exercised by `stub-of-B model ONLY — no real AF effects > activation blocker inventory: unbound automatic prose on opted-in connection private + notice`. It checks the concrete citations and models would-be prose, not disabled prose or all origins. Recipe/index bytes remain unchanged. No specific deployed agent or usage is asserted.

### §2.4 inventory (baseline file:line)

| Existing path | Source / named control | Disposition |
| --- | --- | --- |
| Timer-expiry conversation closure → fork-home prose routing | `src/recipe.ts:1528–1536`, `:1476–1482`; `src/framework-strategy.ts:224–228`; `src/index.ts:503,525,530`. `test/conversations-recipe.test.ts`: “full valid block is accepted” (:24 asks “Finalize the case report and post the answer.”), and “recipe fields pass through; omitted fields stay absent for framework defaults”. | Concretely found **ACTIVATION BLOCKER**; excluded from compiler-core acceptance by the disposition. Deprecation is not removal. |
| Heartbeat/reminder wake with ordinary home/latest-inbound routing available | `src/heartbeat-options.ts:17–30`, `src/index.ts:505–519`, `src/recipe.ts:1476–1482`. `test/heartbeat-judge-take-effect.test.ts`: “with both on, the heartbeat tools are offered and wake_add_rule takes a judge”. | Candidate unbound automatic path; the existing control tests configuration/tools, not public delivery. |
| Subagent result / unrelated module wake | `src/modules/subagent-module.ts:1427,2534–2541`. `test/subagent-prose-routing.test.ts`: “parent without explicit proseRouting yields AF's default on the child ('locus')”. | Candidate unbound module path; control proves inheritance, not a delivery or affected-agent claim. |
| Channel-less UI/IPC external-message wakes | `src/headless.ts:375–380`, `src/index.ts:755`, `src/modules/web-ui-module.ts:1661–1667`, `src/modules/tui-module.ts:24–61`; :35–47 warns about home/default/private prose and recommends explicit send. | Operator-input path, not itself proof of an automatic dependency. |
| Focus + heartbeat prose to excepted work channel | `docs/DENDRITE-TEST-RIG.md:76–85`. | Documentary supporting expectation, not fresh runtime proof. |
| Activity / explicit targeted notice/relay paths | `src/modules/activity-module.ts:104–110` concerns typing; notices/relay name target/publish paths separately. | Not counted as proof of generated unbound automatic prose. |

This bounded inventory is **not exhaustive activation qualification**. Locus/explicit/hybrid/disabled modes and their effects stay distinct. Private output without a locus does not dispose of home/latest-inbound/focus or other-origin dependencies. No rerouting or real notice was added.

## Authorized .3 re-baselining map

All line numbers below are **at c353e1c8**, not the shifted final file. These are the complete restated cells; every other .3 assertion/control remains unchanged. The v1 tests still use admission-only and omit the optional reply predicate, so their old store/wake verdicts remain exact and intent is explicitly absent.

| Old cell/assertion | New contract / replacement assertion | Replacement witness / mutant |
| --- | --- | --- |
| :203 title “…full waits for .4” | “…full installs B (stub-of-B)” | Old no-policy mutant :23 retargeted to the nonzero title; final named kill recorded. |
| :208 full `toThrow(recipient_reply_authority_pending)` | Full has a defined replyAuthority | Same restated title plus new “install iff enabled” witness. Old :24 “full silently offered before B” retired; new install-whenever-policy and never-install mutants killed. |
| :213 `{store:true,mayWake:true}` | Same exact verdict + explicit no replyIntent (v1/admission-only) | “bind returns the exact object…”; reconstructed-key/binder mutants retained. |
| :222 reference `{store:true,mayWake:false}` | Same exact verdict + explicit no intent | “ambient read/wake independent…”; read/wake/lane/addressed mutants retained. |
| :223 no addressed tag `{store:true,mayWake:false}` | Same exact verdict + explicit no intent | Same ambient witness; “prose or ambient wakes” killed. |
| :233 channel-less push `{store:true,mayWake:false}` | Same exact verdict + explicit no intent | “push channel evidence cannot escape…”; channel-less wake-ceiling mutant killed. |
| :234 allowed channel-less wake `{store:true,mayWake:true}` | Same exact verdict + explicit no intent | Same push witness; new eligible-intent witness covers enabled channel-less work too. |
| :235 reference-origin push `{store:true,mayWake:false}` | Same exact verdict + explicit no intent | Same push witness; no channel-evidence stripping. |
| :281 author-capped wake `{store:true,mayWake:false}` | Same exact verdict + explicit no intent | “compiled ceilings receive exact detached evidence…”; per-item/author-ceiling mutants retained. |

New v2 positive/negative controls additionally prove eligible channel/push intent and no intent for denied storage, nonwake work, outside-reply work, disabled automatic replies, absent reply, channel-less push and inference. Inference's .3 lane exemption and common guards are unchanged.

### v1 before/after SHA256 (all four byte-identical)

- `test/stubs/f-host-3/v1/a-contract.ts`: before = final = `f8704bf26175161ee1da5aefc47cdc095fceee8801fcacf9216e4bdbf2a5d0d2`.
- `test/stubs/f-host-3/v1/static-policy.ts`: before = final = `568019a3663f5cf9fe07ecad32d2f556e15e0e91d45149686582f61ef9c39f36`.
- `test/stubs/f-host-3/v1/tsconfig.json`: before = final = `8d1da823bc17b5a3ff6d238e0fd07c6e8896ea9df0efc80006f4e7e56a58a7fc`.
- `test/stubs/f-host-3/v1/typecheck.ts`: before = final = `c14bf549828a83bbf98490392e0b8aca231ee14b0bfb0dd8455af3cee999786e`.

# Verification Gates

## Execution, bindings and lineage

Every gate used `orgasmic dispatch gate NAME -- bash "$ORGASMIC_EVIDENCE_DIR/bounded.sh" ...`: 16G scope, swap 0, `timeout --signal=TERM --kill-after=60s 3000s`, 64 MiB cap, PIPESTATUS retained. No 124/137/141/cap/inconclusive result. Gates were not retried at the same head. The only requalification was the separately approved type-only correction/new commit below; first-head failures remain intact. Exact logs and exit codes are indexed in `gate-index.json`.

The only source-execution root is the **existing** `/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-6jaeq/tmp-recipient/build/head`. `cell.sh` scrubs HOME/config/cache/environment and selects the declared Node/Bun binaries; no token is forwarded or printed. Only test state directories were added beneath the existing state area. No install/new dependency or build root.

| Binding class | Source inventory and use |
| --- | --- |
| Initial inherited root (not implementation base) | Before staging, 437 closed src/test files matched c9f0991d, the old runtime source; inherited installed manifest checked. No implementation was sourced from this head. |
| Matched base = c353e1c8 | Existing root staged to exact frozen base; 555 tracked inputs / 437 src/test files. Fresh base gates in this run; now **REUSED same-run base observations** per operator permission, not old .3 receipts. Before transition, all 14 changed paths' prior hashes matched the base manifest; unchanged paths remained bound. `inputs-base-after-stage.json`, `staged-head.json`, `base-reused-binding.json`. Worktree receipts were dirty while the separately verified staged input was exact c353e1c8, so not claimed as generic reusable clean-tree receipts. |
| Failed first head = 536b2e23 | 566 tracked / 447 src/test files; first results below. Full source inventory verified unchanged after those gates before the correction was staged (`inputs-final-first-head-after-gates.json`). Its full-suite receipt ended with a dirty worktree because the authorized type-only correction was being prepared elsewhere; staged source remained exactly 536b2e23. No first-head receipt substitutes for final qualification. |
| Fresh corrected final = e276024a | 567 tracked / 448 src/test files, closed before and after gates; clean receipt commit. `inputs-final-after-stage.json`, `inputs-final-after-gates.json`. All final gates rerun once, not only previously failed types. |

Installed root+web closure before/after **every stage and final gates**: 16,820 entries, SHA256 `6b42678559f175f926db4a39c1527f2415c37bab1392be6a74ae95446233ed64`. Symlink targets stay within the corresponding dependency root. `installed-*.json` retain the full inventories. Lock hashes: host `7f0e4849a8238b90e9d782ffef558a3e5b80fe6252154d2372f42f9a5c09e020`, web `0617247d4e5a2d86a86886ec98e3dd3464a9250ce8b8b14dd8d7f9fb856fb0c3`.

Node 22.23.3 SHA256 `fde6a4bf8d0562f7751d1a2d6cb9b417c4cfe107bbcb0aa3e9a24e125e348f48`; Bun 1.4.3 `7ce7d6b654eddeec20afddf2396317fba6dc19ab52788a27e894e1806bf8f24b`; selected TS 7.0.2 native `4f2de678286401759b3fb4475bafe35b8f32b4b3a07d92642bbf37eadc9b34a4`. Final bindings verify all exact hashes. The preliminary base toolchain command's obsolete native-preview path was absent and masked by its trailing `true`; it is **not** claimed as the native hash proof. The final direct native hash plus identical before/after closure provides that proof. No host dist/web-dist/out/build/target is present or consumed; Bun executes staged source, tsc uses noEmit. Dependency compiled payloads are covered by the installed manifest.

### Quiescence and resource controls

Initial `f4-prestage-quiescence` **FAILED**, exit 1: zero visible references but three unreadable same-user services and root visibility gap; no staging occurred. The failure file/log is preserved. `ask-47c483dbd1b94eda9b87228944bbd428` supplied a fresh, separately attributed **operator root witness** at 2026-10-31T05:25:12Z: all 376 pids, zero prefix hits/read errors. `/srv/review-drop/TASK-4ZM52-ROOT-WITNESS-1.json` SHA256 `08e68fc98e34207c63acf38ef6812ac9ff86664252aa6729383881d311e53360` verified. Named inaccessible services 1830/39736, 1907/39737, 3182/40128 have pinned uid/start/cgroup; no access was changed.

Permission explicitly allowed sequential reuse of that same root. Immediately-before/after visible scans and path-limited lsof preceded each stage; no holders, no new unresolved same-user identities. Only six unrelated mount paths from the retained .3 warning were excluded from lsof; no warning suppression or exclusion of the head filesystem. Point-in-time operator root observation is not my visibility and not a lock, particularly for later/new inaccessible root pids. No concurrent source writer was introduced. Scan failures were not edited or overwritten.

Disk reserve checks before full suites: base 25,574,150,144 bytes, first head 25,541,857,280, final 25,522,450,432 (>20 GiB); final observation 25,516,036,096. Expected evidence <256 MiB; observed ~30 MiB, no build caches in evidence. Source/dependency staging inventories and quiescence artifacts are durable. `process-reconciliation.json/.txt`: **102 known owner pids checked by pid/start/cgroup, 0 matching survivors, 51 gate scopes, 0 nonempty scopes, 0 build-cwd processes**. No unrelated process killed. All four ask sessions exited with stdout answers; no live question process remains.

### Same-run matched base gates (REUSED observations, c353e1c8)

Command prefix below: `bash "$E/cell.sh" base` inside the bounded wrapper; both logical labels use the same existing root sequentially.

| Gate | Command after cell prefix | Outcome | Log under evidence |
| --- | --- | --- | --- |
| `f4-base-types` | `npx --no-install tsc --noEmit` | PASS; exit 0 | `gate-f4-base-types-01M686SZKZHZCH1G1D93WPEX5T.log` |
| `f4-base-v0-types` | `npx --no-install tsc --noEmit -p test/stubs/f-host-2a/v0/tsconfig.json` | PASS; exit 0 | `gate-f4-base-v0-types-01M686T02B766JAQ88S05K39EK.log` |
| `f4-base-v1-types` | `npx --no-install tsc --noEmit -p test/stubs/f-host-3/v1/tsconfig.json` | PASS; exit 0 | `gate-f4-base-v1-types-01M686T0DRMDWAHY02YJ3A5AA2.log` |
| `f4-base-v0-controls` | `node test/stubs/f-host-3/check-compiler-controls.mjs` | PASS; exit 0 | `gate-f4-base-v0-controls-01M686T0RZGD1A9RXQK5E1B2YP.log` |
| `f4-base-v1-controls` | `node test/stubs/f-host-3/check-v1-compiler-controls.mjs` | PASS; exit 0 | `gate-f4-base-v1-controls-01M686T1XCWTNPV39AA2ESGN1M.log` |
| `f4-base-focused` | `bun test test/recipient-policy.test.ts test/recipe-unknown-keys.test.ts test/mcpl-agent-overlay.test.ts` | PASS — 93 tests; exit 0 | `gate-f4-base-focused-01M686T2VVFBS24ZM6HJX06T91.log` |
| `f4-base-feature-off` | `bun test/recipient-feature-off.probe.ts <existing-root>` | PASS; exit 0 | `gate-f4-base-feature-off-01M686T37EHJBC8PPYVRWAAKRS.log` |
| `f4-base-self-test` | `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS; exit 0 | `gate-f4-base-self-test-01M686T3JSAMY86EZEMB7N10B6.log` |
| `f4-base-docs-contract` | `node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | FAILED — declared filename FP; exit 1 | `gate-f4-base-docs-contract-01M686T3R5ZQP5E97F4KWHEJK7.log` |
| `f4-base-suite` | `bash scripts/test-on-tmpfs.sh` | PASS — 4600 tests; exit 0 | `gate-f4-base-suite-01M686T3XNGFZG8SRPK4AY5W2Z.log` |

Base v2/B gates are N/A: the frozen base has no v2 compiler or B implementation. Base feature-off produced 3,974 bytes. No base .3 receipt was retrofitted to describe the new head.

### Failed first head, 536b2e23 (retained; not final qualification)

The optional replyAuthority spread made TS7 infer the static compiler wrapper result as `{}`. Root tsc passed because root config includes src only; v1/v2 assignment gates caught this real implementation regression. Their controls also failed on the extra unintended diagnostic. These four failures are not pre-existing and not relabelled as passes:

| Gate | Command | Outcome | Log |
| --- | --- | --- | --- |
| `f4-head-v1-types` | `tsc --noEmit -p test/stubs/f-host-3/v1/tsconfig.json` | FAILED — TS2339 regression / extra diagnostic; exit 1 | `gate-f4-head-v1-types-01M6872BNKXQ9Z1PQT5CSW49CP.log` |
| `f4-head-v2-types` | `tsc --noEmit -p test/stubs/f-host-4/v2/tsconfig.json` | FAILED — TS2339 regression / extra diagnostic; exit 1 | `gate-f4-head-v2-types-01M6872C0W9EJFFD1DJBHPF35D.log` |
| `f4-head-v1-controls` | `node test/stubs/f-host-3/check-v1-compiler-controls.mjs` | FAILED — TS2339 regression / extra diagnostic; exit 1 | `gate-f4-head-v1-controls-01M6872DGG4A2QJGPDVRVR5YCB.log` |
| `f4-head-v2-controls` | `node test/stubs/f-host-4/check-compiler-controls.mjs` | FAILED — TS2339 regression / extra diagnostic; exit 1 | `gate-f4-head-v2-controls-01M6872DYWV2N6878513T67KC3.log` |

Other first-head outcomes: root types/v0 types/v0 controls/B byte pin passed; focused **113 pass, 728 assertions**; retained .3 mutants **23 killed**, new **16 killed**; feature-off byte-identical; self-test passed; docs-contract expected FAILED; full suite **4620 pass / 0 fail**. All first-head logs remain in `gate-index.json` and `head-status.txt`, not overwritten.

`ask-319d789fb1184183a4f175850fc9d7a9` authorized **one new separate type-only commit**, discriminating negative controls and **one full corrected-head qualification**, with the matched base reused if bindings unchanged. Soma's refinement (`msg-058fc70229fa4dcc8a283e85f3d30662`) required exact required nested types, no cast/any/suppression and missing-member/wrong-field-type negatives. Those requirements are satisfied and their SHA/control evidence is listed above.

### Corrected final head gates, e276024a

Cell command prefix: `bash "$E/cell.sh" head`. Gate source inventories bind it to e276024a, not the earlier logical-head contents.

| Gate | Command after cell prefix (or noted direct) | Outcome | Log |
| --- | --- | --- | --- |
| `f4-final-types` | `npx --no-install tsc --noEmit` | PASS; exit 0 | `gate-f4-final-types-01M6878TA4B9D9M2W1GPT23MWX.log` |
| `f4-final-v0-types` | `npx --no-install tsc --noEmit -p test/stubs/f-host-2a/v0/tsconfig.json` | PASS; exit 0 | `gate-f4-final-v0-types-01M6878TRJ06N01NADKEEMM6C6.log` |
| `f4-final-v1-types` | `npx --no-install tsc --noEmit -p test/stubs/f-host-3/v1/tsconfig.json` | PASS; exit 0 | `gate-f4-final-v1-types-01M6878VABR68RAAZPFWX9ZXJ1.log` |
| `f4-final-v2-types` | `npx --no-install tsc --noEmit -p test/stubs/f-host-4/v2/tsconfig.json` | PASS; exit 0 | `gate-f4-final-v2-types-01M6878VRMGQ57KZXNBRHAC9XA.log` |
| `f4-final-v0-controls` | `node test/stubs/f-host-3/check-compiler-controls.mjs` | PASS; exit 0 | `gate-f4-final-v0-controls-01M6878W3ZTW9M4W1Z71A2D5WM.log` |
| `f4-final-v1-controls` | `node test/stubs/f-host-3/check-v1-compiler-controls.mjs` | PASS; exit 0 | `gate-f4-final-v1-controls-01M6878X55QFEWZWS898658F5Q.log` |
| `f4-final-v2-controls` | `node test/stubs/f-host-4/check-compiler-controls.mjs` | PASS — missing authorize TS2741 / excess mappingId TS2353; exit 0 | `gate-f4-final-v2-controls-01M6878XZXBB0G5S37PSM2107A.log` |
| `f4-final-return-controls` | `node test/stubs/f-host-4/check-return-controls.mjs` | PASS — four expected inner tsc failures; exit 0 | `gate-f4-final-return-controls-01M6878YMMEQSH1S6KQJTFJ2FV.log` |
| `f4-final-selectors` | `BUN_BIN=<declared-bun> node $E/check-selectors.mjs` | FAILED — supplemental helper format assumption; retained; exit 1 | `gate-f4-final-selectors-01M6878ZRYWXH0HH0S13578YHA.log` |
| `f4-final-b-contract` | `node test/stubs/f-host-4/check-b-contract.mjs /srv/review-drop/af-b29edcf8-contract-v2` | PASS; exit 0 | `gate-f4-final-b-contract-01M68790480X34807ZP8CN6FSB.log` |
| `f4-final-focused` | `bun test test/recipient-policy.test.ts test/recipient-reply.test.ts test/recipient-reply-loading.test.ts test/recipe-unknown-keys.test.ts test/mcpl-agent-overlay.test.ts` | PASS — 113 tests / 728 assertions; exit 0 | `gate-f4-final-focused-01M687909AW8PKVHRFH8CPWX55.log` |
| `f4-final-old-mutants` | `BUN_BIN=<declared-bun> node test/stubs/f-host-3/run-mutants.mjs` | PASS — 23 named kills; exit 0 | `gate-f4-final-old-mutants-01M68790PGZVA85GJ10166PZ4V.log` |
| `f4-final-new-mutants` | `BUN_BIN=<declared-bun> node test/stubs/f-host-4/run-mutants.mjs` | PASS — 16 named kills; exit 0 | `gate-f4-final-new-mutants-01M68794C6M9H9ED3ECQC7G5TV.log` |
| `f4-final-feature-off` | `bun test/recipient-feature-off.probe.ts <existing-root>` | PASS; exit 0 | `gate-f4-final-feature-off-01M68794MN0GR2N1H4Q2CVCWZ3.log` |
| `f4-final-feature-compare` | `direct: python3 -B $E/compare-feature.py` | PASS; exit 0 | `gate-f4-final-feature-compare-01M687950A0Q2AX1VNWGHV5F21.log` |
| `f4-final-self-test` | `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS; exit 0 | `gate-f4-final-self-test-01M687955SM45MSP574JZ9YT9V.log` |
| `f4-final-docs-contract` | `node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | FAILED — declared filename FP; exit 1 | `gate-f4-final-docs-contract-01M68795B5ENZAH8ZFBZ7QC3SB.log` |
| `f4-final-suite` | `bash scripts/test-on-tmpfs.sh` | PASS — 4620 tests / 0 fail; exit 0 | `gate-f4-final-suite-01M68795G7TJ75A50V1GSXGK58.log` |
| `f4-final-input-integrity` | `direct: bash $E/input-integrity.sh` | PASS; exit 0 | `gate-f4-final-input-integrity-01M687C8VQJM6WEB5JE5ERKF2S.log` |
| `f4-final-diff-check` | `direct: git diff --check c353e1c8 HEAD` | PASS; exit 0 | `gate-f4-final-diff-check-01M687C90RYGF5ECMB8B6HWCFP.log` |
| `f4-final-bindings` | `direct: python3 -B $E/final-bindings.py` | PASS; exit 0 | `gate-f4-final-bindings-01M687CXBA16RQ6CJGBVWMFB9T.log` |

Self-test: 57 fixture cases, 45 rejection controls, four oracle mutants, singleton and naming-parser controls; this is not real B execution. Full suite uses `bash scripts/test-on-tmpfs.sh` → `npm test -- --parallel=4`; final 4620 tests / 269 files / 101.04s.

Feature-off is **byte-identical** c353e1c8 versus final e276024a: 3,974 bytes, SHA256 `5af23aaa62c8996f61dc32caf50443b233f92b6e0675a68f98cbb88d97936e82`, `feature-off-base.json` / `feature-off-final.json`. This covers the existing production-function loader/overlay/config probe, not real AF transport/no-await effects. Admission-only still installs no B and emits no intent, exercised fresh at final head.

**Docs-contract is FAILED**, not green: both matched base and final exit 1 at checker:233/361. The unchanged input enumeration includes `../../stubs/f-host-3/run-mutants.mjs` (checker:343); the unchanged TASK|RUN|AGENT|PROJECT- filename predicate matches `run-mutants`. This is the declared filename FP/TASK-EC6WW. No checker/oracle/fixture change was made; naming qualification remains partial without the full private naming input.

### Supplemental zero-selector helper: FAILED, not renamed as a pass

`f4-final-selectors`, exit **1**, failed on its evidence-helper assertion `/0 pass/`. Actual Bun 1.4.3 text was:
```
error: regex "^NO_SUCH_WITNESS$" matched 0 tests. Searched 1 file (skipping 21 tests)
```
The zero-mutant child printed `EXPECTED FAILED zero-mutant selector: exit=1`. The exact named-failure predicate rejected the zero-test output (asserted false before the bad format assertion). **The Bun child's numeric exit status was not printed/retained by this helper; no nonzero-child-exit claim is made from error text alone.** The helper's own status 1 and complete raw log are retained. It was neither edited nor rerun.

`ask-a24ee80809604150ba175d4ef715f80c` operator disposition: finish without rerun; retain helper FAILED, raw evidence, exact format cause and predicate behavior; use the **23+16 named mutant kills**, each with killing test, as the retargeted-title selector controls, not as a substitute pass for this helper. A zero-test selector cannot produce the named `(fail)` witness required by either runner; the new runner separately rejects zero mutant selections.

## Cells and killing tests — final corrected head

All B cells below are **stub-of-B**, even when exercising real host compiler functions. Every listed mutant produced a named failing test and restored source bytes. Full closed inventory after all gates proves restoration. B descriptor/grant/incarnation effects are not credited here.

| Cell | Control / killing witness (test/recipient-reply.test.ts unless stated) | Mutant / result |
| --- | --- | --- |
| 1 install iff; admission-only, policy-free, full, separate deadlines | “install iff enabled”; old restated title in recipient-policy.test.ts | install whenever policy exists / never install KILLED; existing no-policy mutant retained |
| 2 inference/channel-less lane-first, zero lookup | “lane first” | resolver reads channel regardless of lane KILLED |
| 3 absent/version/extra/accessor/symbol intent | “closed intent required” | unvalidated reply intent KILLED |
| 4 serverId exactly connection id; original key/input/compiler/account | “exact connection-id destination” | configured server id comparison KILLED |
| 5 unique intended, permitted, on-connection scope; mismatch/ambiguity/metadata/abort/no input writes | “unique intended registered”; “abort bounded” | locus/latest fallback KILLED; negative register/scope controls use versioned doubles |
| 6 synchronous literal true, truthy/promise/throw and revocation | “literal true ceiling only”; “send-time ceiling double revocation”; model “authorizer nonliteral values” | truthy host ceiling / stub truthy authorize KILLED |
| 7 same channel id on two connections | “two connections same channel” | cross-connection cached lookup KILLED |
| 8 only eligible channel/push intent; absent reply fail-closed; inference unchanged | “eligible intent only”; “absent reply fails closed”; unchanged .3 inference witness | absent reply defaults allow / intent for nonwake / intent for disabled replies KILLED |
| 9 pinned type compatibility and negative types | v2/typecheck + check-compiler-controls + check-return-controls | Expected inner failures: missing/excess + four discriminating return negatives; not runtime tests |
| 10 inventory control + opt-in unbound private/notice demonstration | “activation blocker inventory…” | PASS stub demonstration; actual unbound paths excluded, owner activation decision needed |
| Stub failure/notice and no exception content | “throw private and fixed notice” | exception content in notice diagnostics KILLED |
| Stub 64-pending overload | “64 pending overload private…” | PASS control; pending waits aborted and drained |
| Stub fault throttling across consumer/authority/reconnect | “fault window shared…” | PASS control: one line through 59999 ms, second at 60000 ms |
| Stub frozen reconnect, no resolver rerun/replay | “reconnect reauthorizes frozen…” | resolver rerun on reconnect KILLED; no real transport/tool replay claim |
| Stub all-causes shared turn and revocation | “shared turn every original cause…” | shared-turn winner-only KILLED |
| Separate A/B deadlines | “install iff enabled”; “separate A and B timeout…” | merged A/B timeout KILLED |

### Complete final mutant / killing-test inventory

| Group | Mutant | Named killing witness |
| --- | --- | --- |
| retained .3 | fail-closed removed | recipe and operator file use the same loud refusal |
| retained .3 | protected replacement sheds policy | rejects policy shedding |
| retained .3 | ordinary overlay globally rejected | ordinary enabledCapabilities overlay |
| retained .3 | overlay installs policy | rejects policy shedding |
| retained .3 | modern family accepted | AF binding resolver refuses both modern families |
| retained .3 | binder reconstructs key | bind returns the exact object |
| retained .3 | trust equal key claims | bind returns the exact object |
| retained .3 | skip configured lane | ambient read/wake independent |
| retained .3 | skip read ceiling | ambient read/wake independent |
| retained .3 | descriptor instead of trusted scope | ambient read/wake independent |
| retained .3 | use wake as storage ceiling | ambient read/wake independent |
| retained .3 | use read as wake ceiling | ambient read/wake independent |
| retained .3 | prose or ambient wakes | ambient read/wake independent |
| retained .3 | wrong account echo accepted | read/matches uses exactly the configured identity |
| retained .3 | extra intent keys | strict intent rejects accessors |
| retained .3 | feature-off installs callback | off installs neither callback; admission-only installs no B/D; full installs B |
| retained .3 | channel-less wake ignores ceiling | push channel evidence cannot escape |
| retained .3 | per-item evidence replaced | compiled ceilings receive exact detached evidence |
| retained .3 | author access wake ceiling ignored | compiled ceilings receive exact detached evidence |
| retained .3 | inference unconditional before common guards | inference explicit lane exemption |
| retained .3 | inference drops explicit lane check | inference explicit lane exemption |
| retained .3 | inference borrows push wake ceiling | inference explicit lane exemption |
| retained .3 | selected mode repaired | malformed lane evidence and unsupported selectors |
| new (B cells stub-of-B) | install whenever policy exists | install iff enabled |
| new (B cells stub-of-B) | never install | install iff enabled |
| new (B cells stub-of-B) | resolver reads channel regardless of lane | lane first |
| new (B cells stub-of-B) | unvalidated reply intent | closed intent required |
| new (B cells stub-of-B) | configured server id comparison | exact connection-id destination |
| new (B cells stub-of-B) | locus latest fallback | unique intended registered |
| new (B cells stub-of-B) | truthy host ceiling accepts | literal true ceiling only |
| new (B cells stub-of-B) | cross connection cached lookup | two connections same channel |
| new (B cells stub-of-B) | absent reply defaults allow | absent reply fails closed |
| new (B cells stub-of-B) | intent for nonwake work | eligible intent only |
| new (B cells stub-of-B) | intent for disabled replies | eligible intent only |
| new (B cells stub-of-B) | merged A B timeout | install iff enabled |
| new (B cells stub-of-B) | stub-of-B truthy authorize | authorizer nonliteral values |
| new (B cells stub-of-B) | stub-of-B resolver rerun on reconnect | reconnect reauthorizes frozen |
| new (B cells stub-of-B) | stub-of-B exception content in notice diagnostics | throw private and fixed notice |
| new (B cells stub-of-B) | stub-of-B shared turn winner only | shared turn every original cause |

# Unmet Criteria

- Runtime binding, acceptance (b), remains **PENDING on I6/.2a/I7**, not stubbed into PASS: equal-string descriptor replacement, publish grant/incarnation, actual AF reply-consumer lane gating, actual transport/reconnect/delivery/notice, trusted scope construction/current generation, dynamic selectors/T2 and native/dynamic parity. No real notice, delivery or installed/live B claim.
- The concretely found unbound timer/home paths remain an **ACTIVATION BLOCKER**, excluded from compiler-core acceptance by the engineering stop disposition; activation needs an owner decision (items 6/7). This inventory does not define an exhaustive activation predicate or affected agent.
- Docs-contract remains FAILED on the declared filename false positive; full naming qualification is not claimed.
- Supplemental zero-selector helper remains FAILED as recorded; its Bun-child exit code was not retained. Operator explicitly accepted finishing without rerun. Actual named mutant kills are the selector controls.
- The first-head TS2339 implementation regression is fixed, but its first-head failures and dirty-ended first suite remain historical failed-head observations, never substituted for fresh final qualification.
- Unchanged T1/T2 oracle gaps: `destination.mappingId` absent from B ReplyDestination; oracle lane enum lacks `inference/request`. Out of .4 scope, no edits.

Within the operator-amended **compiler-core (a)** slice, no implementation or assigned fresh corrected-head control remains unperformed. No runtime/activation gap above is declared resolved.

# Residual Risk

- Host trusted lookup is a versioned-double seam with nominal opaque scopes, not a real registry/verifier/revalidation implementation. AF-side descriptor/grant/incarnation cannot be qualified by host stubs.
- Private input/key association assumes B's documented frozen approvedInput lifecycle; actual release binding remains unproven. Stub all-causes/reconnect/notice behavior qualifies only the model.
- Production opt-in still refuses at real loader paths; protected overlay guards remain unit/compiler-core evidence until integration. Bug-report composition/diagnostic behavior is unchanged.
- Operator root witness and worker visible scans are point-in-time observations, not access/locking guarantees. Unreadable root pids at later scans remain a visibility limit, explicitly attributed rather than suppressed.
- Private ancestry/publication hold unchanged. No full-history public privacy check or release activation approval. Existing closure and isolated sources retained; no cleanup/install/publication action inferred.

# Required verbatim brief quotations

## §2.2

> 2. **Synchronous `authorize`** (host:241–249). **Installed iff the compiled policy is present and its
>    `automaticReplies !== false`** (the resolved enabled form per the design) — host:98 `automaticReplies: false | {timeoutMs}`
>    (explicit 1..60000), host:120/128, H brief :315; the v1 compiler
>    (`static-policy.ts:37–43`) refuses an absent/malformed value, so no default
>    exists and none is invented here. No policy → no B; `false`/admission-only →
>    no B. This install condition is separate from authorize's literal-`true`
>    RESULT rule (cell 6) (host:241 "always installs" for a
>    B-opted policy; `admission-only` in `policy-shapes.json:70–78` has
>    `automaticReplies:false` → `replyAuthority` absent). Checks: original
>    policy/key/account association; exact `{serverId, channelId}` equality with the
>    approved destination (serverId = connection id); current host reply ceiling
>    evaluated **only through `trusted.lookup` with versioned doubles**
>    (`recipient-admission.ts:34–41`). T1:284's scope-generation check is
>    **deferred to T2/I7**. No I/O, promise, lookup beyond that, or mutation;
>    literal `true` only. **Descriptor identity, publish grant and incarnation are
>    AF's** (host:244–246, `valid` at `reply-routing.ts:106,136–139`), not host
>    authorize.

## §2.5

> 5. **Authorised .3 edits** (only these; everything else in .3 unchanged):
>    - add an **optional** `readonly reply?: (key, channelId, scope, input) =>
>      boolean` to `CompiledRecipientAdmissionPolicy`
>      (`src/recipient-admission.ts:24–32`), fail-closed: absent ⇒ no channel is
>      reply-permitted ⇒ resolver `null`, no intent. Optional keeps the frozen v1
>      stub (`test/stubs/f-host-3/v1/static-policy.ts:50–57`, strict) compiling
>      unchanged;
>    - `admit` emits `replyIntent {version:1, channelId}` only for an **eligible
>      automatic reply** (host:222–224): channel-bearing admitted work with
>      `mayWake === true`, a present `reply` predicate returning `true` for that
>      channel, and `automaticReplies !== false`; otherwise no member at all
>      (replacing `:136–137`); inference/channel-less never;
>    - replace the `recipient_reply_authority_pending` throw (`:85`) with the
>      `automaticReplies !== false` install path;
>    - new **v2** stub `test/stubs/f-host-4/v2` (v1 stays frozen and passing):
>      `replyChannels → reply` in the static compiler as the superseded
>      test/compiler input only;
>    - `b-contract.ts` stub pinning `ReplyAuthorityConfig`/`ReplyDestination`
>      copied from the v2 drop with sha citations, plus a typecheck that the host's
>      compiled `replyAuthority` is assignable to it.
>    - **Unchanged:** the production-loading refusal
>      `recipient_policy_integration_pending` (`src/recipient-policy.ts:6`) — .4
>      does NOT lift it; production stays fail-closed until I6.
>    - **Authorised re-baselining** of `test/recipient-policy.test.ts` @ c353e1c8,
>      only: `:203` (title "…full waits for .4", also the witness pattern matched
>      by `run-mutants.mjs:44,49`), `:208` (`toThrow('recipient_reply_authority_pending')`
>      → `full` installs), and the verdict assertions `:213,222,223,233,234,235,281`
>      where the eligibility rule above adds `replyIntent` (each restated with
>      the expected intent or its explicit absence). `run-mutants.mjs:23,24`
>      witnesses are re-pointed to the restated title; `:24` ("full silently
>      offered before B") is retired/replaced by the install-iff mutant. Every
>      other .3 control/mutant must still pass unchanged; the re-baselined cells
>      are re-stated and re-run at the .4 head and listed in the report.

## §5

> ## 5. Stop conditions
> 
> A needed B signature missing from the v2 drop; need for a new
> grant/RefusalCode/wire field; profile-off or `admission-only` differs; any
> current policy-free config changes behaviour; an existing unbound automatic-post
> dependency on a would-be B-opted connection; resolver needs registry access
> beyond the B contract; any .3 edit outside §2.5; anything requiring historical
> 27a32127 semantics.

