# Changed

**TASK-4ZM52 — LOW fix round, compiler-core, LOCAL ONLY.** Task operator Tarn; qualifier Soma. The scope amendments below are engineering dispositions, **not owner decisions**. No activation or runtime-binding claim.

## Delta and commit

- Started clean at `e276024acf7c073ad21b42ec5132d96c332a2102`.
- Final local commit: **`aecd4a8cafe470ad2bbe47b5087c1ec454eef62f`**, directly on e276024a; final worktree clean.
- Reported delta: **`e276024a..aecd4a8c`**, five files, +63/-6.
- Author and committer: `Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>`; trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
- No push/PR/merge, main-checkout write, install, live operation, dependency/lockfile/generated-fixture change, new RefusalCode/error string/wire field/grant. `recipe.ts`, `index.ts`, `recipient-policy.ts` remain byte-identical. Production `recipient_policy_integration_pending` stays in force.

```
 docs/design/recipient-reply-compiler.md       | 18 ++++++++++++++
 src/recipient-admission.ts                    |  6 +++--
 test/recipient-reply.test.ts                  | 36 ++++++++++++++++++++++++---
 test/stubs/f-host-4/check-return-controls.mjs |  8 +++++-
 test/stubs/f-host-4/run-mutants.mjs           |  1 +
 5 files changed, 63 insertions(+), 6 deletions(-)
```

## Findings addressed

| Finding | Change and current evidence |
| --- | --- |
| LOW-1 runtime shapes | Exact `replyAuthority` keys asserted next to the full-install control: `['timeoutMs','resolve','authorize']`. Admission success results that previously had only member checks now have either exact `toEqual` verdicts or `['store','mayWake']` key assertions; existing exact verdicts retained. |
| LOW-1 fifth type control | A temporary **fresh typed local** binds a literal directly to `NonNullable<ReturnType<typeof compileRecipientAdmission>['replyAuthority']>` with excess `mappingId`; tsc rejects it with **TS2353, exit 1**. No Object.freeze, cast or suppression at that negative site. The four existing missing/wrong-field negatives still reject with TS2741/TS2322. Source restored after each. |
| LOW-2 detached lanes | Resolver now consults the **same** detached `lanes` set already used by admit, not live `policy.lanes`. The strict-boolean spelling avoids duplicating the frozen .3 mutant's `!lanes.has(...)` needle. No change to trusted lookup, readable/current ceiling, account tamper check or legitimate scope revalidation. |
| LOW-2 cell / mutant | New named cell **“mutating policy.lanes after compile does not widen the resolver”** checks push refusal before/after adding a lane, zero lookups for the forbidden push, unchanged admission refusal, and preservation of the original channel lane after clearing the source set. New **“live resolver lane read”** mutant is **KILLED by that exact named witness**. |
| LOW-3 deadline | Named I6 precondition documented with **host:98 and `test/stubs/f-host-4/v2/static-policy.ts:21,37–38`**. Per the explicit operator/qualifier amendment below, compiler-core now rejects enabled B deadlines outside the documented integer 1..60000 boundary through the **existing** timeout-validation/error path. No new error string or default. Controls reject 0, 60001, NaN and 1.5; accept 1 and 60000; false still installs no B. Separate A timeout remains 700 in the controls. |
| LOW-4 I6 behavior note | Docs explicitly call `unboundProse` a **tautological stub demonstration**, not derived B-consumer behavior. I6 must drive the case through the **real B consumer**; that behavior remains honestly unqualified. The existing activation blocker and mode/effect distinctions are retained. |

### Exactness limitation (not a universal TypeScript claim)

The intended return annotation is unchanged. **Field types/required members are checked**, and a fresh directly contextually typed literal rejects an excess member. **Universal excess-property rejection is NOT claimed**: structural typing can still accept extra members through the actual non-fresh `Object.freeze` construction / `as const` verdict expressions, as demonstrated by the review at e276024a. Those construction/annotation patterns are unchanged. I did not add an “exact TS objects” subsystem or undertake the optional annotation tightening. Actual produced callback/verdict shapes are guarded by runtime exact-key / exact-verdict assertions instead. The fifth negative is reported only for its discriminating direct-typed-local site, never relabelled as proof of universally exact objects.

## Review input and narrow §5 disposition

Read `/srv/review-drop/TASK-4ZM52-REVIEW1.md` **in full**; verified SHA256 **`8f7c7341ff4d5bdac5cb89e75790f4fd4950688c139d24424da9835878c6b1e0`** before use and again in final bindings. Confirmed findings against current source. The unchanged immutable v4.3 brief hashes to `2fafa3d4a2e16d969596086cca5d8b00b0a5c032edd38e0736e91430757c2e84`; engineering STOP disposition / named activation blocker remains in force.

`ask-2145f918a418474f8ce5a74fdf9eb946` returned stdout from task operator Tarn, with binding Soma refinements. In particular, this is the explicit **narrow §5 disposition** permitting the LOW-3 runtime validation beyond the initial doc-only direction:

> #3 — CHANGE vs the original brief: a range check of automaticReplies.timeoutMs IS allowed as covered compiler validation, through the EXISTING validation/error path (the same mechanism as the :152 check of the admission timeout), with the exact documented/B boundary semantics (1..60000 as in host:98 / v2/static-policy.ts:21,37-38) and boundary controls (0, 1, 60000, 60001, NaN, non-integer — with exactly the semantics already documented, no stricter integer rule). No new wire refusal code, no invented default. If that needs a new external contract (e.g. a new error string visible outside) — STOP that part, leave the I6 precondition UNRESOLVED and don't accept an invalid compiled authority as complete. Record the narrow §5 disposition explicitly in the report.

The implementation extends only the existing guard and still throws the existing `recipient_admission_timeout_invalid`; `Number.isInteger` and 1..60000 match the documented boundary, with no stricter integer rule. Invalid compiled B authority is **not returned**. Final bindings prove the runtime delta is exactly the detached-lane read plus this guard extension; the error-string list and return annotation are unchanged. No new external contract was required. I6 integration and its front-end precondition remain pending, not qualified by these compiler controls.

The same answer forbids universal-exactness claims, requires the named live-read kill, and requires targeted controls before one final-head gate set. Followed in that order.

# Verification Gates

## Fresh execution and closure

**Every assigned final-head check was run fresh in this round**; no earlier pass is implied by resuming. Targeted controls first: four new/strengthened LOW cells passed (36 assertions) against worktree source via the declared Bun; all five type negatives passed against the unchanged e276024a annotated type in the existing isolated root, with temporary source restored. This was a dirty/precommit probe, not final qualification. After the local commit and controlled restage, the complete final-head set ran once. No retries and **one full suite at final head**.

Gate form: `orgasmic dispatch gate NAME -- bash "$E/bounded.sh" ...`, where `$E` is this round's evidence directory. Every gate used **16G**, swap **0**, `timeout --signal=TERM --kill-after=60s 3000s`, **64 MiB cap**, retained **PIPESTATUS**. No timeout, 137, 141, cap or INCONCLUSIVE result. Intentional inner tsc negatives exit 1 and are retained as expected failures, not hidden. `gate-index.json` indexes all receipts and byte counts.

The only execution/dependency root remains:
`/home/orgasmic/.orgasmic/worktrees/theaspirational.connectome-host/task-6jaeq/tmp-recipient/build/head`.
No new build root or install. `cell.sh` uses the existing runtime binaries with a scrubbed environment/HOME/config/cache and current-round test state under the existing state area. No run token is printed or forwarded into the scrubbed cell.

Before stage: all **567 tracked inputs**, including a **closed 494-file src/test/docs/scripts inventory**, matched e276024a. Stage copied **only five changed tracked paths**; before/after hashes are in `staged-inputs.json`. After stage and after all gates: same closed inventory matches aecd4a8c, no extra or missing input/control files. `inputs-before-stage.json`, `inputs-after-stage.json`, `inputs-after-gates.json`.

Installed root+web closure before stage / after stage / after gates: **16,820 entries**, exact SHA256 **`6b42678559f175f926db4a39c1527f2415c37bab1392be6a74ae95446233ed64`**. Symlinks remain within their corresponding installed root. Host lock `7f0e4849a8238b90e9d782ffef558a3e5b80fe6252154d2372f42f9a5c09e020`; web lock `0617247d4e5a2d86a86886ec98e3dd3464a9250ce8b8b14dd8d7f9fb856fb0c3`. No host dist/web-dist/out/build/target exists or is consumed. Source execution and noEmit only; dependency compiled payloads are covered by the unchanged manifest.

Fresh toolchain hashes/versions in `low-bindings`: Node **22.23.3**, SHA256 `fde6a4bf8d0562f7751d1a2d6cb9b417c4cfe107bbcb0aa3e9a24e125e348f48`; Bun **1.4.3**, `7ce7d6b654eddeec20afddf2396317fba6dc19ab52788a27e894e1806bf8f24b`; selected native TS **7.0.2**, `4f2de678286401759b3fb4475bafe35b8f32b4b3a07d92642bbf37eadc9b34a4`.

### Fresh quiescence witness, not the old 05:25 witness

`low-prestage-visibility` **FAILED, exit 1**, before edits or staging: zero visible head references, but three inaccessible same-user services plus 250 unreadable root pids. Its failure/log/JSON are preserved. I stopped and asked; **the old 05:25Z witness was not reused**.

Tarn supplied `/srv/review-drop/TASK-4ZM52-ROOT-WITNESS-2.json`, SHA256 **`3b3a8d3d89cd8b3d046aad24cbaac8327fc912956c414669f13d75e6f2207009`**, verified before use. This is a separately attributed operator **root** observation at **2026-10-31T05:54:29Z**, all 378 /proc pids, cwd/root/exe/every fd/maps against the exact prefix, **zero hits/read errors**. Named uid-1002 services are pinned by pid/start/cgroup: 1830/39736, 1907/39737, 3182/40128. Operator explicitly permitted the same-root restage from e276024a to the LOW final commit, no killing/access changes/install.

Worker visible scans and path-limited lsof immediately before/after targeted controls, immediately before/after staging, and after final gates found **zero references/holders** and no unresolved new same-user identities. The only six lsof exclusions are the previously named unrelated Docker mounts; no head filesystem exclusion or warning suppression. This root witness is point-in-time, not a lock or my own root visibility. Later inaccessible root pids remain a visibility limit, separately recorded rather than attributed to the earlier measurement.

Disk before the full suite: **25,470,038,016 bytes**, greater than 20 GiB. Final observation: 25,455,058,944 bytes. Expected evidence <64 MiB, observed ~13 MiB; no build caches in evidence.

`process-reconciliation.json/.txt`: **44 known owner pids** checked by pid/start/cgroup, **0 matching survivors**, **22 gate scopes**, **0 nonempty**, **0 build-cwd processes**. No unrelated process killed; blocking ask returned its stdout answer and ended.

## Gate table

For cell commands below, prefix `bash "$E/cell.sh" head` inside the bounded wrapper. Direct evidence/git commands are marked separately. Each log is under **this round's** evidence directory, not the previous run's.

| Gate | Command after cell prefix, or direct | Result | Log |
| --- | --- | --- | --- |
| `low-prestage-visibility` | `direct: python3 -B $E/quiescence.py` | FAILED — incomplete visibility; STOP/ask, no stage; exit 1 | `gate-low-prestage-visibility-01M688B49GF9FW8PYSZDXRW3ZW.log` |
| `low-targeted-controls` | `direct: bash $E/targeted.sh` | PASS — precommit 4 LOW cells + 5 negatives; not final qualification; exit 0 | `gate-low-targeted-controls-01M688GS8CHKQGJX400M5NBDKF.log` |
| `low-stage` | `direct: python3 -B $E/stage.py` | PASS; exit 0 | `gate-low-stage-01M688NJX3DETR48S267AN3BE8.log` |
| `low-types-root` | `npx --no-install tsc --noEmit` | PASS; exit 0 | `gate-low-types-root-01M688PK6N3MM08RE0PPCAE665.log` |
| `low-types-v0` | `npx --no-install tsc --noEmit -p test/stubs/f-host-2a/v0/tsconfig.json` | PASS; exit 0 | `gate-low-types-v0-01M688PKND9R6E8RFHTS5CE1M1.log` |
| `low-types-v1` | `npx --no-install tsc --noEmit -p test/stubs/f-host-3/v1/tsconfig.json` | PASS; exit 0 | `gate-low-types-v1-01M688PM47DGAEB6WJ3EXVAKXZ.log` |
| `low-types-v2` | `npx --no-install tsc --noEmit -p test/stubs/f-host-4/v2/tsconfig.json` | PASS; exit 0 | `gate-low-types-v2-01M688PMJY6N7H6TM3N4E7DFV7.log` |
| `low-controls-v0` | `node test/stubs/f-host-3/check-compiler-controls.mjs` | PASS; exit 0 | `gate-low-controls-v0-01M688PN1NV4DYBF4Z0WT9Y13Z.log` |
| `low-controls-v1` | `node test/stubs/f-host-3/check-v1-compiler-controls.mjs` | PASS; exit 0 | `gate-low-controls-v1-01M688PP98530SPCNAEJ8NYJ4T.log` |
| `low-controls-v2` | `node test/stubs/f-host-4/check-compiler-controls.mjs` | PASS; exit 0 | `gate-low-controls-v2-01M688PQ4CDBAC3HT2NPF5CD7D.log` |
| `low-controls-return` | `node test/stubs/f-host-4/check-return-controls.mjs` | PASS — all five expected inner failures, incl. direct excess TS2353; exit 0 | `gate-low-controls-return-01M688PQS4VZ92D713P9A3VD29.log` |
| `low-b-contract` | `node test/stubs/f-host-4/check-b-contract.mjs /srv/review-drop/af-b29edcf8-contract-v2` | PASS; exit 0 | `gate-low-b-contract-01M688PS3V1CPHPD8AWYZK9ZPG.log` |
| `low-focused` | `bun test test/recipient-policy.test.ts test/recipient-reply.test.ts test/recipient-reply-loading.test.ts test/recipe-unknown-keys.test.ts test/mcpl-agent-overlay.test.ts` | PASS — 115 tests / 747 assertions; exit 0 | `gate-low-focused-01M688PS92MWWQHYS09N4V3MWW.log` |
| `low-mutants-old` | `BUN_BIN=<declared-bun> node test/stubs/f-host-3/run-mutants.mjs` | PASS — all 23 retained mutants killed; exit 0 | `gate-low-mutants-old-01M688PSMDJPT1WWB69J4NTS2Y.log` |
| `low-mutants-new` | `BUN_BIN=<declared-bun> node test/stubs/f-host-4/run-mutants.mjs` | PASS — 17 named kills, including live lane read; exit 0 | `gate-low-mutants-new-01M688PXD3MMEEZWKDFHTVJ0PR.log` |
| `low-feature-off` | `bun test/recipient-feature-off.probe.ts <existing-root> (tee $E/feature-off.json)` | PASS; exit 0 | `gate-low-feature-off-01M688PXN74HQEQAKR028NFQHS.log` |
| `low-feature-compare` | `direct: python3 -B $E/compare-feature.py` | PASS — exact bytes match supplied baseline pin; exit 0 | `gate-low-feature-compare-01M688PXX831VREKD7FBM23RA9.log` |
| `low-self-test` | `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS; exit 0 | `gate-low-self-test-01M688PY28G1CZV5FCQX4XXBA3.log` |
| `low-docs-contract` | `node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | FAILED — declared filename false positive; exit 1 | `gate-low-docs-contract-01M688PY78MHDBSDSSCBMW5750.log` |
| `low-suite` | `bash scripts/test-on-tmpfs.sh` | PASS — 4622 tests / 0 fail / 31857 assertions; exit 0 | `gate-low-suite-01M688PYCANNKTVMNP4EEE4FHB.log` |
| `low-diff-check` | `direct: git diff --check e276024a HEAD` | PASS; exit 0 | `gate-low-diff-check-01M688T1B0K1CSV35BM966Y1VH.log` |
| `low-bindings` | `direct: python3 -B $E/final-bindings.py` | PASS; exit 0 | `gate-low-bindings-01M688V3R3WQDYA6DVCTEN4JDJ.log` |

Full suite was run **once** at aecd4a8c: `scripts/test-on-tmpfs.sh` → `npm test -- --parallel=4`, 4622 tests across 269 files, 101.14s. Self-test retained 57 fixture cases, 45 rejection controls, four oracle mutants and singleton/naming-parser controls; not real B execution.

**Feature-off byte check:** 3,974 bytes, SHA256 **`5af23aaa62c8996f61dc32caf50443b233f92b6e0675a68f98cbb88d97936e82`**. The inherited baseline transcript was first checked against the supplied pin, then compared **byte-for-byte** with the fresh final-head output (`feature-off.json`). This is the existing loader/overlay/config production-function probe, not real AF transport/no-await qualification.

**Docs-contract remains FAILED**, expected: checker:233/361 reports “internal identifier”; the unchanged enumeration includes `../../stubs/f-host-3/run-mutants.mjs` at checker:343 and the declared filename regex matches `run-mutants`. No checker, enumeration, fixture or oracle edit. TASK-EC6WW / naming-partial disposition remains unchanged.

### New cell and named mutant

```
KILLED live resolver lane read: mutating policy.lanes after compile does not widen the resolver
```

This comes from the actual mutant run, not an inferred kill. Runner still requires a nonzero child plus a named `(fail)` witness; zero selected tests cannot count as a kill. All 23 old and 17 new source mutants restored their original bytes, reverified by the closed inventory after gates.

### Valid inherited evidence and unchanged scope

| Inherited input/context | What remains valid; what is NOT implied |
| --- | --- |
| v4.3 brief + engineering STOP disposition | Constraints/activation blocker still apply. The narrow deadline-validation amendment is recorded above. No activation approval or owner decision. |
| Review APPROVE at e276024a | Read/verified context and concrete LOW findings, not qualification of aecd4a8c. Every assigned final gate here is fresh. |
| Existing declared closure and binaries | Pins inherited as required inputs; **freshly rehashed** before/after stage and after gates. No old receipt is substituted for current types/tests/suite. |
| Feature-off baseline transcript | Unchanged 3974-byte transcript with the supplied SHA256; used only as the expected byte-comparison input. No old gate pass is implied. |
| Frozen v0/v1 stubs, .3 test/restated cells and old runner | **Eight v0/v1 files** freshly verified byte-identical base↔head in `frozen-stubs-sha256.json`; .3 tests/runner/probe also unchanged and run fresh. No additional .3 re-baselining this round. |
| A and B v2 immutable drops | `low-bindings` freshly checks both manifest digests and `sha256sum -c` (A 5/5 OK, B v2 10/10 OK); B byte-contract gate is fresh. No new AF signature or runtime import. |
| Prior failed selectors / prior docs-contract receipts | Kept unchanged in `/home/orgasmic/.orgasmic/runs/run-01M686AMRJEZ0YCY8ZTSHYA17Q/evidence`; no edits, retry or pass relabelling. The review's separate zero-selection probes are context; this round's 40 named kills are fresh. Current docs-contract failure is also fresh. |

A SHA256SUMS digest: `1a8406fa18e4f2b5c777ae773c68772eacea50a5a6c2f0882bd0e1fa6ffec2b2`; B v2 digest: `d9c5164818dd166ae62418fc3ce6060345ad2ec212e3e9190e6f49d5c9a796fe`. All original runtime-binding limitations remain outside the meaning of these integrity checks.

# Unmet Criteria / What was not done

- No requested LOW fix remains unimplemented within the amended compiler-core scope. LOW-3 precondition is documented and compiler boundaries are checked using the allowed existing path; **real I6 integration remains pending**.
- No universal TypeScript exact-object enforcement or optional return-annotation tightening. The known structural-typing excess-member limitation remains honestly recorded; runtime key sets and a directly typed fifth negative are the chosen allowed controls.
- **Docs-contract is FAILED** on the declared filename false positive. Not fixed or rerun. Full private naming qualification remains partial.
- The earlier supplemental zero-selector helper remains FAILED in its original evidence. It was not changed, rerun or renamed as a pass.
- No real B-consumer unbound-prose test: only an I6 requirement note was added. `unboundProse` remains tautological; no new unbound mutant or fake runtime proof.
- No production activation, real AF binding, transport/reconnect/delivery/notice, descriptor/grant/incarnation qualification, real scope constructors/current generation or dynamic selector work. **I6/I7/.2a remain PENDING.** All B behavior remains stub-of-B.
- No change to the named unbound timer/home/latest-inbound activation blocker or non-exhaustive inventory. Owner activation decisions (items 6/7) still needed; no affected-agent claim.
- No T1/T2 oracle reconciliation (`mappingId` absent from ReplyDestination, oracle lacks inference lane), schema/wire/grant/refusal work, bug-report behavior, recipe/index/policy loader change, install, publication or live operation.

# Residual Risk

- Local runtime key checks pin the exercised shapes, not universal structural exactness; the direct-typed excess negative must not be read as covering every non-fresh construction.
- Shared detached lanes prevent operator-object mutation widening, while current trusted scope lookup remains live. Real verifier/projector, AF descriptor/grant/incarnation and approved-input lifecycle coupling remain unqualified.
- The B deadline guard is compiler-core validation behind the unchanged production refusal. I6 must retain the named front-end precondition, separate deadlines and real AF qualification; these tests do not establish release readiness.
- Unbound opt-in behavior still needs the real B consumer at I6. The concrete activation blocker is not cleared by this doc-only requirement or by the stub demonstration.
- Worker process visibility remains incomplete for root/services; the fresh operator witness and worker scans are point-in-time, not a lock or expanded access. No holder was killed or access changed.
- LOCAL ONLY / private ancestry hold remains; no full-range public privacy approval, push, PR, merge or activation authority is inferred.

# Required v4.3 brief quotations (unchanged immutable text)

The narrowed LOW assignment and explicit engineering dispositions above apply without rewriting the immutable brief.

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

