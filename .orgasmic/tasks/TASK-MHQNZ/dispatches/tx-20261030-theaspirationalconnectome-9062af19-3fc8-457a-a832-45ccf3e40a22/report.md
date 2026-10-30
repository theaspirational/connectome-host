# TASK-MHQNZ — r6 PREP delta review (bounded, read-only)

Range: `1cfe32a1c381a2d67731a6c2b8e3989874947df9..0cf9edff7810b3834cd5fc5bcde8be8c66364a0a`
(10 files, all under `test/store-probe/`; +225/-63). Worktree left clean (`git status --porcelain` empty).
Context read: `/srv/review-drop/TASK-MHQNZ-PHASEE-1.md`, `/srv/review-drop/TASK-MHQNZ-R5-DELTA-REVIEW.md`,
implementer evidence `run-01M67HJQMANFVMGDKR7G59MZGH/evidence`.

## Verdict

**APPROVE** — the r6 PREP is correct against the binding Soma conditions. Every condition in the
brief is discharged in the code and corroborated by the recorded capture. Two MEDIUM findings are
gate-durability/latent gaps (the condition holds for *this* round but is not pinned as a regression
barrier), four LOW. None is an unmet acceptance criterion, none is HIGH.

The verdict is about the correctness of the PREP. It is **not** approval to execute: the
recomputed budget correctly concludes STOP, and I independently reproduced that arithmetic.

## Findings

### MEDIUM-1 (test/gate) — the "no store events / no store paths" verification for import-preflight lives only in an untracked evidence script, not in the committed gate
`test/store-probe/check-prep.py:77-82` is the whole committed preflight assertion: caseIds match
`manifest.capture.importPreflightCases`, exactly 12 `instrumentation-ready` events, all descriptors
`facadeWrapped`. It asserts **nothing** about the absence of store activity, yet
`check-prep.py:140` prints `"... No stores opened."`.

The assertions that actually discharge brief condition (3) are in
`$EVIDENCE/import-readiness-check.py:20,21,47` — `exitStatus==0`, exactly one
`import-preflight-complete`, zero `api-enter/api-return/api-throw/open-enter/open-return/rows-exposed/application-rows-exposed`,
and `assert not store, "store paths in import-only trace"`. That file is **not tracked**
(`git ls-files | grep readiness` → empty), hardcodes the anchor `bdf4e438…` and
`$ORGASMIC_EVIDENCE_DIR`, and is never invoked by `check-prep.py`.

No selftest pins the ordering either. Mutant **c3** — delete the early exit at
`worker.mjs:87-91` so an `import-preflight` child falls through into the store path at
`worker.mjs:92+` — leaves `node --test selftest.mjs` green at **23/23**. The analogous
`prep-assertions` ordering *is* pinned, at `selftest.mjs:183`.

Impact: condition (3) is verified for r6 but is not a barrier; the next round can lose it silently
while the committed gate still prints "No stores opened."
Fix: move the `import-readiness-check.py:20,21,47` assertions into `check-prep.py`'s
`elif prefix == 'import-preflight'` block, and add a source-ordering selftest for
`operation === 'import-preflight'` mirroring `selftest.mjs:183`.

### MEDIUM-2 (correctness) — the preflight branch emits `final('complete')` unconditionally, unlike the native branch
`probe.mjs:173` — `evidence.final('complete', {...})` with no inspection of per-case
`classification` or `exitStatus`. The native branch guards exactly this at `probe.mjs:227-229`
(`failed → 'control-failure-inconclusive'`, `process.exitCode = 1`).

Failure scenario: a preflight child whose classification lands outside the `stopNative` set
(`probe.mjs:127`) — e.g. `'refused-unclassified-REVIEW-TRACE'` from `probe.mjs:44` on a nonzero
exit, or `'trace-filtered-uncertain-NOT-PASS'` from `probe.mjs:128` once the stream crosses
192 MiB — is pushed into `results`, `completed++` increments regardless (`probe.mjs:171`), and
`final('complete')` is emitted. `stream.mjs:78-82 checkCompleteness` accepts it (it inspects only
final status, plan, caseIds, counts and the receipt), so `check-prep.py:72-73` sees returncode 0 /
`COMPLETE` and the gate passes with a refused child inside.

Not an actual misreport for r6: all 12 recorded cases have `exitStatus: 0` and
`storePathReferences: 0` / `apiCalls: 0` (verified from `import-readiness.json`). This is latent.
Fix: mirror `probe.mjs:227-229` in the preflight branch.

### LOW-1 (test) — `probe.mjs:36`'s second disjunct is dead code, and the invariant that makes it dead is untested
`probe.mjs:36`: `(e.kind === 'api-return' && e.isMutation && !e.completion)`. `observer.mjs:35`
*always* stamps `completion: 'returned-only; async settlement NOT inferred'`, and
`observer.mjs:35` is the only emitter of `api-return` in the harness (grep confirms), so the clause
can never fire. Nothing tests that invariant: mutant **m6** (drop `completion` from
`observer.mjs:35`) leaves selftest green at **23/23** and would silently make
`apiMutationCompleted` true from a mere synchronous return with no settlement.
`selftest.mjs:223` supplies `completion` itself, so it cannot catch this.

Related, same line: `observer.mjs:30`'s `isMutation` regex
`/^(register|update|set|append|create|switch|fork|compact|delete|remove|sync)/` does **not** match
`addMessage` — a real store write, called twice at `worker.mjs:114-115`, labelled
`isMutation: false` in the streamed evidence a reviewer is instructed to read.
Fix: assert in selftest that every observer `api-return` carries `completion`; add `add` to the
mutation prefixes (or classify from the `intendedApi` table at `observer.mjs:2-7` rather than a
name regex).

### LOW-2 (correctness) — the trace filter degenerates when the store argument is the `-` sentinel
`probe.mjs:48` `traceDecision` keeps a line if `line.includes(store)`. For preflight,
`probe.mjs:170` passes `store = '-'`, so once filtering engages
(`probe.mjs:96`, at ≥192 MiB) nearly any strace line carrying a `= -1 ENOENT`-style result matches
and is retained — the filter degrades to a near-no-op precisely when it is needed.
Direction is fail-closed (bloats toward the 256 MiB cap rather than hiding lines), and r6's
preflight never filtered (`filtered:false`, `droppedLines:0`, asserted at
`import-readiness-check.py:24`), so it did not affect this prep's numbers.
Fix: skip the `includes` disjunct when `store === '-'`.

### LOW-3 (correctness) — `sync` is not awaited before `reader-write-complete`
`worker.mjs:138`: `observer.call('JsStore', store, 'sync'); event('reader-write-complete');`
Open/create (`worker.mjs:95,102`) and close/stop (`worker.mjs:142`) *are* awaited. So
`apiMutationCompleted: true` (`probe.mjs:36,39`) can be emitted on return-only, not settlement.
Over-claim direction, i.e. fail-closed for the compatibility hypothesis — but inconsistent with
the "`api-return` means RETURNED ONLY" discipline the README applies everywhere else
(`README.md:329-330`). Same at `worker.mjs:120` for the seed `sync`.

### LOW-4 (docs) — returned-value property reads are the one unobserved SDK touch and are not named in the boundary paragraph
`worker.mjs:125-133` reads `rows.length`, `row.payload`, `messages.length` directly. These are
property reads on returned data, not method invocations, so the "every SDK call goes through the
observer" condition holds — but `README.md:331-337` names only "SDK-internal/native calls" and
"factory-internal constructors" as outside the boundary. If any of those are accessor properties on
an SDK class they are unobserved reads. Documentation precision only.

## What I verified affirmatively

**(1) Binding conditions in the code.**
- *Observer only at harness call sites.* `observer.mjs:38-41` is a free-standing factory; it is
  invoked only from `worker.mjs`. Nothing is installed on an SDK object.
- *No defineProperty/assignment/Proxy over SDK/native.*
  `git grep -nE 'defineProperty|new Proxy|Object\.assign|setPrototypeOf|__proto__|require\.cache|Module\._|createRequire|NODE_OPTIONS|--import|--require|--preload|registerHooks|module\.register' -- test/store-probe/`
  returns only `selftest.mjs:192,198`, which build the *test fixture* (frozen method + getter).
  `observer.mjs` uses exclusively read-only reflection: `Object.getOwnPropertyDescriptor` /
  `getPrototypeOf` (`observer.mjs:10`), `Reflect.get/apply` (`:39`), `Reflect.construct` (`:40`).
- *No SDK/loader/condition/export patching; no reader substitution.* `worker.mjs:41-43` uses the
  runtime's own resolver (`Bun.resolveSync` / `import.meta.resolve`) and asserts the result equals
  `packageEntry(root, meta, spec.condition)` derived from the package's own `exports` map
  (`runtime.mjs:24-28`); `worker.mjs:49,62` reject a wrong conditional entry or a mixed tree.
  Imports are plain dynamic `import(pathToFileURL(entry))` (`worker.mjs:77,80,82,83`).
- *Receiver / arguments / return / promise identity / throw preserved.* `observer.mjs:39-40`:
  `Reflect.get(receiver, method, receiver)` (getter invoked once, correct `this`), then
  `Reflect.apply(fn, receiver, args)`; `Reflect.construct(C, args, C)` keeps `new.target`.
  `invoke` is deliberately **not** async (`observer.mjs:26-27,33-36`) and rethrows the identical
  error object. Proven discriminating — see (2).
- *Every SDK call through observer.call/construct.* 14 call sites
  (`worker.mjs:95,96,97,100,102,103,104,107,108,114,115,118,119,120,124,131,137,138`); all go
  through the observer. `grep -nE '\b(JsStore|ContextManager|AgentFramework|Membrane|store|cm|framework|membrane)\s*\.\s*[A-Za-z_$][A-Za-z0-9_$]*\s*\(' test/store-probe/worker.mjs`
  → **no matches**; `grep -n '\bnew \b' test/store-probe/worker.mjs` → **no matches**.
  The `intendedApi` table (`observer.mjs:2-7`) is exactly the set of call sites — all 16 declared
  methods are used, none undeclared. Descriptor counts are consistent: 7 (chronicle) / 12 (cm) /
  11 (af) per child, deduping to 16 per runtime×tree × 4 = **64**, exactly the figure asserted at
  `import-readiness-check.py:49`.
- *`refusalBeforeRowExposure` / absent-exposure cannot become true from the absence of events.*
  `probe.mjs:41` is `exposed ? false : null` — structurally never `true`.
  `applicationRowsPossiblyExposed` and `apiMutationCompleted` are `true | null`, never `false`
  (`probe.mjs:39`); `refusalBeforeMutation` is always `null` (`probe.mjs:42`). This was the r5→r6
  fix and it is the right one. Mutation-tested — see (2).
- *No silent skips.* `observer.mjs:11` throws `INCOMPLETE` on a missing/non-callable descriptor,
  `observer.mjs:21` on a missing class. A failed observation write (`worker.mjs:15-17`,
  `fs.writeSync` on fd 4) throws, propagates past both the `catch` and the `finally`, so no
  `finished` event is emitted → `probe.mjs:126` forces
  `instrumentation-inconclusive-NOT-PASS`. Fail-closed.
- *Boundary written down honestly.* `README.md:330-337` ("API boundary is ONLY calls made by this
  harness", "Missing API events/hooks can NEVER mean no read/no mutation", "only observed exposure
  can make the latter false, never true from missing hooks") and `README.md:348-351`. The boundary
  is also stamped into the evidence itself: `observer.mjs:15,31,35`, `worker.mjs:86`.
- *Import-preflight has no store constructors/I-O.* `worker.mjs:75` refuses any non-`-` store path;
  `worker.mjs:87-91` exits before the store block at `worker.mjs:92+`; `probe.mjs:170` passes `'-'`.
  `checkApiSurface` inspects descriptors without constructing or invoking anything
  (`observer.mjs:17-24`). Corroborated empirically across all 12 cases:
  `storePathReferences` 0, `apiCalls` 0 (`import-readiness.json`).
- *Initializer I/O recorded rather than denied.* `initializer-io.jsonl` holds raw syscall lines for
  **3,751** write/mutation, **114** socket-reference and **3,935** outside-input-path syscalls inside
  the import window (`import-readiness-check.py:41-46`), per-case in `import-readiness.json`.
  `README.md:352-355` states imports are not assumed side-effect-free and that absence from the
  selected syscall classes is not proof of no network activity. Correct and not over-claimed.

**(2) Are the synthetic tests discriminating? Yes — re-derived 10 mutants, 8 killed.**
Baseline `node --test test/store-probe/selftest.mjs` → 23/23 pass. Each mutant was applied to a
copy outside the worktree.

| mutant | change | result |
|---|---|---|
| m1 | `invoke` → `async function` (wraps the promise in a new one) | **killed** (tests 20,21,22) |
| m2 | `Reflect.apply(..., undefined, args)` (loses receiver) | **killed** (test 20) |
| m3 | read the getter once extra before `invoke` | **killed** (test 20) |
| m4 | rethrow `new Error(String(error))` instead of the same object | **killed** (test 21) |
| m5 | `Object.defineProperty(receiver, method, …)` before invoking | **killed** (test 20) |
| m7 | swallow the throw, `return undefined` | **killed** (test 21) |
| c1 | `refusalBeforeRowExposure: … (failure ? true : null)` | **killed** (test 23) |
| c2 | `applicationRowsPossiblyExposed: exposed` / `apiMutationCompleted: mutated` (false instead of null) | **killed** (test 23) |
| m8 | `return result instanceof Promise ? Promise.resolve(result) : result` | survived — **semantically equivalent** (`Promise.resolve` on a native promise returns the same object), not an escape |
| m6 | drop `completion` from the `api-return` event | **survived → LOW-1** |
| c3 | delete the `import-preflight` early exit (`worker.mjs:87-91`) | **survived → MEDIUM-1** |

The brief's two named mutants (promise re-wrapping; swallowing a getter / receiver) are both caught.

**(3) Import-preflight exits before store code; gate coverage.** Code: confirmed
(`worker.mjs:75,87-91`; `probe.mjs:170`). Gate: the real check exists but only in the untracked
evidence script — **MEDIUM-1**; and the preflight branch declares COMPLETE unconditionally —
**MEDIUM-2**.

**(4) Budget recomputation — arithmetic, weighting and STOP all correct.** Independently
recomputed from `import-preflight.stdout.jsonl` (57,371,552 B over 12 labels), not read from the
implementer's JSON:

- Weighting: per runtime×route a child runs 1 seed + 1 verify per tree, plus 3 clone attempts whose
  reader trees are old / new / old (`runtime.mjs:6-10`) → **old 4, new 3**, total 7
  (`runtime.mjs:13`: `2 × 3 × 7 = 42`). Sum of the 12 case weights = **42**. ✔
- `measuredImportTransportAll42Bytes` = **198,285,819 B = 189.10009 MiB** — reproduced exactly.
- `importAll42Lines` = **268,365** — reproduced exactly.
- `+16 MiB` non-trace allowance → **215,063,035 B = 205.10009 MiB**, which is `≥ 192 MiB`
  (201,326,592) and `< 256 MiB` (268,435,456). Both comparisons reproduced. ✔
- **The STOP conclusion is correct and honest.** Crossing the 192 MiB *filter* threshold sets
  `filtered = true` (`probe.mjs:96`), which produces `droppedLines > 0` and therefore
  `trace-filtered-uncertain-NOT-PASS` (`probe.mjs:128`) — so the run is inconclusive before the
  256 MiB cap is even reached. The qualifiers are accurate: `storeWorkMeasured: false`, store work
  is strictly additive, and the note states preflight label/operation lengths differ.
- Honesty of the model: the 16 MiB non-trace allowance is the softest number, but it is ~250× the
  observed non-trace parent overhead (0.057 MiB of `plan`/`artifact`/`case-result`/`trace-filter-stats`/`input-tree-verified`
  frames across 12 cases) — generous, not optimistic. I specifically checked the one way this could
  have been badly underestimated: `verifyInputTree` returns only counts/bytes/an aggregate hash
  (`verify-inputs.mjs:39-40`), so the 42 per-child `input-tree-verified` frames are ~482 B each,
  not per-file manifests of the 10,628/7,149-file trees. The `operation` string-length error also
  runs conservative (`import-preflight` is the longest operation label).

**(5) Payload inventory vs R5 — only the harness changed.** Independent structural diff of manifest
`3b4f6aa9…` (`run-01M67H32GBA8TE7ZXCZNX9ZC2N`) against `bdf4e438…`:
- Same 22 input labels, same top-level keys.
- `[harness]`: 19 → 20 files, 121,640 → 134,674 B. **+`observer.mjs`** (sha `43eb43d6cacc…`, 3,034 B)
  and 9 modified: `README.md`, `check-prep.py`, `launch-argv.mjs`, `launch.sh`, `prepare.py`,
  `probe.mjs`, `selftest.mjs`, `stream-check.mjs`, `worker.mjs` — **exactly** the 10 files in the
  commit, and `check-prep.py:40` pins the manifest harness inventory to the actual directory listing.
- `capture.importPreflightCases` added (12 IDs); no other `capture` key changed — notably
  `expectedChildOperations` stays 42 and `expectedCases` stays the 18 native IDs.
- Three `operatorPath` strings re-pointed to the r6 directories (`new-r5-final` → `new-r6-final`;
  the two tree-assertion files to the r6 evidence dir) with **byte-identical contents** — same
  sha256, same size (new-runtime 7,149 files / 88,620,916 B; old/new-tree-assertions 2,338,095 /
  1,587,740 B).
- **All 19 other payload inventories byte-identical**, including `old-runtime` (10,628 files /
  204,435,070 B), `bun-old-runtime`, `bun-new-runtime`, `node-runtime`, `sealed-cache` (145 files),
  `af030-driver-not-D`, `strace`, all four `trace-lib-*`, and every oracle/lock input.
  This matches `payload-inventory-comparison.json` exactly.

**(6) Hygiene.** Author = committer = `Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>`;
trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`. Only `test/store-probe/` touched.
`git grep -nE '/(srv|home|opt)/' -- test/store-probe/` → **none**, and `check-prep.py:36` enforces
that for every harness file. No added line contains a private path. Worktree clean.

## Verification gates

Reused receipts (read, not re-run — all at `0cf9edff`, clean tree):
`phase-p-r6-check-prep-committed` exit 0; `phase-p-r6-deadline-negative-committed` exit 0;
`phase-p-r6-import-preflight-committed` exit 0; `phase-p-r6-kill-committed` exit 0;
`phase-p-r6-noop-committed` exit 0; `phase-p-r6-readiness-and-budget-committed` exit 0
(log `gate-phase-p-r6-readiness-and-budget-committed-01M67J1GYS040N7WNH67P2AXFM.log` — I read its
full output and reproduced the budget numbers independently).

Scoped probes I ran (read-only, no containers, no gate re-runs):
- `node --test test/store-probe/selftest.mjs` → 23/23 pass (baseline).
- 11 mutants against out-of-tree copies (table above); 8 killed, 1 equivalent, 2 escapes → findings.
- Independent budget recomputation from `import-preflight.stdout.jsonl` (weights, bytes, lines,
  threshold comparisons) — matched `budget-recomputed.json` to the digit.
- Independent manifest structural diff r5 vs r6 (labels, per-file sha/size/mode, operatorPaths).
- Frame-type byte census of the preflight stream (54.71 MiB total: 54.521 MiB `syscall-trace`,
  0.135 MiB `api-event`, 0.057 MiB everything else) to test the 16 MiB allowance.
- `initializer-io.jsonl` category census (3,751 / 114 / 3,935; 1,841 distinct outside-input paths).
- Greps for direct SDK invocation, `new` expressions, and forbidden mutation/loader primitives.

Not run, by design: no container launch, no Phase E, no native matrix, no limits re-proof.

## Unmet criteria / test gaps

Phase P acceptance (input-manifest sha256, harness sha256, image ID + digest, full launch argv,
whole-command deadline, limits proof) is met and unchanged from r5 — the r6 delta adds the
import-preflight capture and the measured budget. No acceptance criterion is unmet.

Gaps, all named above:
1. No committed assertion that the preflight capture contains no store events/paths (MEDIUM-1).
2. No selftest pinning the `import-preflight` early exit; the analogous `prep-assertions` ordering
   *is* pinned at `selftest.mjs:183` (MEDIUM-1, mutant c3).
3. Preflight completeness does not consider per-case classification/exit status (MEDIUM-2).
4. No test that observer `api-return` events always carry `completion` (LOW-1, mutant m6).
5. No test for the `'-'`-sentinel filter degeneration (LOW-2) — untriggered in r6
   (`filtered:false`).
6. `checkApiSurface`'s refusal paths (`observer.mjs:11,21`) are exercised only indirectly; no test
   feeds a missing class or a non-callable descriptor.

## Residual risk

- Store-phase trace volume remains **unmeasured**. The prep says so explicitly
  (`storeWorkMeasured: false`) and does not convert the import measurement into an experimental
  bound. This is the correct carry-forward of r5 MEDIUM-1, now quantified rather than guessed.
- The API observation boundary is genuinely narrow: SDK-internal and native calls are invisible at
  the API layer and are only reachable through syscalls. The code and README both say this; no
  conclusion in the harness depends on API-event absence. Residual risk is that a *reader* of the
  evidence over-reads the boundary — mitigated by the per-event boundary stamps.
- `import-readiness-check.py` is a one-off evidence artifact pinned to `bdf4e438…` and
  `$ORGASMIC_EVIDENCE_DIR`; it is not reproducible from the repository (MEDIUM-1).
- The 16 MiB non-trace allowance is a judgement, not a measurement — but it is ~250× the observed
  parent overhead, so the STOP conclusion is robust to it being wrong by two orders of magnitude.
- `isMutation` mislabels `addMessage` in the streamed evidence (LOW-1). Currently inert for
  classification because the consuming clause is dead, which means the mislabel is only a
  reading hazard — until that clause is revived.

## Fix directions

1. Move `import-readiness-check.py:20,21,47` into `check-prep.py`'s preflight block so "No stores
   opened." is actually enforced by the committed gate; add an `operation === 'import-preflight'`
   source-ordering selftest mirroring `selftest.mjs:183`.
2. Mirror `probe.mjs:227-229` into `probe.mjs:173` — flip to `control-failure-inconclusive` and a
   nonzero exit if any preflight case classification matches `/NOT-PASS/` or `exitStatus !== 0`.
3. Add a selftest asserting every observer `api-return` carries `completion`, and either delete the
   now-dead `!e.completion` disjunct at `probe.mjs:36` or make it load-bearing deliberately.
4. Classify read/mutation from the `intendedApi` table (`observer.mjs:2-7`) instead of a name regex,
   which removes the `addMessage` mislabel by construction.
5. Guard `probe.mjs:48` against the `'-'` sentinel (skip the `includes` disjunct when
   `store === '-'`).
6. For symmetry with `open`/`close`, await the `sync` calls at `worker.mjs:120,138` before emitting
   `seed-complete` / `reader-write-complete`, or rename those events to make return-only explicit.

---

# Separate section: technical options for viability within the UNCHANGED envelope

**INFORMATION ONLY.** None of this is a recommendation to run Phase E, to raise the 192 MiB filter
threshold or the 256 MiB cap, or to alter the Soma envelope. Each option is stated with its
evidentiary cost, because every one of them buys headroom by giving something up. The measurement
that makes this section meaningful is r6's own: the **SDK import phase alone** is 189.10 MiB
weighted over 42 children — the store work is not the dominant cost, the imports are.

## Option A — split the matrix into several separate launches, each with its own capture

Shape: instead of one container streaming all 42 children, run N launches (e.g. 2, one per runtime,
21 children each; or 6, one per runtime×route, 7 children each), each with its own
`launch.sh` invocation, its own stdout redirection, its own host receipt, argv JSON, inspect and
hash chain.

Pros: the budget is per-stream. At 6 launches the import cost drops to ≈31.5 MiB per stream,
leaving ≈160 MiB of headroom per stream for store work — the only option here that leaves real room
for the unmeasured store phase. The envelope (`4GiB/2CPU/256 PIDs`, core 0, `network none`,
read-only root, cap-drop ALL, no RW mounts), the 90 s child deadline and the 1800 s whole-command
deadline are all untouched; the per-launch whole-command deadline becomes *easier* to meet. A
failure in one launch does not void the others.

Evidentiary consequences:
- `manifest.capture.expectedCases` and `stream.mjs:77-83 checkCompleteness` are built around one
  declared case set per stream, so the manifest must declare per-launch case sets and the checker
  must be told which launch it is verifying. Completeness becomes N local claims plus a host-side
  argument that all N ran — it is no longer one chain.
- The master→clone invariant breaks across container lifetimes. `probe.mjs:186-195` seeds a master
  in `/work` (tmpfs) and seals it; `probe.mjs:217` asserts
  `deepEqual(stable(masterAfter), masterSeals[writer])`. If `old-to-old` and
  `old-reader-to-new-store` land in different launches they no longer share a byte-identical master
  unless the master inventory is pinned by hash across launches and re-seeded identically — which
  itself becomes a claim needing proof (seeding is not guaranteed byte-reproducible).
- N separate host receipts/argv/inspects to approve, and N separate exact-argv decisions if approval
  is per-argv. That multiplies the qualifier's decision surface, which is the opposite of what the
  current single-argv-sha approval model was designed for.

## Option B — reduce the trace classes to the store phase

Shape: today `probe.mjs:65-67` traces each child end-to-end with
`-e trace=%file,%desc,%memory,%process`. Two sub-variants: (B1) keep the full expression but gate
*retention* on phase, dropping lines emitted before `instrumentation-ready` from the stream; (B2)
narrow the expression, most obviously by dropping `%memory`.

Pros: attacks the actual 189 MiB directly rather than redistributing it, and leaves store-access
evidence at full fidelity. B1 is the better-targeted of the two: the import window is already
delimited in the stream by the `native-payload` → `instrumentation-ready` wall timestamps
(`import-readiness-check.py:23,36`), so the boundary is mechanically identifiable, and the dropped
region is exactly the region r6 just characterized.

Evidentiary consequences:
- B1 forfeits per-native-child initializer-I/O evidence. The claim "imports are NOT assumed
  side-effect-free" (`README.md:352-355`) would then rest on the r6 *preflight* rather than on the
  native run itself — a different and weaker provenance, since the preflight child never has a
  store path in its environment. The 3,751 write and 114 socket-reference syscalls r6 recorded
  during import are precisely what would stop being captured where it matters.
- B1 also needs care not to become "a hash of discarded bytes", which Soma's revision-2 condition
  explicitly rejects. Dropping a *declared phase* and saying so is not the same as hashing away
  trace bytes, but the report would have to be unambiguous that the import phase of the native run
  is unobserved at the syscall layer.
- B2 (`%memory`) is the more damaging. Soma's revision-2 condition named mappings/msync among the
  classes needed to correlate exposure/mutation with the refusal point. Without `%memory`, an
  `mmap`-based writer is invisible at the syscall layer, and
  `refusalBeforeMutation` — already deliberately `null` and requiring manual raw-syscall
  correlation (`probe.mjs:42`) — becomes unanswerable rather than merely manual.

## Option C — per-child capture

Shape: one capture sink per child rather than one parent stream, so the 256 MiB cap applies
per child (worst observed per-child framed cost: 767,795 B; the 189 MiB figure is a 42-child sum).

Pros: the cap stops binding entirely, and a killed child's partial prefix is isolated from the rest.

Evidentiary consequences: this is the option whose compliant form is most constrained. Soma's
revision-2 condition forbids RW volume mounts and rejects `podman cp` from the `/work` tmpfs, so
there is no host-side per-child sink inside a single container — `/work` evaporates on stop. The
only compliant shape is 42 separate container launches with 42 host redirections, i.e. Option A
taken to its limit: 42 receipts/argv/inspects, and the envelope's "one rootless container" line
would need re-reading by the qualifier rather than being preserved by construction. The integrity
story also fragments: 42 independent chains with no cross-chain link, so ordering and completeness
across children must be reconstructed from host receipts instead of from one chained hash — and the
chain was adopted specifically because partial evidence had to survive a kill.

## Option D — reduce the matrix itself

Shape: drop the `node` runtime rows. `probe.mjs:221` already labels them
"native-format component probe; **NOT** actual old-reader proof"; only the Bun rows carry the
old-reader role (`probe.mjs:220-221`, `README.md` Node-row labelling).

Pros: halves everything at no cost to the *actual* old-reader claim — 21 children, imports ≈95 MiB
weighted, comfortably under the 192 MiB filter threshold with ≈97 MiB left for store work, in a
single container, with one chain, one receipt, one argv, and no change to the envelope, the
deadlines, the capture architecture or the completeness model. Of the four, this is the only one
that buys headroom without weakening the integrity or capture story.

Evidentiary consequences: loses the Node native-format component control and the cross-runtime
corroboration — if a Bun row fails, there is no second runtime to distinguish an SDK-format problem
from a Bun-specific one. `expectedCases` drops 18 → 9, `expectedChildOperations` 42 → 21, so the
manifest changes and needs a fresh exact-manifest decision. It also narrows what the run can say
about native format independent of the reader runtime, which was part of why the Node rows were
added.

Nothing in this section is a viability finding. Every option trades evidence for headroom, and the
choice of which evidence to give up is the programme qualifier's, not the harness's.

---

# Addendum (requested by tarn2 / Soma, msg-c19050e9659945d68a4143eeff1c6cf8)

Still **INFORMATION ONLY**: nothing here is implemented, launched, or proposed for launch.
Three things this addendum explicitly does **not** propose, per the instruction: hiding import
effects (which rules out Option B1 above — see the note at the end of (a)), treating filtered
evidence as a pass (already structurally refused at `probe.mjs:128` →
`trace-filtered-uncertain-NOT-PASS`), and a general probe scheduler.

## (a) Minimal sequential split into groups, preserving full import+store trace and each group's own controls

**Group definition.** The minimal self-contained unit is **one runtime × route = 7 children**:
2 seeds + 2 verifies (old and new masters) + 3 clone attempts (`old-to-old`, `new-to-new`,
`old-reader-to-new-store`), i.e. `runtime.mjs:6-13`'s inner loop. This is minimal because the
`old-to-old` and `new-to-new` controls and the cross-version attempt must share the masters sealed
at `probe.mjs:193-194` and re-checked at `probe.mjs:217`; splitting finer than 7 separates an
attempt from its own control. Six groups cover the 42 children. Full
`%file,%desc,%memory,%process` tracing is retained for both the import and the store phase in every
group — nothing is dropped.

**Measured per-group import bytes** (from the r6 preflight per-child framed costs, weighted old×4 /
new×3; `budget-recomputed.json` + my independent recomputation):

| group (runtime/route) | children | measured import framed bytes | MiB | trace lines | headroom to 192 MiB |
|---|---|---|---|---|---|
| node/af | 7 | 101,259,877 | **96.569** | 131,992 | 95.43 MiB |
| bun/af | 7 | 54,829,225 | **52.289** | 76,549 | 139.71 MiB |
| bun/cm | 7 | 17,771,002 | **16.948** | 25,867 | 175.05 MiB |
| node/cm | 7 | 16,600,770 | **15.832** | 22,558 | 176.17 MiB |
| node/chronicle | 7 | 5,332,265 | **5.085** | 7,939 | 186.91 MiB |
| bun/chronicle | 7 | 2,492,680 | **2.377** | 3,460 | 189.62 MiB |
| **sum** | **42** | **198,285,819** | **189.100** | **268,365** | — |

The sum reproduces the single-stream figure exactly, which is the consistency check that the split
is a pure repartition of the same measurement.

**The distribution is the point, and it is very uneven.** `node/af` alone is 96.57 MiB — **51 % of
the entire 189.10 MiB** — because the AF route imports AF + Membrane + CM + Chronicle under Node's
ESM loader. A 6-way split therefore does *not* give six comfortable groups: it gives five groups
with 139–190 MiB of headroom and one group (`node/af`) with 95.43 MiB, which is the only number
that matters, since the store phase is unmeasured and must fit inside the *worst* group.
A coarser 2-way split (one group per runtime, 21 children) measures **node 117.486 MiB**
(74.51 MiB headroom) and **bun 71.614 MiB** (120.39 MiB headroom) — i.e. the 2-way split's worst
group has less headroom than the 6-way split's worst group, so going coarser buys nothing.

**Total elapsed.** The r6 preflight ran 12 import-only children in **6.94 s** wall
(`import-preflight-host-receipt.json`: `elapsedSeconds: 6.940948257`, `exitStatus: 0`), ≈0.58 s per
child for the import phase. Store-phase elapsed is **unmeasured**, so the only defensible bound is
the structural one: per group, 7 children × the 90 s child deadline (`runtime.mjs:11`) = 630 s
worst case, plus parent inventory/copy/seal work, inside one unchanged 1800 s whole-command
deadline (`runtime.mjs:14`). Across 6 groups the structural worst case is **6 × 1800 s = 10,800 s
(3 h)** of launch budget; the measured-import component of that is trivial (≈42 × 0.58 s ≈ 25 s)
and tells you nothing about the store phase.

**Total output cap.** Six launches each with its own 256 MiB stdout cap (`stream.mjs:4`) is
**6 × 256 MiB = 1,536 MiB** of aggregate captured evidence, against a 192 MiB filter threshold
applied six times independently.

**Honest statement of what the split actually changes.** Each individual launch stays inside the
unchanged per-launch limits — same container envelope, same 90 s/1800 s deadlines, same
192/256 MiB thresholds, same `--pull=never`/`network=none`/read-only/cap-drop posture. But
**N separate 1800 s / 256 MiB budgets are an AGGREGATE INCREASE, not "unchanged limits."** The
sequential split buys headroom by spending 6× the wall-clock budget and 6× the output allowance in
total. Describing it as "within the unchanged envelope" would be true only of each launch in
isolation and false of the experiment as a whole; it should be presented to the qualifier as a
request for 6 launches, not as a free re-shaping.

**Fresh-store isolation between groups.** Isolation is structural rather than something the harness
has to enforce: `/work` is a per-container tmpfs, so each group seeds its own masters at
`probe.mjs:187-194` under its own `/work/native/<runtime>/` and clones per scenario at
`probe.mjs:202-205`, with `errorOnExist: true`. A group cannot see or inherit another group's store
— every group starts from an empty writable tmpfs and ends by destroying it. The *cost* of that
same property is that the master sealed in group *i* cannot be the byte-identical master read in
group *j*: group `node/af`'s new-store master and group `bun/af`'s new-store master are separately
seeded, so a cross-runtime comparison of "the same new store" is no longer available by
construction. Within the 7-child group definition this does not bite, because every attempt shares
its group's own masters and `probe.mjs:217`'s seal assertion stays intra-group.

**What remains unknown if any group is INCOMPLETE.** Because each group carries its own old→old and
new→new controls, an INCOMPLETE group is contained: the surviving groups' rows stay qualified, and
`stream.mjs:77-83 checkCompleteness` still applies per group against that group's declared case set.
What is lost is specifically:
1. That group's 3 rows — including its `old-reader-to-new-store` row, which is the only row that
   carries the actual F-host.2b question for that runtime×route cell. There is no substitute for it
   from another group.
2. The ability to distinguish a **route-specific** from an **SDK-wide** result for that cell. If
   `node/af` is INCOMPLETE and `node/cm` refuses, you cannot say whether AF behaves the same way,
   because the AF driver (`0.30.0`, `probe.mjs:224` `afDriver: '0.30.0 NOT D'`) reaches the store
   through a different path.
3. If the INCOMPLETE group is a *Bun* group, the loss is more serious than the row count suggests:
   `probe.mjs:220-221` labels the Node rows "native-format component probe; **NOT** actual
   old-reader proof", so only the Bun groups carry the real old-reader role. An INCOMPLETE
   `bun/*` group removes old-reader evidence that no Node group replaces.
4. Nothing is learned about the *cause* unless the partial prefix is read: a killed group yields a
   verified prefix with no terminal record, which `stream.mjs:78` correctly reports as INCOMPLETE,
   and Soma's standing condition forbids synthesizing a closing record on the host. So "INCOMPLETE"
   stays INCOMPLETE; it must not be narrated as a refusal, an environment failure, or a pass.

Note on Option B1 above (dropping pre-`instrumentation-ready` lines from the native stream): the
instruction not to propose hiding import effects **rules B1 out**. I am recording it as excluded,
not carrying it forward. Option B2 (`%memory`) was already described as the more damaging variant
and is likewise not carried forward. The sequential split in (a) retains the full import+store
trace precisely so that no import effect is hidden.

## (b) Efficiency option: defer any further reference-only AF 0.30 / CM 0.24.0 experiment until INT-1

The new-store side of this matrix is pinned to **CM 0.24.0 / Chronicle 0.5.1, with AF 0.30.0 as a
driver only** — `worker.mjs:53` (`['0.30.0', '0.24.0', '0.5.1']`), `prepare.py:157`, and the
manifest's own `nativeFormat` string: `"(a) CM 0.24.0 / Chronicle 0.5.1; AF 0.30.0 is only a
driver, NOT D"`. Section (b) is already explicitly null: `applicationFloor =
"(b) pending D integration tarball"`, `dIntegrationPin = null`
(enforced at `check-prep.py:41-42`; `probe.mjs:150-152` refuses the D section with exit 78, and
`launch.sh:12` refuses any non-`native` section outside preflight).

The efficiency argument is therefore about **input identity, not about limits**: spending the
native budget now produces evidence about a configuration that is reference-only by its own
manifest. If the qualification candidate turns out to be **CM 0.24.1**, that is a *different input*
— a different `new-runtime` payload, a different per-file inventory, and therefore a different
`input-manifest.json` sha256 requiring a fresh exact-manifest decision. Concretely, everything
measured in this prep would have to be redone against it:

- The `new-runtime` input (7,149 files / 88,620,916 B, byte-identical from r5 to r6) is the thing
  that changes, so the pinned-leaf assertions at `worker.mjs:22-28` and the tree assertions at
  `verify-inputs.mjs:34-41` all re-derive.
- The 189.10 MiB import measurement re-derives: the import cost is dominated by the *new*-side
  module graph under the AF route, so a CM minor-version change moves exactly the number that the
  STOP conclusion turns on. The per-child figures above (`node/new/af` 14.979 MiB,
  `bun/new/af` 8.616 MiB) are properties of the 0.24.0 graph.
- The masters seeded by the new writer are produced by the candidate's own writer, so a 0.24.0
  master is not the store a 0.24.1 reader would be read against — the `old-reader-to-new-store` row
  would be answering the question about the wrong store.

So the sequencing that avoids paying twice is: **INT-1 first**, then qualify the exact candidate
dependency set (with CM 0.24.1 treated as a separate declared input, not a substitution inside the
existing manifest — `launch.sh:12`'s refusal of "AF 0.30 substitution" is the right instinct and
should extend to CM), then re-derive the manifest, the import measurement and the budget against
that set, and only then ask whether a native matrix is viable. Running the matrix now against
0.24.0 spends the whole budget on a row that is reference-only by construction, and the result
could not be carried over to the candidate.

This also interacts with (a) in the qualifier's favour: if the candidate set is settled first,
there is exactly one matrix to split and one aggregate budget to approve, rather than a 6-launch
aggregate spent on 0.24.0 followed by a second 6-launch aggregate on the candidate.
