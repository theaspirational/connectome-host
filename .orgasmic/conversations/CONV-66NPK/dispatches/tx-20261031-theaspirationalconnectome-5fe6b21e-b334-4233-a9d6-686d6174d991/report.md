# CONV-66NPK — contract check of T2CORE-BRIEF-v2 (read-only, pre-implementation)

Brief `/srv/review-drop/tarn-rtaln-20261030/T2CORE-BRIEF-v2.md`, sha256
`e06e00fd497733e0d27e4c9d999b1225150c2573c7565cc8297ae8630b864590` — **verified, matches**.
Read-only check: `git show`/`git grep`/`git log`/`git merge-base`/`sha256sum` only. No build,
install, edit, network or gate run.

## Verdict

**READY WITH CHANGES** — required: H1, H2, H3, H4, M2.

Every citation, sha and line pin in the brief resolves and says what the brief claims
(detail below), and §1's gap-status table does **not** overstate closure. The problems are
all in §2/§3/§4: three pieces of the proposed work are not implementable against the frozen
`aecd4a8c` seam as written (H1, H3), one proposed rule is fail-open against T1 (H2), and the
proposed fact-access mechanism cannot be realised without one of two named inventions of
authority (H4), with an unsatisfiable typecheck beside it (M2).
These are brief-scope defects, not code defects; an implementer following §3 verbatim would
hit a §6 stop mid-slice, build a widening default, or claim a gate that cannot run.

---

## Findings

### HIGH

**H1 — `T2CORE-BRIEF-v2.md:79-84,90-93`: §3.2 parent-rule and §3.4 ref/current work are not
reachable from the frozen seam; both routes hit a §6 stop or test only the double.**
`src/recipient-admission.ts:39-42` @ `aecd4a8c` is the whole seam:
`lookup(key, channelId) -> VerifiedScope | undefined` and `readable(scope, key)`. It
**discards `ref` and `descriptor`**, which AF's view does return
(`src/mcpl/registration-view.ts:23` @ `a0970645`), and it exposes **no `current`**. The
compiled ops take `(key, channelId, scope, input)` only (`recipient-admission.ts:28-30`).
Consequences:
- The only T1-sanctioned way to evaluate a child's parent rule is descriptor-claim-as-
  `lookup()`-key plus `ref` **identity** equality against `scope.parent`
  (T1 `:115-118` @ `bff8c46e`). Neither the claim nor the ref is reachable in the seam.
- T1 puts parent read-scope evaluation in the **projector**, not the selector: "The projector
  may read already accepted parent references through the same view"
  (`generic-recipient-scope-contract.md:311-313`), matching I7 `project(key, candidate,
  descriptor, signal, registrations)` (`registration-view.ts:29-31`). §2 lists "real
  projector/verifier" as **still missing**, so §3.2's parent clause is assigned to a
  component this slice does not build.
- §3.4 says "capture `ref` before an await and check `current(ref)` after it, **in the
  compiled ops**". The ops are synchronous booleans with no awaits and no ref; the only real
  post-await boundary in `aecd4a8c` is `replyAuthority.resolve` -> `authorize`
  (`recipient-admission.ts:132-150`, re-running `replyChannel` at `:148`). AF already owns the
  admission-await recheck (`registration-view.ts:135-150`).
*Failure scenario:* the implementer reaches §3.2, finds no ref/descriptor, and either widens
`TrustedRecipientRead` (= "edit to .3/.4 production code", §6 last clause -> stop, mid-slice)
or implements parent evaluation wholly inside `scope-double.ts`, producing a row labelled
`stub-of-AF-view` that exercises the double's own closure and **not one line of host selector
code** — a test that passes without exercising the criterion.
*Fix:* split §3.2. Keep in-scope: `readable`, `joined`/`joinedReadable`, `dmUserId` identity,
`kind === 'sub-conversation'` plus presence of `scope.parent`. Move out of scope, with named
owner: parent read-scope and claim/native agreement (projector slice), `current(ref)` barriers
(T4). If parent evaluation must stay, state it as an explicit §6-approved closed-list widening
of `TrustedRecipientRead` (see H4 Option B) and re-estimate — do not leave it implicit.

**H2 — `T2CORE-BRIEF-v2.md:76-78` and `:131`: "an absent selector is the existing H static
behaviour (feature off)" contradicts T1 `:44` and is fail-open.**
T1 @ `bff8c46e` `:44-45`: "Missing selector/boolean/list means deny; unknown/invalid configured
fields fail startup. **Empty lists mean deny, not all.**" T2-input @ `9d022502`
`f-host-t2-brief.md:111`: "Missing members deny". T1 contains **no** "feature off" notion at
all (`grep -i 'feature.off|disabled|not configured'` over T1: zero hits). §6's stop condition
"A default for an absent selector other than 'feature off'" **encodes the wrong default as the
guard**, so the error cannot be caught by the stop list.
*Failure scenario:* an operator configures a dynamic block with `read` only. Per §3.1 the
absent `wake` falls back to "the existing H static behaviour" — i.e. the static `wakeChannels`
set (`test/stubs/f-host-4/v2/static-policy.ts:52-57`). A dynamic opt-in then silently carries
static wake authority, which is exactly T2-input `:132`'s mutant-to-reject ("Route H static
policy through dynamic defaults", "no selector fallback"). §3.1 omits T1 `:45`'s empty-list
rule entirely.
*Fix:* separate the two levels explicitly. (i) No dynamic/`recipientPolicy` block at all =>
feature off, existing path byte-unchanged. (ii) Block present, any of `read`/`wake`/`reply`
absent => **deny that operation** (T1 `:44`), never fall back. (iii) Selector present, member
missing or list empty => deny (T1 `:44-45`). Rewrite §6's bullet to "any fallback from an
absent selector inside a present dynamic block to static behaviour or to 'all'".

**H3 — `T2CORE-BRIEF-v2.md:94-97`: three of the five `t2-off/*` controls are not feasible
against `aecd4a8c`, and §3.5 silently re-bases the comparison from H to .4.**
T2-input `:131-135` defines these controls against **H's actual serialized outputs and ordered
effect deltas**, and `:141` states "No byte-equivalence result can be claimed before H exists".
§1 itself says the production loader, overlay boundary and callback installation are **still
missing**. Against `aecd4a8c`:
- `t2-off/automatic-replies` — feasible (`recipient-admission.ts:130` `replyAuthority ===
  undefined` when `automaticReplies === false`; already covered by
  `test/stubs/f-host-4/check-return-controls.mjs`).
- `t2-off/no-policy` — feasible only at seam level (`recipient-admission.ts:104`
  `if (policy === undefined) return {}`; existing mutant `test/stubs/f-host-3/run-mutants.mjs:23`).
  "Same H **config output bytes**" is not: `recipientPolicy` is refused outright at the loader
  (`src/recipient-policy.ts:10-14,32-33`), so no H-with-policy config output exists to diff.
- `t2-off/static-baseline` — only as **stub-vs-stub** (vs
  `test/stubs/f-host-4/v2/static-policy.ts:50-58` `compileStaticRecipientAdmission`), not vs H.
- `t2-off/reconnect` ("Same feature-off initialize/grants/reconnect as H; zero scope
  lookup/projector calls") — **not feasible**: initialize/grants/reconnect are A/AF-owned
  (`recipient-admission.ts:19-22`: "A owns feature-grant/hook/render/queue and live epoch
  checks") and there is no installed callback path to observe without H's loader.
- `t2-off/unconfigured-peer` ("masks/tools/context hooks/prose/typing unchanged") — **not
  feasible**: none of those surfaces is reachable from the compiler seam.
*Failure scenario:* §4 lists "feature-off byte compare against aecd4a8c" as a gate. Two of its
five named controls have nothing to compare, so the gate either reports PASS on a control it
did not exercise, or blocks the round at gate time.
*Fix:* in §3.5 name the two feasible controls, label `static-baseline` as a stub-vs-stub
compiler-seam equivalence (explicitly **not** the H byte-equivalence of T2-input `:131-135`,
per `:141`), and defer `reconnect`/`unconfigured-peer` to the loader/overlay slice. Mirror that
split in §4's gate list.

**H4 — `T2CORE-BRIEF-v2.md:55-63`: the proposed fact accessor has no admissible source of
truth — either realizable form commits a named invention, so this part STOPS per §2's own
clause unless the reader is injected instead.**
Framing first: a *read-only* fact accessor is **not** inherently a constructor, and this
finding does not rest on that word. It rests on the accessor's source of truth. A reader needs
a populated fact table, and in `src/` there are exactly two ways to populate it:
- **(i) a writer in `src/`.** The brand is `{ readonly [verifiedScopeIdentity]: never }`
  (`src/recipient-scope.ts:7` @ `aecd4a8c`), so a fact-bearing `VerifiedScope` can only exist
  via a cast plus a side table. If the table lives in `src/recipient-scope-facts.ts` (it must,
  to be read there), the double can only fill it through an exported writer — i.e. **brand
  minting with no verifier, reachable from production**. `package.json` @ `aecd4a8c` has **no
  `exports` map and no `typesVersions`** (verified by grep) and `files` ships `"src"`
  (`:9-11`), so that writer is importable by any `src/` caller and by any consumer via
  `@animalabs/connectome-host/src/recipient-scope-facts.js`. §6's "exported
  constructor/accessor" stop never fires, because it is not *root*-exported.
- **(ii) no writer — the double casts its own object and `src/` decodes it structurally.** That
  is **decoding an arbitrary object**: the accessor would trust the shape of whatever was cast,
  with no verifier and no provenance, which is precisely what
  `src/recipient-scope.ts:1-4` ("No authority factory or made-up fact members are exported
  while its verifier/projector API is PENDING") and T1 `:134-138` ("Unknown, contradictory,
  incomplete, stale or unverifiable evidence fails closed") forbid.
*Failure scenario, form (i):* `attach()` lands in `src/`; a later `src/` caller — or a consumer
deep-import — mints a fact-bearing `VerifiedScope` with no verifier, and the fail-closed
property §2 claims is gone. *Form (ii):* `factsOf()` returns facts for any object shaped like
`VerifiedRegistrationScope`, so descriptor-derived or ACK-derived data reaches a selector as
authority.
*What is NOT the finding:* the brief's own word "constructor", and the naming/packaging
question generally (L6). Test-double facts being test-only is correct and not at issue; the
absence of production issuance/binding is correctly recorded as an open gap (`:35-36,64-66`).
*Fix (Option A — the only form with neither failure mode): do not add a `src/` module at all.*
Make the selector compiler generic over the fact type with the reader **injected**:
`compileDynamicSelectors<F>(config, facts: (s: VerifiedScope) => F | undefined)`, with both the
branded scopes and the reader supplied from `test/stubs/f-host-t2/*`. The source of truth is
then the caller, production has no implementation to pass and no object to decode, it fails
closed by construction, and `recipient-scope.ts:3-4` stays literally true. *Option B,* if H1
forces a production-resident reader: add it as one optional member on the existing seam
(`TrustedRecipientRead.facts?(scope)`, plus `ref`/`current` for H1) as an explicit §6-approved
closed-list edit to `src/recipient-admission.ts:39-42` — one reviewed seam, not a new
free-standing module. Under **neither** option may the reader synthesise `mappingGeneration`
equality or any other generation authority (§6; see check item 5).

### MEDIUM

**M2 — `T2CORE-BRIEF-v2.md:47-54`: the "assignable to and from the pinned
`VerifiedRegistrationScope`" typecheck is unsatisfiable as specified.**
§2 says the double's scopes carry "the I7 `VerifiedRegistrationScope` facts **plus an opaque
`ref` / `current()` model**", and that a typecheck proves the fact type is "assignable to and
from" the pinned `VerifiedRegistrationScope` (`registration-view.ts:13-21`). Mutual
assignability requires structural equivalence; required extra `ref`/`current` members make the
double's type assignable **to** the interface but not **from** it.
*Failure scenario:* the `t2` typecheck gate is written, one direction errors, and the
implementer "fixes" it by widening the asserted type or marking the extras optional — which
destroys the point of the pin.
*Fix:* declare two types. `VerifiedScopeFacts` = structurally exactly
`VerifiedRegistrationScope`, with the bidirectional assignability assertion on that type only;
`ref`/`current` sit in a separate view-model record keyed beside it, with its own one-way
assertion against `RegistrationView` (`:22-25`).

**M3 — `T2CORE-BRIEF-v2.md:25`: the I7 row calls `a0970645` "reviewed"; it is a LOW round one
commit past the APPROVEd head, with only inherited review.**
`/srv/review-drop/TASK-S4Q8F-FIX3-REVIEW.md` (sha256 `05578797…`) APPROVEs at
`29b5ebf33ca5d785564cdd85c4609543d8202d57`. `/srv/review-drop/TASK-S4Q8F-LOWS4.md:5,7` records
`a0970645` as the LOW follow-up head and states its "r3 APPROVE is **inherited review context,
not a new-head test pass**". The brief discloses exactly this structure for the .4 pin
(`:24` "LOW round … not re-reviewed") but not for I7 — asymmetric.
Material risk is low: `src/mcpl/registration-view.ts` is **byte-unchanged** between
`29b5ebf3` and `a0970645`, so the pinned signatures are within reviewed scope. But
`docs/registration-scope-view.md` — the other half of the I7 pin — changed **+11/-4** in the
unreviewed commit (fork/composer rules, not the lifecycle rules the selectors rely on).
*Fix:* re-label `:25` as "signatures reviewed at `29b5ebf3` (TASK-S4Q8F-FIX3-REVIEW
`05578797…`); `a0970645` is the LOW round beyond it, not re-reviewed (TASK-S4Q8F-LOWS4);
`registration-view.ts` unchanged across that delta, `docs/registration-scope-view.md` +11/-4".
Worth also noting `/srv/review-drop/TASK-S4Q8F-FROZEN-HEAD-VALIDATION.md` was **BLOCKED on a
confirmed opt-in batch-isolation defect** at `47f3b4f2`; the eight commits `47f3b4f2..a0970645`
are that fix (`b52dd8c4` "isolate registration capture for each batch item" onward). Cite that
lineage so the pin's history is not re-derived later.

**M4 — `T2CORE-BRIEF-v2.md:111-112`: the new T2 files will escape the docs-contract/neutrality
scan, and the brief itself would fail it.**
`test/fixtures/generic-recipient/check-scope-contract.mjs:336-351` @ `aecd4a8c` scans a
**hard-coded** list (f-host-2a and f-host-3 files only). T2-input extended that list in its own
round (`f-host-t2-brief.md:238-239`), but `9d022502` is not an ancestor of `aecd4a8c`, so the
extension is absent — and the f-host-4 files are already unscanned (pre-existing, not a T2
regression). §4 says only "docs-contract (report the exact result per checker sha)".
*Failure scenario:* `scope-double.ts`, the new tsconfig/typecheck/runner and the new design doc
land, the gate passes, and none of them was checked for machine-local paths, internal task ids
or credential-shaped text.
*Fix:* make §4 require adding every new T2 file to `check-scope-contract.mjs:336-351` as part
of the slice. Separately: this brief cites `/srv/review-drop/...` paths and `TASK-4ZM52` /
`TASK-S4Q8F` ids, which the checker's own denial controls reject ("machine-local path",
"internal identifier"). The committed design doc must carry shas only — as T2-input's committed
brief does.
*Caveat on the gate's reach (not a substitute for M4's fix, an addition to it).* `neutral()`
(`check-scope-contract.mjs:227-236`) is **literal regex over raw file text**. A
split/reconstructed literal evades every one of its assertions — the checker demonstrates this
on itself, building its own denial controls at runtime via `.join()` precisely so that scanning
its source raises no hit (`:226,364-374`). The new T2 artifacts are `.ts`/`.mjs` code, where
concatenated path/id fragments are idiomatic, so adding them to the list is necessary but not
sufficient: §4 should require a human neutrality read of the new stub/runner sources, and
should not report the docs-contract pass as covering them. My own M4 claim does not depend on
grep: it rests on list membership in the hard-coded array at `:336-351`, which I read
directly.

**M5 — `T2CORE-BRIEF-v2.md:103-113`: §4 drops the matched-base gates that .4 §4 required.**
`FHOST4-BRIEF-v4.3.md` §4: "tsc base/head … full suite **base/head** … Matched base =
c353e1c8 (fresh, or reused with a bindings table, labelled)". §4 here says only "full suite
once at the final head".
*Failure scenario:* a suite failure at the T2 head cannot be classified pre-existing vs
regression without re-running the whole slice. The §3.5 comparison "against frozen .4
aecd4a8c" needs a base run regardless, so the gate is self-inconsistent.
*Fix:* restore matched base = `aecd4a8c` (fresh or reused with a labelled bindings table) for
tsc and the full suite, and state it as the baseline for the feature-off comparison.

**M6 — `T2CORE-BRIEF-v2.md:98-101`: the 49/8 counts are correct, but "each labelled
stub-of-AF-view" over-claims for rows with no distinct host-compile witness.**
Counts verified at `9d022502:docs/design/f-host-t2-brief.md:172-228`: 57 rows total, 49 `T2`,
8 `T3/T4 only` (breakdown: 38 `T2 +T4`, 6 `T2 +T3/T4`, 5 `T2 +T5`). The "stub-of-AF-view" label
is honest and matches the existing `stub-of-B` convention
(`test/stubs/f-host-4/check-compiler-controls.mjs` final line). Rows that have **no** host
portion in compiler-core:
- `dynamic-no-incoming-grant`, `t03-publish-grant-revoked` — grants are A-owned
  (`recipient-admission.ts:19-22`); only a negative-space "the compiler never consults a grant".
- `dynamic-registration-replaced-during-admission` (mutant: "omit post-await admission
  generation check") — `admit()` is synchronous (`recipient-admission.ts:170-217`); the barrier
  is AF's (`registration-view.ts:135-150`), T1 `:332-334`.
- `dynamic-unknown-native-root` — native truth, which T2-input `:168` says T2 "cannot test by
  setting a fixture boolean".
Also a cluster (`dynamic-use-before-registration`, `-push-before-registration`,
`-rejected-registration`, `-arbitrary-registration`) collapses onto one witness: `lookup`
returns `undefined` => deny (`recipient-admission.ts:199`).
*Fix:* apply T2-input's own precedent (`:230-233`, where `t03-native-send-access-revoked` is
marked witness-only) per row: distinguish rows yielding an independent host witness from rows
recording only the uncovered witness, and say which rows share a witness. Do not report the 49
as 49 independent controls.

**M7 — `T2CORE-BRIEF-v2.md:76-77`: "effective reply = reply ∩ read" needs evaluation-time
conjunction, because the reply path never calls `read`.**
`recipient-admission.ts:113-129` (`replyChannel`, used by both `resolve` and `authorize`)
checks lane, `lanes`, `validLaneItem`, reply intent, channel agreement, `trusted.lookup`,
`trusted.readable` and `reply(...)` — it **never** invokes `read(...)`. (On the ingress path
the intersection is structural: `:199-200` denies on `read` before evaluating `wake`.)
*Failure scenario:* a compiled `reply` selector that satisfies the static subset check but
whose dynamic read facts (`joined`, `dmUserId`, parent) deny, approves a reply through
`replyAuthority.resolve` — the exact T1 `:66` invariant the brief claims.
*Fix:* §3.1 must require the compiled `reply` selector to conjoin the full read predicate at
evaluation time, not merely pass a static non-subset check; same for `wake` wherever it is
reachable without a prior `read`.

**M8 — `T2CORE-BRIEF-v2.md:137-140`: the estimate's share of the `:703` row does not fit the
slice's own exclusions.**
`generic-recipient-host.md:703` @ `a316200c` gives 12-20 h for all of T2 ("dynamic scope
compile/**bind** coverage **and feature-off controls**"). This slice claims 10-16 h while §5
explicitly excludes runtime binding and §3.5 defers most feature-off work (H3) — leaving 2-4 h
of the row for the loader/overlay boundary, callback installation, real bind and the
feature-off controls against H. 10-16 h for 49 rows plus a double, a pinned drop, five
typechecks, a new mutant runner and the gate matrix is plausible as *effort* (≈12-18 min/row,
tight); the implication for the remaining T2 is not.
*Fix:* state what the 10-16 h charges against `:703` and say plainly that the residual T2
(real bind + feature-off vs H) needs its own incremental estimate, or that `:703` is
overcommitted. Keep it labelled an operator estimate, as it is.

### LOW

**L1 — `:70-84`:** §3 omits two T1 rules that the rows depend on. T1 `:54` "Addressedness still
gates wake" (currently emergent from `recipient-admission.ts:205-206`, not a stated selector
requirement) and T1 `:89` "a readable channel that is listed but **not joined** is denied"
(T2-input `:114`; rows `dynamic-listed-joined-channel`, `dynamic-listed-but-not-joined`). §3.2
ties `joined` only to `joinedReadable`. State both explicitly so a refactor cannot drop them.

**L2 — `:48`:** `test/stubs/f-host-t2/v1/` has no `v0` in `aecd4a8c`'s tree (`9d022502` is not
an ancestor — confirmed), while `f-host-3/v1` already exists, so §4's "v0/v1/v2/t2" gate names
are ambiguous. Decide whether §8's "closed, cited copy" lands `f-host-t2/v0` first, or name the
new directory `v0`; then make §4 name full paths rather than bare version letters.

**L3 — `:26`:** the A/B pins are AF commits, listed without naming the repo (they do not
resolve in the host clone). Both verified ancestors of `a0970645`. Add the repo.

**L4 — `:99`:** "each labelled `stub-of-AF-view`" over-applies to the five `+T5` rows
(`a04-*`, `a03-bad-static-mapping`), which compare against the source-derived `oldStore`/
`oldWake` Mapping X oracle (T1 `:386-393`), not the AF view. Use a second label.

**L6 — `:57-58`:** the naming/packaging question on its own — whether "module-private" and
"not exported from the package root" are accurate descriptions given the absent `exports` map —
is a wording issue, not a blocker; it matters only as the mechanism that makes H4's form (i)
production-reachable. Fix the wording with H4.

**L5 — `:112`:** §4 says "docs-contract (report the exact result per checker sha; no
retroactive pass)" where .4 §4 named the expected outcome ("expected still FAILED on the
declared filename FP — report as such, never 'all gates pass'"). Name the expected result so a
FAIL is not re-litigated at gate time.

---

## Citation / pin verification (check item 1 — all resolve)

| Brief claim | Result |
| --- | --- |
| Brief sha256 `e06e00fd…` | **matches** |
| T1 `bff8c46e3a4c…` | resolves; ancestor of `aecd4a8c` |
| T1 `:40-90` selector semantics | `:40` = "A proposed compiled policy has three independent selectors"; `:90` = end of the provisional-mapping section. **correct** |
| T1 `:70-90` serializable spelling is an engineering call | `:75` "final serializable field spelling is an engineering call". **correct** |
| T1 `:298-334` view shape | `:298` "Extend the opt-in binder with an epoch-bound read-only registration view"; `:334` "A verified sidecar must never be read from persisted rows". **correct** |
| T1 `:324-326` VerifiedScope facts | "receiving account, kind, readable/joined facts or DM user identity, exact parent RegistrationRef … **opaque immutable mapping generation**". **correct** |
| T1 `:336-339` packaging open | "Final interface spelling/packaging is AF-scope engineering … no implementation or release artifact is produced in T1". **correct** |
| T1 `:350-394` fixtures/timeline | within "## 5. Public fixtures and gates" (header at `:344`). **correct** |
| T1 `:94-138` descriptor is never authority | trust-boundary/evidence-origin section. **correct** |
| `generic-recipient-host.md:703` @ `a316200c`, 12-20 h | exact line; text verbatim. **correct** |
| T2-input `9d022502fa10…`, 4 INPUT GAPs, `t2-off/*`, 57/49/8 | resolves; 57 rows, 49 `T2`, 8 `T3/T4 only`. **correct** |
| **`9d022502` is NOT an ancestor of `aecd4a8c`** (`:144-146`) | `git merge-base --is-ancestor` => **NOT an ancestor. correct.** `test/stubs/f-host-t2/v0/{stubs,typecheck}.ts,tsconfig.json` exist at `9d022502` and are absent from `aecd4a8c` |
| .3 `c353e1c8d19a…`: ops, `TrustedRecipientRead`, opaque brand only | all exact (`recipient-admission.ts:24-42`, `recipient-scope.ts:1-7`); `recipient-scope.ts` byte-identical `c353e1c8`..`aecd4a8c`. **correct** (`wakeWithoutChannel` is a boolean field, listed among "ops" — harmless) |
| .4 `aecd4a8cafe4…`: optional fail-closed `reply`, eligible-only `replyIntent`, 1..60000 guard, detached lanes, production refused | `recipient-admission.ts:30,209-215,152-156,53-81`; refusal `src/recipient-policy.ts:6,10-14`. **correct** |
| .4 provenance: `e276024a` APPROVE, `TASK-4ZM52-REVIEW1.md 8f7c7341…`, `LOWS1 dfc8a3c7…`, not re-reviewed | both sha256 **match**; REVIEW1 "APPROVE — HIGH confidence"; `e276024a` is an ancestor and `aecd4a8c` is exactly one commit beyond it; delta reproduces (5 files, +63/-6). **correct and honestly qualified** |
| I7 `a0970645…`: `RegistrationRef`, `RegistrationView`, `VerifiedRegistrationScope` fields, `RegistrationScopeConfig.project` | `registration-view.ts:6-7,13-21,22-25,26-32` — **field-for-field exact** |
| I7 "reviewed" | **overstated — see M3** |
| A `869bb3ad…` / B `29edcf8b…` | resolve in the **AF** clone (not the host clone — L3); both ancestors of `a0970645` |

## Answers to the remaining check items

2. **§2 separation / §1 overstatement.** §1 does **not** overstate closure; each gap row is
   accurate and gap 2's "issuance still missing" is right (AF has `project()`/`accept()`, but
   in AF, unreleased, host dep is `^0.30.0`). Suggested precision: T2-input's gap-1 requirement
   list has six items — `aecd4a8c` supplies lane/envelope checks, independent A/B timeouts,
   attestation and a *stub* static admission/reply; it does **not** supply the config/overlay
   boundary or feature-off controls, which is exactly what H3 depends on. Say which. §2's "what
   H supplies" is accurate but its *sufficiency* claim is wrong — H1 (ref/descriptor dropped),
   H4 (no admissible source of truth for the fact reader), M2 (unsatisfiable typecheck).
3. **Selector work vs T1 `:40-90` / "Planned host mapping".** Matches on independence,
   intersection-with-read, unknown => fail, DM by identity, and no implicit "all". Diverges on
   missing => deny (**H2**), omits empty-lists-deny (H2), addressedness and listed-but-unjoined
   (L1), and needs the reply-path conjunction (M7). The sub-conversation clause contradicts
   nothing in T1 but is unreachable from the .4 seam (**H1**).
4. **`src/recipient-scope-facts.ts`.** A read-only fact accessor is **not** inherently a
   constructor and I do not block on the word. The defect is its **source of truth**: in `src/`
   the table can only be populated by an exported writer (brand minting with no verifier,
   production-reachable because there is no `exports` map and `files` ships `src`) or by
   structurally decoding a cast object (trusting unverified shape). Both are real inventions of
   authority, so **HIGH/STOP (H4)**; the naming/packaging question on its own is **LOW (L6)**.
   Safer alternative that commits neither: inject the reader (H4 Option A) — test-double facts
   stay test-only, production has nothing to call and nothing to decode, and the missing
   production issuance/binding remains an explicit gap, as §2 already says.
5. **I7 field sufficiency.** Sufficient for `channels` (channelId arrives separately),
   `joinedReadable` (`readable`+`joined`), `dmUsers` (`dmUserId`, incl. author equality from
   `recipient-admission.ts:74-75` on `channels/incoming`), account match, and
   `subConversations` presence. **Insufficient** for the parent's *own* rule: there is no
   ref->scope accessor on `RegistrationView` (**H1**). **`mappingGeneration` is not needed**:
   replacement is caught by `current(ref)`'s record-identity check
   (`registration-view.ts:80`), so §6's stop is correctly placed and not triggered — but
   evaluate it per row, since `t03-moved`/`t03-replaced-equal-ids` host portions rest entirely
   on the double's re-registration model, and an in-place-mutation variant would need an
   equality rule (=> §6 stop, ask).
6. **`t2-off/*` feasibility.** Two of five feasible, one stub-vs-stub, two not: **H3**.
7. **49/8 and labelling.** Counts exact; labelling honest but over-broad: **M6**, **L4**.
8. **Estimate.** Effort plausible but tight; the share of the `:703` row is not: **M8**.

## Verification Gates

**None run** — the dispatch brief scopes this to a read-only contract check (no builds,
installs, edits or network; `git show`/`grep`/`sha256sum` only), and the T2 brief itself
specifies gates for the *implementation* round. Evidence is therefore inspection-only:
`sha256sum` on the brief and the four review drops; `git log -1`, `git merge-base
--is-ancestor`, `git diff --stat`, `git ls-tree`, `git grep` and `git show` over
`/home/orgasmic/src/connectome-host` @ `bff8c46e`, `9d022502`, `a316200c`, `c353e1c8`,
`e276024a`, `aecd4a8c` and `/home/orgasmic/src/agent-framework` @ `29b5ebf3`, `47f3b4f2`,
`a0970645`, `869bb3ad`, `29edcf8b`. Every file:line in this report was read at the stated pin.

Toolchain feasibility note for §4: `npx --no-install tsc` is already exercised by
`test/stubs/f-host-4/check-compiler-controls.mjs` and `check-return-controls.mjs` at
`aecd4a8c`, so the typecheck gates are runnable — T2-input's "typechecking is PENDING"
(`:103-105`) no longer applies at this base. The named gate scripts exist
(`test/stubs/f-host-3/run-mutants.mjs`, `test/stubs/f-host-4/run-mutants.mjs`,
`test/fixtures/generic-recipient/check-scope-contract.mjs`).

## Unmet criteria / test gaps

- No executable artifact exists yet; nothing in the brief is implemented. All gaps are
  specification gaps.
- H1: as written, §3.2/§3.4 rows would be satisfied by tests that exercise only
  `scope-double.ts` — passing without touching the acceptance criterion.
- H3: two named `t2-off/*` controls have no observable surface at this base.
- M4: new T2 files are outside `check-scope-contract.mjs:336-351`, so the neutrality scan would
  not cover the files this round adds (the f-host-4 omission there is pre-existing).
- M5: no matched-base run means suite failures cannot be classified.
- M6: several of the 49 rows yield no independent host witness; a shared-witness cluster is
  counted as four rows.
- M3: the I7 pin's lifecycle doc is 11 lines beyond its review (signatures are not).

## Residual risk

- **Scope-widening (highest).** H2's fallback rule is the one finding that could ship a
  fail-open path, and §6's stop list as written would not catch it.
- **Mid-slice stop.** H1 makes a §6 stop likely partway through §3.2/§3.4, after the double and
  pinned drop are built — the expensive ordering.
- **Over-reading `stub-of-AF-view`.** The label is honest, but with M6 unaddressed a reader may
  take "49 rows" as 49 independent witnesses of host behaviour. Nothing in this slice qualifies
  AF currentness, native truth, issuance or storage parity, as §5 correctly states.
- **Unverified by me.** The `#931` publication hold and the AF review-process claims beyond the
  drops cited above; AF's own test results (I inspected source and review records, ran nothing).
  `/srv/review-drop/TASK-4ZM52-LOWS1.md` and `TASK-S4Q8F-LOWS4.md` are treated as claims whose
  *reproducible* parts (deltas, shas, ancestry) I re-derived and which held.

## Fix directions (ordered)

1. **H2** — rewrite §3.1's absent-selector rule to T1 `:44-45` (three-level: no block => off;
   block with absent selector => deny; present selector with missing member/empty list =>
   deny), and invert §6's bullet to forbid the fallback.
2. **H1** — split §3.2/§3.4: keep facts-only predicates in scope; move parent read-scope and
   claim/native agreement to the projector slice and `current(ref)` barriers to T4. If parent
   evaluation stays, pre-approve the `TrustedRecipientRead` widening as a §6 closed-list edit
   (H4 Option B) and re-estimate.
3. **H3** — reduce §3.5 to the two feasible controls plus a stub-vs-stub `static-baseline`
   explicitly labelled as *not* T2-input's H byte-equivalence (`:141`); defer
   `reconnect`/`unconfigured-peer`; mirror in §4.
4. **H4** — adopt Option A (inject the fact reader; no new `src/` module) unless H1 forces
   Option B. Do not rely on "not exported from the package root" as a boundary, and in neither
   option may the reader decode an unverified object, trust descriptor/ACK fields, mint a brand
   or synthesise generation authority.
5. **M2** — split `VerifiedScopeFacts` (structurally exact, bidirectional assertion) from the
   `ref`/`current` view model (one-way assertion).
6. **M7** — require evaluation-time conjunction of the read predicate inside the compiled
   `reply` selector.
7. **M4/M5** — require the new T2 files to be added to `check-scope-contract.mjs:336-351`
   **and** a human neutrality read of the new sources (the scan is literal-text only); restore
   matched base `aecd4a8c` for tsc and the full suite; strip `/srv/...` paths and `TASK-*` ids
   from the committed doc.
8. **M3/M6/M8** — re-label the I7 pin's review status; mark witness-only and shared-witness
   rows per T2-input `:230-233`; state the estimate's charge against `:703` and the residual.
9. **L1-L5** — add addressedness and listed-but-unjoined as explicit selector requirements;
   settle `f-host-t2` versioning and use full paths in §4; name the AF repo for the A/B pins;
   use a second label for the `+T5` oracle rows; name docs-contract's expected result.
