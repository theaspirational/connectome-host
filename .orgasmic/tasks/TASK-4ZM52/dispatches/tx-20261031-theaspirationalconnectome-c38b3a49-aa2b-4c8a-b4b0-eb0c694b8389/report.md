# TASK-4ZM52 — F-host.4 compiler-core review (LOCAL ONLY)

Range reviewed: `c353e1c8d19a11697b5515bf3b03110de5dd85de..e276024acf7c073ad21b42ec5132d96c332a2102`
(2 commits: `536b2e23` = retained failed first head; `e276024a` = type-only return annotation).

Inputs verified by hash before use:
- Brief `/srv/review-drop/tarn-rtaln-20261030/FHOST4-BRIEF-v4.3.md` = `2fafa3d4a2e16d969596086cca5d8b00b0a5c032edd38e0736e91430757c2e84` ✓ (matches the dispatch prefix `2fafa3d4…2e84`)
- Implementer report `/srv/review-drop/TASK-4ZM52-FHOST4.md` = `a6bad5688ab0786f520a01fb98ae15ac92808f088c53c6513d22d4c5ff114f7f` ✓ (`a6bad568…4f7f`)

Task operator Tarn, qualifier Soma; the quoted ENGINEERING STOP DISPOSITION (ask-428cedb0)
is recorded under that name in the implementer report and in
`docs/design/recipient-reply-compiler.md:30-34`. Nothing here is an owner decision.

All eight brief checks were performed **from source**, not from the receipts. The existing
build root `…/task-6jaeq/tmp-recipient/build/head` was confirmed staged at `e276024a`
(494 tracked `src/ test/ docs/ scripts/` files compared: 0 differ, 0 missing; staged
`src/recipient-admission.ts` = `033b78f7…` = `HEAD`, **not** the first head `75f638db…`),
so reruns were executed there under my own bounded/scrubbed cell, with no installs.

## Verdict

**APPROVE — HIGH confidence** for acceptance (a) *host core, compiler-core, LOCAL ONLY*.
Acceptance (b) *runtime binding* is correctly left PENDING and is not claimed.

Every brief check passed on independent re-derivation. No HIGH or MEDIUM finding. Four LOW
findings are blind spots and hardening gaps, none of them a defect in current behaviour and
none an unmet acceptance criterion. Note that **not all gates are green**: `docs-contract`
remains FAILED (declared filename false positive, TASK-EC6WW) and the supplemental
zero-selector helper is a recorded FAILED limitation; the implementer states both plainly
and makes no blanket-green claim (`docs/design/recipient-reply-compiler.md:70-71`).

## Findings

### LOW — `src/recipient-admission.ts:130-151` (test, correctness): the return annotation does not reject *excess* nested members
The annotation is a genuine compiler-checked contract for *missing* and *wrong-type* members,
but it cannot see invented extra members inside `Object.freeze({…})` (`:130`) or inside an
`as const` result (`:210`, `:214`), because TypeScript's excess-property check only fires on a
fresh literal directly assigned to a contextual type. I proved this myself in the staged root:

| my mutation | tsc verdict |
| --- | --- |
| drop `authorize` from `replyAuthority` (`:141`) | **CAUGHT** TS2322 |
| `admit`'s `mayWake` → `String(mayWake)` (`:214`) | **CAUGHT** TS2719 |
| `resolve` returns `{channelId: channelId.length}` (`:138`) | **CAUGHT** TS2322 |
| add `publishGrant: 'invented'` to the **top-level** return (`:161`) | **CAUGHT** TS2353 |
| add `mappingId: 'invented'` inside `replyAuthority` (`:131`) | **NOT CAUGHT**, exit 0 |
| add `serverId: 'invented'` inside `admit`'s success result (`:210`) | **NOT CAUGHT**, exit 0 |

Failure scenario: a later edit adds an unintended member — exactly the kind of invented
wire/authority field `test/stubs/f-host-4/v2/typecheck.ts:11-12` exists to forbid — to
`replyAuthority` or to `admit`'s verdict, and nothing fails. For `admit` the runtime
`toEqual` cells (`test/recipient-policy.test.ts:213,222,233,281`) would still catch it, but
`replyAuthority`'s key set is asserted **nowhere**: the only assertions are `toBeDefined()`
(`test/recipient-policy.test.ts:208`, `test/recipient-reply.test.ts:48`) and `timeoutMs`
(`:50`). The b-contract assignability check cannot help either —
`v2/typecheck.ts:8` assigns a non-fresh value, so excess properties are unchecked there too.

This does **not** violate the operator's conditions: ask-319d789f (3) and Soma's refinement
required missing-member and wrong-field-type discrimination, and `check-return-controls.mjs`
delivers exactly those four. Brief §3 cell 9's excess control also exists, as a negative on
the pinned *stub type* (`v2/typecheck.ts:12`, TS2353). The gap is the host return shape.

Fix direction: add `expect(Object.keys(full.replyAuthority!)).toEqual(['timeoutMs','resolve','authorize'])`
beside `test/recipient-reply.test.ts:48-50`, and/or a fifth `check-return-controls.mjs` case
asserting an excess nested member is rejected after binding the literal to the annotated type
directly (e.g. via a typed local) rather than through `Object.freeze`.

### LOW — `src/recipient-admission.ts:117` vs `:156,169` (correctness): the resolver reads `policy.lanes` live while `admit` reads a detached snapshot
`admit` uses the defensive copy `const lanes = new Set(policy.lanes)` (`:156`, consumed at
`:169`) — the frozen .3 idiom of detaching the compiled ceiling from the operator's object.
The new `replyChannel` instead reads `policy.lanes.has(input.lane)` **live** (`:117`). The
other policy members the resolver needs *are* captured (`reply` at `:105`,
`automaticReplies` at `:106`), and the one other live read, `policy.accountId` at `:122`, is
deliberate — it is paired with the captured `replyAccount` as an explicit tamper check.
`policy.lanes` has no such pairing, so this reads as an oversight rather than a check.

Failure scenario: a holder of the policy object mutates `policy.lanes` after `compileRecipientAdmission`
returns (the field is `readonly` to TS only, not frozen by this compiler). The resolver's lane
ceiling then widens while admission's stays pinned, so the two disagree about which lanes are
admissible. Blast radius is bounded: the hard lane gate at `:116` still restricts resolution
to `channels/incoming`/`push/event`, resolution still requires a closed `replyIntent` plus
matching channel evidence, `trusted.lookup`, and a literal-`true` `reply`; and production is
fail-closed behind `recipient_policy_integration_pending`. So no reachable escalation today.

Fix direction: one line — have `replyChannel` consult the same detached set. `lanes` is
declared at `:156` but `replyChannel` is only ever *called* after `compileRecipientAdmission`
returns, so hoisting the `new Set(policy.lanes)` above `:113` (or simply referencing `lanes`)
is sufficient; add a mutate-`policy.lanes`-after-compile cell to pin it.

### LOW — `src/recipient-admission.ts:131` (design/robustness): compiler-core forwards `automaticReplies.timeoutMs` without the range check it applies to its own deadline
`compileRecipientAdmission` defends itself against a bad admission deadline — `:152-154`
throws `recipient_admission_timeout_invalid` unless `policy.timeoutMs` is an integer in
1..60000 — but installs `replyAuthority.timeoutMs` straight from `automaticReplies.timeoutMs`
(`:131`) with no equivalent validation. Today the only front-end that reaches it does validate
(`test/stubs/f-host-4/v2/static-policy.ts:21,37-38`, `deadline()` = integer 1..60000), which
is precisely where brief §2.2 places the check, so this is consistent with the brief as
written and the `merged A B timeout` mutant does pin the two deadlines as distinct.

Failure scenario: the qualified I6 integration supplies a policy from a different front-end
with `automaticReplies: {timeoutMs: 0}` (or `NaN`, or 10^9); B receives a nonsensical routing
deadline from a compiler that rejects the same value for lane A. Unreachable while production
stays refused, hence LOW.

Fix direction: reuse the existing guard at `:152` for `automaticReplies.timeoutMs` when
`automaticReplies !== false`, throwing a sibling `recipient_reply_timeout_invalid`; flag for
the I6 reviewer if adding a new error string is out of .4 scope (brief §5 bars new RefusalCodes).

### LOW — `test/stubs/f-host-4/b-model.ts:70` (test): the activation-blocker demonstration is tautological and has no mutant
`unboundProse(optedIn)` is `optedIn ? {private:true, notice:privateReplyNotice} : {private:false}`,
and `test/recipient-reply.test.ts:245-246` asserts exactly that, so the cell restates B's
documented opt-in rule rather than deriving it. No mutant in `test/stubs/f-host-4/run-mutants.mjs`
targets `unboundProse`, so it contributes no discriminating power.

This is honestly labelled, which is why it is LOW and not a correctness finding:
`docs/design/recipient-reply-compiler.md:51-55` states the cell "demonstrates only B's opt-in
rule … neither asserts the mode produces prose nor qualifies real effects". The cell does
retain real teeth from its citation assertions (`test/recipient-reply.test.ts:243-244` would
fail if the doc dropped a `file:line` citation or the "not an exhaustive" caveat), and the
notice literal is byte-pinned (`test/stubs/f-host-4/check-b-contract.mjs:18-19`). Brief §3
cell 10 asks only for an inventory **control**, which that citation check satisfies.

Fix direction: none required for .4. At I6, replace the tautology with a cell driven through
the real B consumer so the opt-in refusal is derived rather than asserted.

## Verification Gates

Reran in the staged `e276024a` root, each bounded (`timeout --signal=TERM --kill-after=60s 1200s`,
PIPESTATUS retained, 32 MiB cap), no retries, no installs, via `orgasmic dispatch gate`:

| Gate | Command | Outcome | Log |
| --- | --- | --- | --- |
| `rev-focused` | `bun test test/recipient-policy.test.ts test/recipient-reply.test.ts test/recipient-reply-loading.test.ts` | **PASS** — 41 pass / 0 fail / 329 expect() | `gate-rev-focused-01M687RMJX0DY9BZSHN8ACWGN2.log` |
| `rev-v1-v2-types` | `tsc --noEmit` for v1, v2 and the root project | **PASS** — v1=0, v2=0, root=0 | `gate-rev-v1-v2-types-01M687RV3SBM60VVS6XKYWSPMM.log` |
| `rev-return-controls` | `node test/stubs/f-host-4/check-return-controls.mjs` | **PASS** — all four negatives failed as expected with real TS2741/TS2322 | `gate-rev-return-controls-01M687RYNX40GMNRPZ9ARN7P61.log` |
| `rev-own-mutations` | my own 3 deeper annotation mutations | **3/3 CAUGHT** (4th case aborted on my own needle typo, pre-write; source intact) | `gate-rev-own-mutations-01M687SHK50JZPE1S78N2EAZQ5.log` |
| `rev-own-mutations-2` | my own 3 excess-member mutations | 1 caught, **2 NOT caught** → LOW finding #1 | `gate-rev-own-mutations-2-01M687T92J5BS84938QEPWY6ZB.log` |
| `rev-mutants-new` | `run-mutants.mjs` (5 of 16: install-whenever, never-install, configured-server-id, truthy-ceiling, absent-reply-defaults-allow) | **PASS** — 5 named kills | `gate-rev-mutants-new-01M687W54C5B3T7SG0N6FEES5Y.log` |
| `rev-mutants-old-retargeted` | `test/stubs/f-host-3/run-mutants.mjs` (no selector support → all ran) | **PASS** — 23 named kills, incl. the retargeted witness | `gate-rev-mutants-old-retargeted-01M687W9Z0WGWW00W420M54KP6.log` |
| `rev-feature-off` | `bun test/recipient-feature-off.probe.ts <root>` | **PASS** — 3,974 bytes, `5af23aaa62c8996f61dc32caf50443b233f92b6e0675a68f98cbb88d97936e82` | `gate-rev-feature-off-01M687WSDH7DB7SBVA23Z71YNM.log` |
| `rev-zero-selection-a` | 0-match `bun test --test-name-pattern`; f-host-4 empty mutant selection | **PASS** — Bun 1.4.3 exits **1**, `matched 0 tests`; empty selection rejected at `:34` | `gate-rev-zero-selection-a-01M6885B05C9QGZZT3T91E06Q5.log` |
| `rev-zero-selection-b/c` | both runners with a witness matching 0 tests | **PASS** — both exit 1 at the named-`(fail)` assert (`f-host-4:49`, `f-host-3:48`) | `gate-rev-zero-selection-c-01M6886DN65H2EY3FXFCWHXQPQ.log` |

Reused receipts (not rerun): `f4-final-suite` (exit 0, 4620 tests) and the remaining 19
`f4-final-*` gates listed in the dispatch. A full suite is not named for this review.

### Zero-selection guard — separate narrow control (manager addition, msg-2188df08)

Requested because `f4-final-selectors` FAILED on a Bun output-format assumption and did not
record the child exit code, so it is not proof. **Result: the zero-selection guarantee is
PROVEN**, independently of that helper. Logs `gate-rev-zero-selection-a-01M6885B05C9QGZZT3T91E06Q5.log`,
`-b-01M6885XXY6PD3PPBFDSDZZ2YG.log`, `-c-01M6886DN65H2EY3FXFCWHXQPQ.log`. Full suite not rerun.

**(a) Bun's own behaviour on a 0-match selector.** Same binary, `bun --version` = `1.4.3`:

```
$ bun test --test-name-pattern "^NO_SUCH_WITNESS$" test/recipient-reply.test.ts
error: regex "^NO_SUCH_WITNESS$" matched 0 tests. Searched 1 file (skipping 19 tests) [4.00ms]
CHILD_EXIT=1
```

So Bun **does** exit non-zero (1) on a zero-match selector, and emits no `(fail)` line. This
confirms the helper's premise was wrong in its text assertion (`/0 pass/`) but also shows why
exit code alone is **not** a sufficient kill signal: `assert.notEqual(r.status, 0)` would pass
spuriously on a zero-test run. The guard therefore has to be the named-witness check.

**(b) Both runners reject an empty selection and a missing named `(fail)` witness.**

Read at file:line:
- `test/stubs/f-host-4/run-mutants.mjs:33` builds `selected` from `process.argv[2]`;
  `:34` is `assert(selected.length > 0, 'selector matched zero mutants')`.
- `:48` `assert.notEqual(r.status, 0, SURVIVED)` — necessary but, per (a), not sufficient.
- `:49` `assert(output.split('\n').some(line => line.includes('(fail)') && line.includes(witness)), 'no named failing test (zero tests is FAIL)')` — the real guard.
- `test/stubs/f-host-3/run-mutants.mjs` has **no selector support at all** (`process.argv` is
  never read; the only `selected` match in the file is a mutant *name*, `:36`), so an empty
  mutant selection is structurally impossible there and all 23 always run. Its guard is the
  identical named-witness assert at `:48`.

Empirically, with the declared Bun 1.4.3:

| probe | child exit | assertion that fired |
| --- | --- | --- |
| `run-mutants.mjs NO_SUCH_MUTANT_XYZ` (f-host-4, empty selection) | **1** | `selector matched zero mutants` @ `run-mutants.mjs:34` |
| f-host-4 runner, real mutation applied but witness replaced by a 0-match string | **1** | `install whenever policy exists: no named failing test (zero tests is FAIL)` @ `:49`, with Bun's `matched 0 tests` in the captured output |
| f-host-3 runner, the retargeted witness replaced by a 0-match string | **1** | `feature-off installs callback: missing discriminating assertion` @ `:48`, same `matched 0 tests` output |

The second and third rows are the decisive ones: the mutation really was applied and Bun really
did exit 1, yet the run still failed — at the witness assert, not the exit-code assert. So a
selector that matched 0 tests cannot be laundered into a kill by either runner, and the 23+16
named kills are therefore valid selector controls for the retargeted titles. The bogus-witness
copies were made in `$ORGASMIC_EVIDENCE_DIR` (`bogus-witness-h4.mjs`, `bogus-witness-h3.mjs`),
never in any tracked tree; the runners' `finally` blocks restored the staged source, reverified
byte-identical to `HEAD` afterwards.

Kept separate from successful gates, as instructed: `docs-contract` stays **FAILED** (declared
filename false positive) and `f4-final-selectors` stays **FAILED** (a recorded limitation, not
renamed as a pass). No "all gates pass" claim is made anywhere in this review.

### Brief check-list results, each re-derived from source

**1. §2.5 is a closed list — CONFIRMED.** `git diff --name-only c353e1c8 HEAD -- src/` returns
exactly `src/recipient-admission.ts`. Blob SHAs are byte-identical base↔head for
`src/recipient-policy.ts` (`9b7f4ffc…`), `src/recipe.ts` (`7a3938b3…`) and `src/index.ts`
(`a5c37dbf…`). The production barrier is a real negative, not a stub: `src/recipient-policy.ts:6,10-14`
is untouched and `test/recipient-reply-loading.test.ts:16-24` drives
`recipient_policy_integration_pending` through **six real loader entry points** —
`validateRecipe`, `loadRecipeDetailed` (file), `loadRecipeDetailed` (localhost HTTP),
`loadSavedRecipe`, `loadMcplServers`, `readAgentOverlayFile`. Total delta is 15 files, +751/-8,
all other paths `test/`, `docs/` or `test/stubs/`.

**2. Install condition — CONFIRMED.** No policy → `return {}` (`:104`). `automaticReplies === false`
→ `replyAuthority = undefined` (`:130`) and the conditional spread omits the key entirely
(`:161`), so it is an absent member, not an `undefined` value — asserted as
`not.toHaveProperty('replyAuthority')` (`test/recipient-reply.test.ts:46`) and
`Object.keys(...) === ['inboundAdmission']` (`test/recipient-policy.test.ts:205`). `full` →
installed (`test/recipient-policy.test.ts:208`). Both directions carry killing mutants, which I
reran: `install whenever policy exists` and `never install` both KILLED. **authorize: literal
`true` only** — `:127` requires `reply(...) !== true` to bail and `:149`/`:126` likewise, and
`:141-149` is a pure boolean conjunction; `rev-focused` reran
`literal true ceiling only: truthy, promise, throw refuse without changing admission`, and I
killed the `truthy host ceiling accepts` mutant.

**3. `reply` optional and fail-closed — CONFIRMED.** Declared optional at `:30`. Resolver bails
on `!reply` (`:122`). `replyIntent` is emitted only under
`mayWake === true && typeof channel === 'string' && replyScope && automaticReplies !== false && reply`
with `reply(...) === true` (`:207-210`); a `reply` throw is caught and leaves the verdict
untouched (`:212`). `inference/request` returns `{store:true,mayWake:true}` at `:190`, before
the intent block is reachable; channel-less work fails `typeof channel === 'string'`. The
resolver's lane gate `:116` precedes any intent check or `trusted.lookup`. I killed
`absent reply defaults allow`; `intent for nonwake work` and `intent for disabled replies`
exist in the runner and are covered by the reused 16-kill receipt.

**4. The `e276024a` annotation — CONFIRMED as a compiler-checked contract.** `git diff 536b2e23 e276024a`
is exactly two files: the return annotation on `src/recipient-admission.ts` (+19/-1, zero
runtime lines) and the new `check-return-controls.mjs`. `grep` for
`@ts-ignore|@ts-expect-error|@ts-nocheck|: any|<any>|as any|as unknown|eslint-disable` in the
runtime file returns **NONE**. Only `inboundAdmission` and `replyAuthority` are optional, plus
`replyIntent?` inside `admit`'s success result — all three genuinely conditional per §2.5.
Every nested member is required with an exact type; the two `unknown`s
(`isReplyIntent: (value: unknown) => boolean`, `replyIntent?: unknown`) are **parameter**
positions, which strengthen rather than weaken. The only new `as ` is the `as const` at `:210`,
matching the frozen .3 idiom at `:159,190,214`; my mutations prove it does not hide missing or
wrong-type members. All four shipped negatives discriminate on rerun, and my three independent
deeper mutations were all caught. The one real limit is excess members — LOW finding #1.

**5. Re-baselining map complete — CONFIRMED.** The `test/recipient-policy.test.ts` diff is
+11/-8 and touches **only** the nine cells named in §2.5 (`:203, :208, :213, :222, :223, :233,
:234, :235, :281` at base numbering). Seven of the nine are **pure additions** of
`not.toHaveProperty('replyIntent')`; no existing `toEqual` exact verdict was weakened or
relaxed anywhere in the file. `:203` is the title change; `:208` is the authorized
`toThrow('recipient_reply_authority_pending')` → `.replyAuthority).toBeDefined()` swap. No
other assertion in the file changed. `test/stubs/f-host-3/run-mutants.mjs` shows `:23`
retargeted to the restated title and `:24` ("full silently offered before B") retired, exactly
as §2.5 directs. **Witnesses are not zero-match**: both runners require a printed `(fail)` line
containing the witness string (`f-host-4/run-mutants.mjs:49`, `f-host-3/run-mutants.mjs:49`),
so a zero-test selection is an assertion failure, not a kill — and my rerun produced
`KILLED feature-off installs callback: off installs neither callback; admission-only installs no B/D; full installs B`,
a named kill on the retargeted witness.

**6. v1 stubs and feature-off — CONFIRMED.** All four `test/stubs/f-host-3/v1/` files are
byte-identical base↔head, and their SHAs match the implementer report exactly:
`a-contract.ts f8704bf2…`, `static-policy.ts 568019a3…`, `tsconfig.json 8d1da823…`,
`typecheck.ts c14bf549…`. `test/stubs/f-host-2a/` is untouched; the only `f-host-3` change is
`run-mutants.mjs`. I reproduced feature-off at the head independently: 3,974 bytes, SHA256
`5af23aaa62c8996f61dc32caf50443b233f92b6e0675a68f98cbb88d97936e82` — the claimed value. The
base side is covered by `compare-feature.py`, which extracts the single `{"final"…}` line from
each cell log and asserts byte equality, so it is a real comparison rather than a size check.
The v2 compiler is v1 + the authorized `replyChannels → reply` wiring only
(`v2/static-policy.ts:52,56`); v1 already carried and validated the `replyChannels` field.

**7. B is stub-of-B only — CONFIRMED, labelled honestly.** `test/stubs/f-host-4/b-model.ts:1-4`
opens "STUB-OF-B ONLY … This does NOT qualify descriptor replacement, grants, incarnation,
transport, notice or delivery." `docs/design/recipient-reply-compiler.md:19-20` assigns
descriptor identity, publish grant and incarnation to AF and states "No descriptor/grant/
incarnation qualification is claimed"; `:57-64` lists runtime binding, notice/delivery/
transport/reconnect and AF lane gating as PENDING on I6/.2a and T2 selectors on I7. Every
B-behaviour `describe` is prefixed `stub-of-B`. The AF contract is pinned by bytes, not
imported (`check-b-contract.mjs:10-21` verifies the three v2 drop files against their SHA256s
and the quoted ranges by substring). The §2.4 inventory citations are **real**, spot-checked
against source: `src/recipe.ts:1528-1536` is the `idleTtlMs` expiry/`closurePrompt` closure-turn
block, `:1476-1482` the `proseRouting` delivery/latest-inbound locus rule, `src/index.ts:503,525,530`
the conversations/dendrite wiring, and `test/conversations-recipe.test.ts:24` is literally
`closurePrompt: 'Finalize the case report and post the answer.'`. The blocker is labelled
"concretely found cases, not an exhaustive activation predicate" with no affected-agent claim
(`docs/design/recipient-reply-compiler.md:33-34`), and `:44-49` explicitly refuses to let
"delivery without a locus stays private" dispose of fork-home/latest-inbound/focus origins.

**8. Reruns — DONE** in the staged root, which I confirmed is at `e276024a`. Table above.

## Unmet criteria / test gaps

- Acceptance (b) runtime binding: **correctly PENDING**, not claimed. Not a gap.
- `replyAuthority`'s key set is asserted nowhere (LOW #1); no mutant targets `unboundProse` (LOW #4).
- No cell pins the resolver's lane ceiling against post-compile mutation of `policy.lanes` (LOW #2).
- `automaticReplies.timeoutMs` has no compiler-core range control (LOW #3).
- Not re-verified by me: `docs-contract` (no reusable receipt was supplied to this review; the
  implementer declares it FAILED on the declared-filename false positive / TASK-EC6WW, matching
  brief §4's stated expectation) and the full suite (reused receipt `f4-final-suite`, exit 0).
- Carried forward unchanged, per brief §3: the `check-scope-contract.mjs` reply-oracle
  mismatches (`destination.mappingId` absent from `ReplyDestination`; `enums.lane` lacks
  `inference/request`) remain a recorded T1/T2 gap, out of .4 scope.
- The supplemental zero-selector helper remains FAILED by operator decision (Bun 1.4.3 emits
  `error: regex … matched 0 tests`, not `0 pass`). The substantive guarantee **does not depend
  on it and is now proven** by the separate narrow control above: Bun exits 1 on a 0-match
  selector, and both runners reject an empty selection and a missing named `(fail)` witness.

## Residual risk

1. **I6 integration is the real risk surface, not this diff.** Everything qualified here is a
   pure compiler seam behind `recipient_policy_integration_pending`. The three hardening
   findings (#1–#3) all become reachable only once a real loader and a real AF front-end exist.
2. `trusted.lookup` remains a versioned double. Unique-registration, frozen binding, descriptor
   identity, publish grant and incarnation are unqualified and explicitly AF's.
3. The `approvals` WeakMap keys on object identity, so authorize refuses an equivalent-but-distinct
   input object. That is fail-closed and matches B's frozen-binding model, but it is a real
   coupling assumption about how AF reuses the input object — worth an explicit I6 check.
4. The §2.4 inventory is a bounded, non-exhaustive snapshot by construction. Activation of the
   timer-closure/home/latest-inbound paths still needs an owner decision (items 6/7); this
   review does not and cannot clear them.
5. Naming qualification stays PARTIAL and docs-contract stays FAILED; no blanket green-gates
   claim is supportable, and the implementer does not make one.

## Fix directions

Nothing blocks acceptance (a). Suggested, in priority order:

1. Assert `replyAuthority`'s exact key set in `test/recipient-reply.test.ts:48-50`, and add an
   excess-nested-member case to `check-return-controls.mjs` that binds the literal to the
   annotated type directly rather than through `Object.freeze`.
2. Make `replyChannel` consult the detached `lanes` set instead of `policy.lanes` (`:117`), and
   add a mutate-after-compile cell.
3. Range-check `automaticReplies.timeoutMs` in compiler-core, or hand it to the I6 reviewer as a
   named precondition if a new error string is out of .4 scope.
4. At I6, drive the activation-blocker cell through the real B consumer.

## Review hygiene

My worktree is untouched: `git status --porcelain` empty, `HEAD` still `e276024a`, no commits,
edits or new tracked files. All my artifacts live in `$ORGASMIC_EVIDENCE_DIR`. My mutation
probes wrote only to the staged build root and restored it in `finally`; I verified afterwards
that all 494 `src/`/`test/` files there are byte-identical to `HEAD` (differs=0) and that no
stray files remain in `test/stubs/f-host-4/v2/`.
