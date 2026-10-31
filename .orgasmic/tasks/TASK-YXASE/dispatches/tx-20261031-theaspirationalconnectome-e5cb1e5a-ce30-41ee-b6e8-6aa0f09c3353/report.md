# Local compiler-core implementation report

## Changed

Final local commit **`8df256ccda356d9235f459b4d504a82760d7ed05`**, tree **`70cb266c7ccef8abb9e1df3979512eee38d62b75`**; worktree clean.
Base **`aecd4a8cafe470ad2bbe47b5087c1ec454eef62f`**. No push, PR, merge, install, live integration or `src/` diff.
Author and co-author trailer match the dispatch on all five local commits (`commits.txt`).

- **First commit `cd5edcd8c8ddbc9d55531a7db0ea89da668cfb4c`** contains only the required 49-row allocation and counts, before implementation.
- `docs/design/dynamic-selector-core.md`: complete row table with planned witness, responsible engineering slice, later allocation and evidence label. Later additions explain partial projections and evidence limits; they do not relabel deferred rows as executed.
- `test/stubs/f-host-t2/v1/`: byte-bound compile-only AF signatures, dynamic selector compiler, separate exact-shape facts and parent-authority types, cooperative doubles, typecheck/config, and tests.
- Test controls/runners: pinned-drop byte check, discriminating type negatives and 13 named T2 mutants with zero-selection rejection.
- Checker edit is **input-list-only**: all 10 new files (nine code/config/control files plus the design document) are listed. No detector or oracle semantics changed. `changed-files.txt` lists all 11 affected paths.

Three-level absence, invalid-field/non-subset refusal, joined/readability/account/DM checks, independent selectors, explicit wake/reply read conjunctions, and exact scope/parent binding are implemented in the test-only compiler. No new wire field, production brand constructor, production issuance, parent evaluator or loader schema was added.

### Row accounting: planned versus executed versus deferred

| Class | Rows | Executed meaning |
| --- | ---: | --- |
| Independent host witness | **21** | All planned partial host witnesses executed in the focused suite; not full row/timeline qualification |
| Shared witness | **9** | Mapped to four shared witness families, not nine independent controls; lifecycle semantics remain deferred |
| Witness-only / uncovered | **19** | Real row semantics untested here; responsible slices retained. Parent-denial scaffolding is not a real projector/T4 witness |
| Total | **49** | Allocation, not independent-control count |

Five rows carry **oracle-of-mapping-X** labels: eligibility only, not storage parity. The ten additionally deferred parent rows have only the common missing-parent-authority denial scaffold. Author-access storage, context-only separation and trigger-channel witnesses are explicitly partial projections, not native/D/post-await proofs.

### Coverage boundary

- **stub-of-AF-view:** injected cooperative fact tables and exact-key lookup, not the real AF registration view, currentness, producer authenticity or generation binding.
- **stub-of-projector:** C1–C6 per operation and the two C3 DM-child fixtures (different parent object; other scope). C6 also traverses the actual host compiler seam. `===` establishes only the cooperative model's binding rule; it proves neither authentic parent authority nor unforgeability.
- **Actual code exercised:** the new test-only selector compiler and frozen host `compileRecipientAdmission` pure seam. Existing runner coverage remains compiler-core / stub-of-B. No new real projector, AF-view runtime, native, loader or production issuance coverage is claimed.

## Verification Gates

### Inputs, execution identity and bounds

The full immutable brief was read and its SHA256 verified **before implementation**:
`6482ad0684a699c4c227d6413e5aa46b20b6dd38970263804e68b9ec69e91730`.
Both AF drop files passed `sha256sum -c SHA256SUMS`; the byte-contract gate additionally proves the exact transcribed signatures with only the documented descriptor substitution. No I7 signature was missing.

Every current gate used `orgasmic dispatch gate NAME -- bash "$ORGASMIC_EVIDENCE_DIR/bounded.sh" ...`, as **orgasmic**, in a user scope with **MemoryMax=16G / MemorySwapMax=0**, **TERM timeout 3000s / kill-after 60s**, **32 MiB cap**, retained **PIPESTATUS**, and no retries. `cell.sh` selects the existing root and scrubs configuration. Full-suite flags are exactly **`bun test --max-concurrency 8`** on both current sides; no claim of eight parallel file workers is made. There was no timeout/cap/capture failure: all current capture exits are **0** and no current receipt is INCONCLUSIVE. Producer/outer exits of **1** for the two full suites and docs-contract remain failures.

Runtime/compiler identities (freshly hashed):
- Node **22.23.3**, `/opt/node-v22.23.3-linux-x64/bin/node`, SHA256 `fde6a4bf8d0562f7751d1a2d6cb9b417c4cfe107bbcb0aa3e9a24e125e348f48`.
- Bun **1.4.3**, SHA256 `7ce7d6b654eddeec20afddf2396317fba6dc19ab52788a27e894e1806bf8f24b`.
- Native TypeScript **7.0.2**, SHA256 `4f2de678286401759b3fb4475bafe35b8f32b4b3a07d92642bbf37eadc9b34a4`.

Only the operator-authorized existing root was staged, **base first, then head, sequentially**:
`/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-6jaeq/tmp-recipient/build/head`.

| Consumed source | Commit | Tree | Files | Source manifest SHA256 |
| --- | --- | --- | ---: | --- |
| Current matched base | `aecd4a8cafe470ad2bbe47b5087c1ec454eef62f` | `95aa311cb92a828647cce64d0f78a45b432781dc` | 567 | `c0719664dbb7bdde68b6bd852acacab120ed8824d0673bf228fd2ade67b74bed` |
| Current head | `8df256ccda356d9235f459b4d504a82760d7ed05` | `70cb266c7ccef8abb9e1df3979512eee38d62b75` | 577 | `ee1ceddaacb3b4c46d8fe070882d6f3ff7bf6c27440f59968ff47a309ee03268` |

Both stages have closure SHA256 **`6b30aacfc79d84af1d55e150560f4aa181c40e3767233f28297985e2fdc3f695`** in the current serialization: **18,273 entries = 16,820 file/link entries + 1,453 directories**. Normalized comparison to the historical sealed manifest (SHA256 `6b42678559f175f926db4a39c1527f2415c37bab1392be6a74ae95446233ed64`) finds **zero dependency differences**; all **567** base source hashes also match history. Different serialization hashes are not mislabeled changed dependencies.
Host lock: `7f0e4849a8238b90e9d782ffef558a3e5b80fe6252154d2372f42f9a5c09e020`; web lock: `0617247d4e5a2d86a86886ec98e3dd3464a9250ce8b8b14dd8d7f9fb856fb0c3`.
`base-binding.json`, `head-binding.json`, `*-sources.json`, `*-closure-*.json`, `historical-base-comparison.json`, `head-closed-inputs.json` and `consumed-build.json` retain the consumed source/build/dependency manifests. Final source sets, bytes and dependency entries are unchanged; no host dist/web-dist/out/build/target exists. Test-generated `logs/failures.log` is confined to the execution root; full failed transcripts are preserved in evidence. No tracked lockfile/generated-file side effect occurred.

Base receipts were taken while implementation proceeded in the separate assigned worktree: base-types starts dirty at `cd5edcd8`; base-full starts there and ends at the final implementation head. They are **staged-base observations bound by manifests**, not clean-tree reusable worktree receipts. Head gates bind the final clean commit throughout. Full exact argv, start/end receipt commits, exits, bytes and raw-log names are in **`gate-index.json`**. All log names below are relative to this evidence directory; cell commands run in the staged root.

### Current gate receipts

| Gate | Command after bounded wrapper | Exit (producer / capture) | Receipt log |
| --- | --- | --- | --- |
| `t2-base-full` | `bash $E/cell.sh base bun test --max-concurrency 8` | 1 (1 / 0) | `gate-t2-base-full-01M68BZ849RT17EJSQ43KQF4D3.log` |
| `t2-base-types` | `bash $E/cell.sh base npx --no-install tsc --noEmit` | 0 (0 / 0) | `gate-t2-base-types-01M68BZ7NWJAZ9EGQ3G5MQ21ES.log` |
| `t2-diff-check` | `git diff aecd4a8cafe470ad2bbe47b5087c1ec454eef62f --check` | 0 (0 / 0) | `gate-t2-diff-check-01M68CD2YPE30M8E9ZKZQ9A5GM.log` |
| `t2-head-docs-contract` | `bash $E/cell.sh head node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | 1 (1 / 0) | `gate-t2-head-docs-contract-01M68CF9JP7H755NBNF4Q69BFW.log` |
| `t2-head-drop-bun` | `bash $E/cell.sh head bun test/stubs/f-host-t2/check-view-contract.mjs /srv/review-drop/af-a0970645-view` | 0 (0 / 0) | `gate-t2-head-drop-bun-01M68CF4BSWEW02RGNJ8SEHWN2.log` |
| `t2-head-drop-node` | `bash $E/cell.sh head node test/stubs/f-host-t2/check-view-contract.mjs /srv/review-drop/af-a0970645-view` | 0 (0 / 0) | `gate-t2-head-drop-node-01M68CF46HC611Y4KY5JSXFXCD.log` |
| `t2-head-dynamic-mutants` | `bash $E/cell.sh head node test/stubs/f-host-t2/run-mutants.mjs` | 0 (0 / 0) | `gate-t2-head-dynamic-mutants-01M68CF8VDY3DJZZCR7837BEPX.log` |
| `t2-head-dynamic-negatives` | `bash $E/cell.sh head node test/stubs/f-host-t2/check-compiler-controls.mjs` | 0 (0 / 0) | `gate-t2-head-dynamic-negatives-01M68CF23V0Q8P8CDPW98ZB2PW.log` |
| `t2-head-dynamic-types` | `bash $E/cell.sh head npx --no-install tsc --noEmit -p test/stubs/f-host-t2/v1/tsconfig.json` | 0 (0 / 0) | `gate-t2-head-dynamic-types-01M68CEYM1DGB2894EGF5WVVZ3.log` |
| `t2-head-focused` | `bash $E/cell.sh head bun test test/stubs/f-host-t2/v1/dynamic-policy.test.ts --max-concurrency 8` | 0 (0 / 0) | `gate-t2-head-focused-01M68CF4VP6M5JQPXSFN6RK6VG.log` |
| `t2-head-full` | `bash $E/cell.sh head bun test --max-concurrency 8` | 1 (1 / 0) | `gate-t2-head-full-01M68CFZ71BZZ30YAY9XYZEJCB.log` |
| `t2-head-old-mutants` | `bash $E/cell.sh head node test/stubs/f-host-3/run-mutants.mjs` | 0 (0 / 0) | `gate-t2-head-old-mutants-01M68CF50PPSNX6PM642E0RJ5S.log` |
| `t2-head-reply-mutants` | `bash $E/cell.sh head node test/stubs/f-host-4/run-mutants.mjs` | 0 (0 / 0) | `gate-t2-head-reply-mutants-01M68CF8K9TAQ1NWNG8MKMDYWE.log` |
| `t2-head-root-negatives` | `bash $E/cell.sh head node test/stubs/f-host-4/check-return-controls.mjs` | 0 (0 / 0) | `gate-t2-head-root-negatives-01M68CEYZBKJA7SYSGPF7WPNF1.log` |
| `t2-head-self-bun` | `bash $E/cell.sh head bun test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | 0 (0 / 0) | `gate-t2-head-self-bun-01M68CF9DPWJZ0APWEWWKWCWX1.log` |
| `t2-head-self-node` | `bash $E/cell.sh head node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | 0 (0 / 0) | `gate-t2-head-self-node-01M68CF98N8FN5XP9E4YMBTC9D.log` |
| `t2-head-toolchain` | `bash $E/cell.sh head bash -c 'node --version; bun --version; npx --no-install tsc --version; sha256sum /opt/node-v22.23.3-linux-x64/bin/node "$BUN_BIN" node_modules/@typescript/typescript-linux-x64/lib/tsc; sha256sum package-lock.json web/package-lock.json'` | 0 (0 / 0) | `gate-t2-head-toolchain-01M68CEX2FKFEV0R9GH9WEMTZC.log` |
| `t2-head-types` | `bash $E/cell.sh head npx --no-install tsc --noEmit` | 0 (0 / 0) | `gate-t2-head-types-01M68CEXEWN4DBCDD9RH27KGSG.log` |
| `t2-head-v1-negatives` | `bash $E/cell.sh head node test/stubs/f-host-3/check-v1-compiler-controls.mjs` | 0 (0 / 0) | `gate-t2-head-v1-negatives-01M68CF0GZT6A6MV9DDSB65J62.log` |
| `t2-head-v1-types` | `bash $E/cell.sh head npx --no-install tsc --noEmit -p test/stubs/f-host-3/v1/tsconfig.json` | 0 (0 / 0) | `gate-t2-head-v1-types-01M68CEXX94MF625Q6EY3VED5X.log` |
| `t2-head-v2-negatives` | `bash $E/cell.sh head node test/stubs/f-host-4/check-compiler-controls.mjs` | 0 (0 / 0) | `gate-t2-head-v2-negatives-01M68CF1F1QD1SWJZBAE9DZGPZ.log` |
| `t2-head-v2-types` | `bash $E/cell.sh head npx --no-install tsc --noEmit -p test/stubs/f-host-4/v2/tsconfig.json` | 0 (0 / 0) | `gate-t2-head-v2-types-01M68CEY8PC0JJ3312SAY8PQCG.log` |
| `t2-head-zero-selection` | `bash $E/cell.sh head bash -c 'out=$(node test/stubs/f-host-t2/run-mutants.mjs "^no-match$" 2>&1); s=$?; printf "%s\n" "$out"; test "$s" = 1 && grep -q "selector matched zero mutants" <<< "$out"'` | 0 (0 / 0) | `gate-t2-head-zero-selection-01M68CF93N18119HXRYR0FR6ES.log` |
| `t2-immutable-inputs` | `bash -c <exact brief hash assertion; AF sha256sum -c SHA256SUMS> (full argv in gate-index.json)` | 0 (0 / 0) | `gate-t2-immutable-inputs-01M68CD2SP1X0EWMP9GSB9DDRH.log` |
| `t2-stage-base` | `python3 $E/stage.py base aecd4a8cafe470ad2bbe47b5087c1ec454eef62f` | 0 (0 / 0) | `gate-t2-stage-base-01M68BY6TDJFCC2PCVTZWXP2ZQ.log` |
| `t2-stage-head` | `python3 $E/stage.py head HEAD` | 0 (0 / 0) | `gate-t2-stage-head-01M68CEF7DNFKHY6MDNX4FY9J7.log` |

**23 zero-exit receipts; 3 failed receipts; 0 INCONCLUSIVE receipts.** The failed receipts are base-full, head-full and the expected docs-contract failure. This is not a green gate set.

### Types, focused controls and mutants

- Root, `test/stubs/f-host-3/v1/tsconfig.json`, `test/stubs/f-host-4/v2/tsconfig.json`, and `test/stubs/f-host-t2/v1/tsconfig.json` typecheck successfully. No optional T2 v0 was landed.
- **18 discriminating type negatives**: 5 root return-shape, 3 retained v1, 2 retained v2, 8 T2. Each controlling script asserts the intentional inner compiler failure and diagnostic. T2 includes required→optional accountId (bidirectional fails, keys still equal), added optional member (keys fail, bidirectional still passes), wrong one-way view, boolean authority, boolean member, excess facts member, static fallback field and boolean parent reader. No unused suppression was counted as a witness.
- Focused suite: **49 pass / 0 fail / 153 assertions / 1 file**. This count includes controls and 18 C1–C6 operation cases; it does not mean 49 independent row witnesses.
- Existing mutants: **23 compiler + 17 reply kills**, with names and witnesses in `mutant-kills.json` and their runner receipts.
- New T2 runner: **13 named kills** listed below. Zero-selection control observes inner exit 1 with the required assertion; its outer gate exits 0.

- absent selector allows all: absent selectors deny
- empty list allows: empty list denies
- present empty block becomes feature off: three absent levels
- reply skips full read conjunction: reply evaluates full read conjunction
- reply mirrors read and drops own members: reply keeps own members
- DM matches channel id: DM identity not channel
- child skips sub-conversation conjunct: C1 dynamic-read-sub-conversations-denied
- child parent presence alone: C2 parent presence alone
- parent authority scope binding skipped: C3 exact scope and parent binding
- parent authority parent binding skipped: C3 exact scope and parent binding
- child admits early on child conjuncts: C5 all fact conjuncts
- unknown fact allows: unknown fact denies
- predicate memoises first verdict: predicate is not memoised

The reply-skips-read mutant is an explicitly **structural defence-in-depth kill**, not a distinct behavioral verdict claim: conservative subset checks make reply imply read on stable pure facts in this model. It pins the explicit full-read call without an impure counter/spy. The other T2 kills use behavioral assertions. All mutant-edited bytes were restored and reverified. No mutation was made to the assigned worktree's production sources.

Node and Bun each pass the pinned-view check and oracle self-test. The oracle retains **57 cases, 45 rejection controls, four documentation-oracle mutant kills**, plus singleton/naming-parser controls; these are separate from compiler mutant coverage.

### docs-contract and neutrality

**FAILED**, producer **1**, capture **0**, at checker SHA256
`60c8903662f632adbf94c6ed5e469a633f99e94fda010cd5ffbb561d97665a8e`.
The unchanged baseline checker SHA256 is
`aadccf28b8ed364c907d7e6141090293a5eb0824cfcbba336f6cdf3772a2ef0c`.
The literal input list now contains **25 inputs**, including all **10 new files**. `checker-inputs.json` demonstrates the declared filename FP: the checker source contains two `run-mutants` filename references matched as internal identifiers. No detector fix or EC6WW merge was made. This failure was not reclassified as a pass.

`neutrality-read.md` records the complete manual worker source read, including split/reconstructed strings and the later partial-projection clarifications. No new machine-local paths, internal task/run/conversation identifiers or credentials were found in new tree content or commit messages. This manual read is not an independent privacy review. The literal scanner does not cover reconstructed strings, and no nonempty private forbidden-name policy was supplied. No complete naming/publication qualification is claimed.

### Three distinct full-suite observations

| Observation | Exact consumed source | Pass / fail | Tests / files / assertions | Producer / capture | Duration |
| --- | --- | --- | --- | --- | --- |
| Historical LOWS1 (retained, not rerun) | aecd4a8c | **4622 / 0** | 4622 / 269 / 31857 | **0 / 0** | 101.14s |
| Current matched base | aecd4a8c | **4591 / 31** | 4622 / 269 / 31783 | **1 / 0** | 404.66s |
| Current head | 8df256cc | **4640 / 31** | 4671 / 270 / 31936 | **1 / 0** | 398.57s |

Historical receipt **`historical-lows/gate-low-suite-01M688PYCANNKTVMNP4EEE4FHB.log`**, full capped transcript **`historical-lows/raw.Cj5vvQ.log`**, and its source/dependency manifests, wrappers and binding receipt are retained separately. `historical-receipt-binding.json` identifies them; this is an observation, not a substitute for a current gate.

Current complete capped transcripts: **`raw.hKjv9A.log`** (base, 808706 bounded-log bytes) and **`raw.Ifuc4J.log`** (head, 813498 bytes). Their receipts retain actual pipeline exits. No log was capped or overwritten. The historical cap was 64 MiB; current caps are 32 MiB.

**Per-test comparison:** all **4622 existing test verdicts are identical between current base and head**; exactly **49 new tests pass**. No existing failure was converted to PASS or assigned a cause. Machine-readable per-test inventories and deltas are in `historical-per-test.json`, `base-per-test.json`, `head-per-test.json`, `historical-current-test-deltas.json`, `base-head-test-deltas.json`, and `all-base-failure-outcomes.json`. File, complete displayed name and occurrence are retained to disambiguate names.

`config-comparison.md` compares only a bounded whitelist: tool hashes/versions, flags, timeouts/caps, concurrency, HOME/TMPDIR/PATH roles, CI, Bun preload/update-check, npmrc/cache and Git config. No raw environment or secret dump was made. Historical `--parallel=4` plus tmpfs differs from current `--max-concurrency 8` plus dedicated disk-backed temp directories. These observed differences are **not causal findings**. The historical/current discrepancy is **unexplained**. Shared failures are neither a cause nor a PASS. Reproducibility and qualification remain open.

All 31 names and outcomes follow (historical **PASS**, current base **FAIL**, current head **FAIL** for each):

| # | Test | Historical / base / head |
| ---: | --- | --- |
| 1 | fleet--await > an await already waiting keeps a child it counted when the child is restarted meanwhile | PASS / FAIL / FAIL |
| 2 | fleet--await > an await already waiting drops a restarted child once this host sends the new one work | PASS / FAIL / FAIL |
| 3 | fleet--await > a timeout's report doesn't name as idle a child this host sent work after the await last read it | PASS / FAIL / FAIL |
| 4 | FleetModule lifecycle > real headless relaunch survives the stale socket left after ready failure | PASS / FAIL / FAIL |
| 5 | FleetModule lifecycle > unconfirmed termination keeps the child blocked and reports the cleanup failure | PASS / FAIL / FAIL |
| 6 | a fleet parent lets go of a child's socket that resets | PASS / FAIL / FAIL |
| 7 | npm pack's JSON is read in npm 12's shape too, and a prepack's output doesn't break it | PASS / FAIL / FAIL |
| 8 | an install that fails once and then succeeds passes on its second attempt | PASS / FAIL / FAIL |
| 9 | an install that runs out of time, even one that ignores SIGTERM, fails at once, saying so, without another attempt | PASS / FAIL / FAIL |
| 10 | npm pack that runs out of the install's time, even ignoring SIGTERM, fails, saying so | PASS / FAIL / FAIL |
| 11 | npm pack that fails, or prints anything but one record with a filename and its files, fails by name | PASS / FAIL / FAIL |
| 12 | a client whose socket is reset stops being current, and its disconnect is logged once | PASS / FAIL / FAIL |
| 13 | a fleet child's agent overlay > a child launched into its data directory before starts from a copy of the working directory's overlay, once, its owner's alone, and keeps it as its own | PASS / FAIL / FAIL |
| 14 | a fleet child's agent overlay > a child launched into its data directory before, with no overlay in the working directory, starts from an empty one, which a later launch keeps | PASS / FAIL / FAIL |
| 15 | a fleet child's agent overlay > a child whose data directory is the working directory, by any spelling, keeps its own overlay there beside the host's, starting from a copy of it, and restarts in place | PASS / FAIL / FAIL |
| 16 | FleetModule — unresolved launch artifacts > persisted ready child killed by parent exit recovers through second-parent autoStart | PASS / FAIL / FAIL |
| 17 | FleetModule — unresolved launch artifacts > historical crashed record recovers through explicit launch | PASS / FAIL / FAIL |
| 18 | FleetModule — unresolved launch artifacts > adopted-child restart records confirmed death, escalation=false | PASS / FAIL / FAIL |
| 19 | FleetModule — unresolved launch artifacts > adopted-child restart records confirmed death, escalation=true | PASS / FAIL / FAIL |
| 20 | FleetModule — unresolved launch artifacts > reaped same-owner crash supports manual launch | PASS / FAIL / FAIL |
| 21 | FleetModule — unresolved launch artifacts > reaped same-owner crash supports restart | PASS / FAIL / FAIL |
| 22 | FleetModule — unresolved launch artifacts > reaped same-owner crash supports autoRestart | PASS / FAIL / FAIL |
| 23 | FleetModule — unresolved launch artifacts > confirmed graceful stop reconciles the tracked generation's stale artifacts | PASS / FAIL / FAIL |
| 24 | FleetModule — unresolved launch artifacts > confirmed forced kill tool reconciles the tracked generation's stale artifacts | PASS / FAIL / FAIL |
| 25 | FleetModule — unresolved launch artifacts > confirmed forced stop reconciles the tracked generation's stale artifacts | PASS / FAIL / FAIL |
| 26 | FleetModule — unresolved launch artifacts > stop reconciles an already crashed tracked generation | PASS / FAIL / FAIL |
| 27 | FleetModule — unresolved launch artifacts > stop reconciles an already exited tracked generation | PASS / FAIL / FAIL |
| 28 | FleetModule — unresolved launch artifacts > confirmed forced death of an adopted generation reconciles its stale artifacts | PASS / FAIL / FAIL |
| 29 | FleetModule — unresolved launch artifacts > stop retries adopted terminal cleanup after an inconclusive socket probe | PASS / FAIL / FAIL |
| 30 | outside a git checkout a pack goes ahead, saying it can't tell what git ignores | PASS / FAIL / FAIL |
| 31 | outside a git checkout an npm publish stops, saying why | PASS / FAIL / FAIL |

### Stops, holders and operator teardown

No §6 production/source/API stop was crossed. Production issuance, parent evaluation, ref/current/epoch barriers and loader work remain allocated, not invented here.

1. Before base staging, operator Tarn supplied root witness 1 (06:54:13Z, 370 processes, zero hits/errors; SHA256 `66982fc26616ce2ab8212ac14e3209bd9561a079d3447e122cb316aa0e7154fb`). Own immediately-prestage visible scan at 06:56:45Z found zero head-root hits. Source staging followed the operator's sequential-root authorization.
2. **Base suite leaks three orphaned `mock-headless-child.ts` instances** after the suite ends: **892709 / 892747 / 893064**, start ticks **31262496 / 31262530 / 31262910**, PPID 1830, scope **run-u71373.scope**, argument `mock-recipe --headless`. Own 07:04:16Z scan found them. **STOP: no head staging, no worker kills.** `base-survivors.json` preserves identity/cgroup evidence.
3. **Separate operator teardown disposition by Tarn**, not a general kill-holders rule: Tarn checked pid/start/cgroup together, confirmed that scope contained only those three mocks, sent three TERMs, and found none after 5 seconds. Witness 2 (07:05:11Z, 397 processes, zero hits/errors; SHA256 `96453ba907ca9fb67f2412ebfba0c154497e553bf1fe298f69b2e0d84f57b3be`) precedes own 07:05:38Z clear scan and head staging.
4. **Head suite leaks the same three-mock pattern/mechanism**: **929169 / 929207 / 929543**, ticks **31317287 / 31317322 / 31317705**, PPID 1830, scope **run-u71397.scope**. Own 07:13:13Z scan found them. **STOP again: no additional cells, no worker kills or wider cleanup.** `head-survivors.json` preserves identities and `head-postsuite-visible.json` records hits.
5. **Separate head operator teardown disposition by Tarn:** checked the three identities and sole scope membership, sent TERMs, none remain after 5 seconds. Witness 3 (07:14:05Z, 399 processes, zero hits; SHA256 `6b24f64566b85a5d0d7937aad33b38b7da2fa3389b0f5cd1387f19621dc6918b`). Own final visible scan at 07:14:59Z found zero head-root hits. No additional test cells were run.

All three witness files are copied into evidence. Root observations are point-in-time, not locks or worker visibility. Own scans retain permission errors, including known same-user services 1830/1907/3182 and inaccessible root processes; they do not claim complete visibility. All three blocking asks returned stdout dispositions and ended. No worker-initiated TERM, speculative fixture fix, retry, timeout increase, new dependency acquisition or wider process cleanup occurred.

## Unmet Criteria

- **A clean full-suite pass is not established:** current base and head each fail the same 31 existing tests. Historical LOWS1's 4622/0 is retained, not erased or used to override current failures. Its reproducibility discrepancy is unexplained.
- **docs-contract remains FAILED**, the declared expected filename false positive. Naming/privacy/publication qualification is not complete.
- **19 rows remain witness-only/uncovered by design**, plus all retained T3/T4/T5/projector/I6/loader portions. No real-loader, projector, AF-view-runtime or complete-T2 qualification is supplied.

The requested local test/stub compiler artifacts and planned gate executions are complete; the above failures and qualifications remain open rather than being relabeled successful.

## Residual Risk

The injected readers are cooperative doubles. Exact object equality does not prove issuance, authenticity, parent native agreement, currentness or unforgeability. The separate view model is compile-only and descriptor-widened, so only its stated one-way type assertion is justified. No `mappingGeneration` comparison, ref/current logic, descriptor/ACK authority or new production constructor was added.

Real parent read-scope/native agreement belongs to the projector; native truth/effects to T3; ref/current/replacement/revocation/post-await barriers to T4; storage parity to T5; production fact issuance/binding to I6; real feature-off config/overlay/reconnect/unconfigured-peer controls to the H loader integration. Existing activation blockers, I6/I7 qualification, D and LOCAL ONLY/private-ancestry publication holds are unchanged. Tarn operated the task and Soma qualified its scope; neither is represented as an owner decision.

Evidence holds logs, manifests and small scripts only (about 27 MiB before this report), not build caches. Existing build/state roots remain untouched except the explicitly authorized source staging and test state. The shared mock leaks and all 31 failures are findings, not repaired in this slice. No release, activation, completeness or integration claim is made.
