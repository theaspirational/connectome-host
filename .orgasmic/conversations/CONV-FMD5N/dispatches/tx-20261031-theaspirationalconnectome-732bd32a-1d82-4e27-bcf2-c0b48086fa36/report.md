# CONV-FMD5N — bounded re-check of T2CORE-BRIEF-v3 (read-only, pre-implementation)

Brief `/srv/review-drop/tarn-rtaln-20261030/T2CORE-BRIEF-v3.md`, sha256
`625210b9c0c432fd82d07e5b89b46188d7e1a24d39eafe93f5171428a651f505` — **verified, matches**.
Previous check `/srv/review-drop/T2CORE-BRIEF-CHECK1.md`, sha256
`fce4db08c9c1e76237984602cf01a1c3e3aaf57c5d30cd06da9f83a169f277ad` — **verified, matches**.
Read-only: `git show` / `git grep` / `git log` / `git merge-base` / `git diff --stat` /
`sha256sum` / `diff` only. No build, install, edit, gate run or network.

All ten pins resolve in the named clones (host `bff8c46e`, `9d022502`, `a316200c`, `c353e1c8`,
`aecd4a8c`; AF `29b5ebf3`, `47f3b4f2`, `a0970645`, `869bb3ad`, `29edcf8b`).

## Verdict

**READY WITH CHANGES** — required: H1, M1, M2, M3, M4.

v3 closes the substance of all four check-1 HIGHs and all seven MEDIUMs: the fail-open
absent-selector default (H2) is replaced by three separately-pinned controls with the §6 guard
rewritten the right way round; the unreachable parent/freshness work (H1) is allocated to named
gates with acceptance; the infeasible `t2-off` controls (H3) are split with the H byte-equivalence
explicitly disclaimed; the fact accessor (H4) becomes an injected reader with no `src/` module;
M2–M8 and L1–L6 are addressed with correct citations. Soma's rules (a) and (c) are satisfied as
written.

The remaining problems are introduced *by* the v3 restructure. Rule (b) is only partly satisfied:
§3a moves the parent rule out, but §3.3's residual in-slice rule is an **affirmative admit on
parent presence alone**, which is fail-open against T1 `:65`/`:47` (H1), and §3.6's row
classification was not reconciled with the ten rows that §3a just vacated (M1). Two deliverables
have no stated home (M2, M3), and the mutant set guards the new conjunction in one direction only
(M4).

Item 3 answers up front: **yes** to both. The injected reader `<F>` is implementable against
`aecd4a8c` with **zero** `.3`/`.4` production edits — no closed edit list is needed — and the
evaluation-time `read ∧ reply` conjunction needs **no** widening of `TrustedRecipientRead`.
Evidence in M2 and §Item 3 below.

---

## Findings

### HIGH

**H1 — `T2CORE-BRIEF-v3.md:76-79` (§3.3): the residual in-slice sub-conversation rule is an
affirmative admit on parent *presence*, which is fail-open against T1 `:65` and `:47`.**
§3.3 keeps exactly one child control in this slice: "`kind === 'sub-conversation'` requires
`scope.parent` **present**". §3a moves "parent's own read-scope and claim/native agreement" to the
projector gate. Nothing in §3 supplies a *deny* in the interim, so the compiled ceiling as
specified returns `true` for a child whose `parent` ref is present but unauthorised, non-current,
cross-connection or natively unverified.
T1 @ `bff8c46e` `:65`: "A selector for a child requires both the child rule **and its verified
parent rule**." `:47`: "An explicit channel entry never bypasses current native readability, valid
registration, DM identity restrictions or **verified sub-conversation lineage**." `:137-138`:
"Unknown, contradictory, incomplete, stale or unverifiable evidence fails closed."
Soma's rule (b) requires that moving the parent rule out "didn't delete the barriers" and that
"missing/unverifiable facts never become affirmative". Presence-only satisfies the first half
(the requirement is recorded in §3a) and **violates the second**: the unverifiable parent fact
becomes affirmative scope inside this slice.
*Failure scenario:* the double supplies `{kind:'sub-conversation', readable:true, parent:refX}`
where `refX` is a registration this connection never had read scope over. The compiled `read`
returns true; `recipient-admission.ts:199` admits; `:211` can mint a `replyIntent`. The host test
for `t2/dynamic-claimed-parent-overrides-actual` or `t2/t02-parent-outside-read`
(`9d022502:docs/design/f-host-t2-brief.md:204,196`) then passes **without exercising its own
criterion**, and its stated mutant ("ignore actual parent scope", "authorize child without allowed
parent") is unkillable because the brief specifies the mutated behaviour.
*Fix:* make compiler-core **deny-by-default for children**. In-slice rule: `kind ===
'sub-conversation'` ⇒ **deny** unless a projector-supplied parent authority is present; `parent`
presence is a necessary, never sufficient, condition. The admit path for children is owned by the
projector gate (§3a row 1) and claims no host witness here. Add a §4 mutant "sub-conversation
admitted on parent presence alone" alongside the existing "sub-conversation without parent
admitted" — the current single mutant only covers the *absent*-parent half.

### MEDIUM

**M1 — `T2CORE-BRIEF-v3.md:87-95` (§3.6) vs `:97-106` (§3a): §3a vacates the host witness for
ten of the 49 claimed T2 rows, and §3.6's class list does not name any of them.**
§3.6 says "implement the host-compile portion of the T2 rows" and gives four *e.g.* examples of the
witness-only class (`dynamic-no-incoming-grant`, `t03-publish-grant-revoked`,
`dynamic-registration-replaced-during-admission`, `dynamic-unknown-native-root`). Counting the
parent-bearing rows at `9d022502:docs/design/f-host-t2-brief.md:172-228`, ten further rows lose
their host-compile portion under §3a once parent read-scope/agreement go to the projector and
`ref`/`current`/epoch go to T4:
projector-owned — `t02-unreadable-parent`, `t02-parent-outside-read`, `t03-parent-access-revoked`,
`dynamic-claimed-parent-widening`, `dynamic-claimed-parent-overrides-actual`,
`dynamic-parent-not-asserted`, `dynamic-parent-claim-native-disagreement`;
T4-owned — `dynamic-cross-connection-parent`, `dynamic-stale-parent-epoch`,
`dynamic-parent-removed-before-ingress`.
(`dynamic-dm-sub-conversation` and `dynamic-read-sub-conversations-denied` do keep a host witness —
`dmUserId` identity and the `subConversations` member respectively.)
*Failure scenario:* with the classification open, the implementer either claims host coverage for
these ten against the presence-only rule (H1's over-claim), or discovers mid-slice that §3a owns
them and renegotiates the row accounting the §7 estimate was built on. §3.6's own rule — "'49
independent controls' must not be claimed" — has no denominator to replace it with.
*Fix:* enumerate the three classes **per row** in §3.6 (or an appendix table) before
implementation, explicitly listing the ten above as witness-only with their owner gate, and state
the resulting per-class counts the report must publish.

**M2 — `T2CORE-BRIEF-v3.md:39-46` (§2): the brief never says where `compileDynamicSelectors`
lives, and the two readings differ by a §6 stop.**
The sentence "Production passes no reader, so it has nothing to call or decode and fails closed"
presupposes a production-resident function; "**no new `src/` module**" two lines above, plus
"never imported by `src/`" for the doubles, presupposes a test-resident one. §4's "root" typecheck
reads as the former.
Resolution from the repo: the **test-resident** reading is both correct and already precedented.
`test/stubs/f-host-4/v2/static-policy.ts:50-58` @ `aecd4a8c` is exactly this shape —
`compileStaticRecipientAdmission(policy, trusted)` builds `read`/`wake`/`reply` closures and calls
`compileRecipientAdmission` through a deep import (`:4`). Every symbol needed is already exported
(`recipient-admission.ts:24,39,86`; `recipient-scope.ts:7`), and the non-exported `Lane` alias
(`recipient-admission.ts:9`) is already worked around the same way static-policy does (`:12`
`AdmissionLane` from `f-host-3/v1/a-contract.js`, `:54` `new Set(policy.lanes)`).
*Failure scenario:* the src/ reading is taken; the new compiler lands in `src/` as a new module
(contradicting §2's own first clause) or inside `recipient-admission.ts` (= a `.3`/`.4` production
edit, §6 last clause ⇒ stop mid-slice).
*Fix:* state the residency: `test/stubs/f-host-t2/v1/dynamic-policy.ts`, mirroring
`f-host-4/v2/static-policy.ts:50`, zero `src/` change, no closed edit list required. Then delete or
re-word the "production passes no reader" sentence — production does not call this compiler at all,
which is a *stronger* fail-closed statement than the one the brief makes.

**M3 — `T2CORE-BRIEF-v3.md:53-54` (§2, last doubles bullet): the "byte-pinned drop" of AF
`registration-view.ts` has no stated location, and the in-repo reading cannot typecheck.**
The bullet sits inside "Test doubles … directory `test/stubs/f-host-t2/v1/`", so the natural
reading lands the byte copy in-repo; §4 then runs a "Pinned-drop byte check" and a typecheck of
that directory. `a0970645:src/mcpl/registration-view.ts` is **163 lines of implementation**
(classes, `withRegistrationScope` at `:135-150`) and imports `./inbound-admission.js`,
`./registration-ingress.js` and `./types.js` (`:2-4`) — none of which exist in the host tree. A
`tsc --strict` over `test/stubs/f-host-t2/v1` containing that file fails on three unresolved
imports before any assertion runs.
The established pattern is the opposite and works: the AF drop lives **outside** the repo
(`/srv/review-drop/af-a869bb3a-contract/`, `/srv/review-drop/af-b29edcf8-contract-v2/`, each with
`SHA256SUMS` + `PROVENANCE.txt`), and the in-repo artifact is a **hand-transcribed compile-only
surface** citing per-file SHA256s — `test/stubs/f-host-4/v2/b-contract.ts:1-11` and
`test/stubs/f-host-3/v1/a-contract.ts:1-7` @ `aecd4a8c`.
*Fix:* split the bullet. (1) New external drop `/srv/review-drop/af-a0970645-view/` with
`SHA256SUMS` + `PROVENANCE.txt`, `cmp`-verified against `git show`. (2) In-repo
`test/stubs/f-host-t2/v1/view-contract.ts` transcribing only `registration-view.ts:6-32`
(`RegistrationRef`, `ReadonlyData`, `VerifiedRegistrationScope`, `RegistrationView`,
`RegistrationScopeConfig`), citing the drop shas — this is the type the bidirectional and one-way
assertions run against. §4's "pinned-drop byte check" then means drop-vs-`git show`, not a repo
file.
Also unresolved in the same bullet: the one-way assertion target "`RegistrationView`" is ambiguous
between AF's `registration-view.ts:22-25` and the existing v0 alias
`9d022502:test/stubs/f-host-t2/v0/stubs.ts:9` (`Parameters<typeof bind>[1]`). Name which.

**M4 — `T2CORE-BRIEF-v3.md:118-121` (§4): the mutant set guards the new read∧reply conjunction in
one direction only, and omits the empty-block escape.**
Two gaps:
(i) **Conjunction degeneracy.** §4 includes "reply skips the read conjunction" but no mutant for
the converse — `reply` evaluating `read` and **dropping its own members**. T2-input `:136`
(`t2-selector/independence`) names exactly that as a mutant to reject ("Ignore selector or **copy
read into wake/reply**"), and v3 drops `t2-selector/independence` without naming it or an owner
gate anywhere. Intersection and copy are distinguishable, but as written the required behaviour
and the forbidden behaviour are guarded by a single one-sided mutant.
(ii) **Empty-but-present block.** §3.1 distinguishes level (i) "no dynamic block at all ⇒ feature
off: the existing .4 path" from level (iii) "member missing or list empty ⇒ deny". The residual
escape route is a *present but empty* block collapsing to level (i): if
`compileDynamicSelectors({})` yields `undefined` as the `CompiledRecipientAdmissionPolicy`, then
`recipient-admission.ts:104` `if (policy === undefined) return {}` installs **no** admission hook
at all — feature-off, i.e. no ceiling, not deny. §4's "empty list allows" mutant covers an empty
*list*, not an empty *block*.
*Fix:* add two named mutants — "reply drops its own members and mirrors read" and "present but
empty dynamic block compiles to feature-off (`policy === undefined`)" — and state in §3.1 that
only a syntactically absent block reaches level (i). Separately: name `t2-selector/independence`'s
owner, and say explicitly that `reply = reply ∧ read` is an intersection, not a copy, so the
independence control and the conjunction requirement do not read as contradictory.

**M5 — `T2CORE-BRIEF-v3.md:123-128` (§4 docs-contract): the expected result is not named, and an
in-flight change to the same file sits on an unrelated base.**
(i) Check-1 L5 asked for the expected outcome to be named; v3 says only "Expected result is
reported per checker sha". The known state is **FAILED**: `FHOST4-BRIEF-v4.3.md:28` records
"docs-contract FAILED (declared filename false positive, TASK-EC6WW)" and `:217` "expected still
FAILED on the declared filename FP". The mechanism is verifiable at the pin —
`check-scope-contract.mjs:233` @ `aecd4a8c` asserts
`!/\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/iu`, case-insensitive with no digit requirement, and
`:337` puts the checker's own source in the scanned set, where `:343` is the literal
`'../../stubs/f-host-3/run-mutants.mjs'` — `run-mutants` matches. v3 §4 then **requires adding a
new t2 runner** to that same list, so the FAIL persists and grows. Not naming it invites a
re-litigation, or worse a "gate passes" claim, at gate time.
(ii) `/srv/review-drop/TASK-EC6WW-FIX2.md:5-10` records local commit `8d211982` changing **only**
`test/fixtures/generic-recipient/check-scope-contract.mjs`, on base `698bb587`. Neither commit is
an ancestor of `aecd4a8c` and `aecd4a8c` is not an ancestor of `8d211982` (verified with
`git merge-base --is-ancestor`). That rewrite replaces the `:233` detector with
`mintedNodeIdentifier`/`compactRunIdentifier` and moves the surrounding lines, so v3's "around
:336-351" pin drifts and the two edits collide in the same region of the same file.
*Fix:* state the expected docs-contract result (FAILED on the declared-filename FP, reported as
such, never "all gates pass"), and state how the T2 list edit sequences against EC6WW `8d211982`
— either T2 edits the `aecd4a8c` checker and EC6WW rebases over it, or T2 waits. Note that under
`8d211982`'s new detector a `task-`/`conv-` + five-Crockford-character token anywhere in the new T2
sources is rejected (its own control `['shape collision: ordinary task word', ['task','based'],
true]`), which the human neutrality read should cover.

**M6 — `T2CORE-BRIEF-v3.md:153-157` (§7): the estimate's basis list omits work §4 adds, and the
projector slice is asserted "counted separately" without a budget.**
(i) §7 enumerates its basis as "selectors, three-level rule, conjunction, injected-reader doubles,
drop, typechecks, t2 runner, row classification". §4 additionally requires: landing `f-host-t2/v0`,
four typechecks each with a discriminating negative control, the `check-scope-contract.mjs` input-
list edit, a **human neutrality read** of all new sources, and a matched-base **full suite
base/head** (two 3000 s-bounded runs). None appear in the basis. Against ~39 rows that still carry
a host witness after M1, 8 h at the floor leaves single-digit minutes per row once that infra is
paid; check-1 judged the *larger* 10–16 h figure "plausible … tight".
(ii) §7's ":703 is likely **overcommitted** … with the projector and T4 work counted separately" is
right about T4 — `generic-recipient-host.md:705` @ `a316200c` budgets T4 at 10–16 h including
"epoch rotation". It is **not** established for the projector: T1 `:311-312` places the
validator/projector on the **host** opt-in binding, and no row in the `:700-708` table covers a
host projector (`:708` AF-scope 8–16 h is conditional on "T1 requires AF plumbing"). So the
projector slice §3a assigns is currently **unbudgeted**, not separately budgeted — which
strengthens the overcommit claim but misstates where the hours are.
*Fix:* extend §7's basis list to the §4 additions and re-state the floor, and change "counted
separately" to name the projector slice as **unbudgeted** against the `:700-708` table, needing its
own incremental estimate like the loader residual.

### LOW

**L1 — `:70-73` (§3.2):** the conjunction makes `read` run twice per ingress admit
(`recipient-admission.ts:199` then the conjoined `reply` at `:211`) and twice per reply authorize
(`resolve` `:135` then `authorize`'s re-run of `replyChannel` at `:148`). Require the compiled
predicates and the injected reader to be **pure** (no memoisation, counters or one-shot state) and
add a mutant "predicate memoises its first verdict" — otherwise the double-evaluation is a silent
behaviour difference the §3.5 stub-vs-stub comparison will not catch.

**L2 — `:76` (§3.3):** "`readable` must be true" is ambiguous between the injected **fact**
(`VerifiedRegistrationScope.readable`, `registration-view.ts:16`) and the seam op
`trusted.readable` (`recipient-admission.ts:41`). The compiled predicates never receive `trusted`,
so only the fact reading is implementable. Say so, and note that the seam already checks its own
`readable` at `:199`/`:126`, so the selector check is defence in depth, not the only barrier.

**L3 — `:113-114` (§4):** "Matched base = `aecd4a8c` … also the baseline for the §3.5 comparisons" is
inconsistent with §3.5, where `static-baseline` is now a stub-vs-stub equivalence at a single head
and `no-policy`/`automatic-replies` are same-head seam controls. A commit baseline applies to the
tsc/full-suite pair, not to the §3.5 controls. Re-word.

**L4 — `:115-117` (§4):** the typecheck list omits `test/stubs/f-host-2a/v0`, which
`9d022502:test/stubs/f-host-t2/v0/tsconfig.json` extends and whose `stubs.ts` v0 imports
(`v0/stubs.ts:7`). Harmless for `tsc` resolution, but if `f-host-t2/v0` is landed the 2a/v0 path
becomes a transitive input and should be named with the others.

**L5 — `:119` (§4):** the mutant "absent-selector falls back to static" presumes a static
fallback path exists in the dynamic compiler's source to mutate. If the dynamic compiler never
imports `static-policy.ts` (it should not), that mutation is not an expressible unique-site edit
under the existing runners' `split(needle).length - 1 === 1` discipline
(`f-host-4/run-mutants.mjs:40`). Re-word as "absent selector compiles to allow-all" and keep the
static-fallback case as a compile-time/typecheck negative instead.

**L6 — `:24` (§1, H .4 row):** v3 drops v2's report citations for the `.4` pin, leaving "e276024a
review APPROVE" with no report name. Verified here: `e276024a..aecd4a8c` is exactly one commit
("Harden recipient reply compiler controls after LOW review"), matching the row's claim. The
asymmetry with the fully-cited I7 row is the mirror image of check-1 M3; one sentence restores it
(the reports are `/srv/review-drop/TASK-4ZM52-REVIEW1.md`, `…-LOWS1.md`).

---

## Item 1 — check-1 disposition (H1–H4, M2–M8, L1–L6)

| Check-1 | v3 status | Citation |
| --- | --- | --- |
| H1 parent/`ref`/`current` unreachable | **closed** (allocation) | `v3:14-15` §0; `v3:97-106` §3a rows 1–2 name the projector slice (T1 `:311-313`, `project(...)`) and T4 (AF `registration-view.ts:135-150`, T1 `:332-334`), each with acceptance; §3.3 drops the parent-rule clause. Verified: `recipient-admission.ts:39-42` @ `aecd4a8c` exposes `lookup`/`readable` only — no `ref`, descriptor or `current`; AF `registration-view.ts:23-24` has them. **Residual:** the in-slice remnant is fail-open — H1 above. |
| H2 absent selector = "feature off" | **closed** | `v3:60-66` §3.1 three distinct levels, (ii) "deny that operation. Never fall back to the static sets (`wakeChannels`, `replyChannels`) or to 'all'", (iii) empty ⇒ deny. §6 bullet 2 (`v3:141-142`) now guards the *fallback*, not the wrong default. Matches T1 `:44-45` verbatim. **Residual:** empty-block escape — M4(ii). |
| H3 three `t2-off` controls infeasible | **closed** | `v3:82-86` §3.5 keeps only `automatic-replies` (`recipient-admission.ts:130`) and `no-policy` "at seam level only" (`:104`, mutant `f-host-3/run-mutants.mjs:23` — verified), labels `static-baseline` stub-vs-stub against `f-host-4/v2/static-policy.ts` and disclaims T2-input `:131-135` per `:141`; `v3:103` §3a row 3 defers `reconnect`, `unconfigured-peer` and the H config-output byte-equivalence to the H loader. |
| H4 fact accessor needs an invention | **closed** (Option A) | `v3:39-46` §2: no new `src/` module, `compileDynamicSelectors<F>(config, facts)`, reader test-only, explicit bar on decoding unverified objects / descriptor / ACK / brand minting / generation authority. `src/recipient-scope.ts:1-7` @ `aecd4a8c` stays literally true. **Residual:** residency unstated — M2. |
| M2 unsatisfiable bidirectional typecheck | **closed** | `v3:49-52`: `VerifiedScopeFacts` structurally exactly `VerifiedRegistrationScope` with the bidirectional assertion; `ref`/`current` in a separate record with a one-way assertion. Satisfiable against `registration-view.ts:13-21` (all extras already optional). |
| M3 I7 "reviewed" mislabel | **closed** | `v3:25` now reads "reviewed at `29b5ebf3`, APPROVE; `a0970645` is the LOW round beyond it, not re-reviewed; `registration-view.ts` byte-unchanged across that delta, `docs/registration-scope-view.md` +11/−4", plus the `47f3b4f2` BLOCKED lineage. Verified: `git diff --stat 29b5ebf3 a0970645` touches only the doc, +11/−4. |
| M4 docs-contract/neutrality escape | **closed** (scan reach) | `v3:123-128`: every new T2 file added to the input list at `check-scope-contract.mjs:336-351`, result "covers only that literal scan", human neutrality read required and **not** covered by the pass, design doc shas-only. Matches the checker's own self-exempting `.join()` construction (`:222-226,364-374`). **Residual:** expected result / EC6WW collision — M5. |
| M5 matched-base gates dropped | **closed** | `v3:113-114`: "Matched base = `aecd4a8c` for tsc and the full suite (fresh, or reused with a labelled bindings table)"; `v3:129` "Full suite base/head". **Residual:** wording — L3. |
| M6 "49 independent controls" over-claim | **partly closed** | `v3:87-95` §3.6 introduces independent / shared / witness-only classes, names the four-row `lookup → undefined` collapse, and forbids claiming 49. Counts re-verified at `9d022502:…t2-brief.md:172-228`: 57 rows, 38 `+T4` + 6 `+T3/T4` + 5 `+T5` = 49, 8 native-only. **Not closed:** the ten rows §3a vacates — M1. |
| M7 evaluation-time conjunction | **closed** | `v3:70-73` §3.2 requires the compiled `reply` to evaluate the full `read` predicate, citing `recipient-admission.ts:113-129`. Verified: `replyChannel` never calls `read`; on ingress the intersection is structural at `:199-200`. **Residual:** one-sided mutant — M4(i). |
| M8 estimate vs `:703` | **partly closed** | `v3:153-157` §7 states what it charges, that the residual needs its own incremental estimate, and that `:703` is likely overcommitted. `:703` = 12–20 h verified at `a316200c:docs/design/generic-recipient-host.md:703`. **Not closed:** basis list and projector budget — M6. |
| L1 addressedness / listed-not-joined | **closed** | `v3:74-75` states both, citing T1 `:54` and `:89` (both verified). |
| L2 `v0` ambiguity | **closed** | `v3:47-48` allows landing `9d022502:test/stubs/f-host-t2/v0/*` as `v0`; `v3:115-116` names full paths. Verified: the three v0 files exist at `9d022502` and v0 is 9 lines depending only on `f-host-2a/v0`, which exists at `aecd4a8c`. **Residual:** L4. |
| L3 A/B repo unnamed | **closed** | `v3:26` "AF `869bb3ad…` / AF `29edcf8b…` (both ancestors of `a0970645`)" — ancestry verified. |
| L4 label over-applied to `+T5` | **closed** | `v3:94` "`oracle-of-mapping-X` for the 5 `+T5` rows", matching T1 `:386-393`. |
| L5 docs-contract expected outcome | **not closed** | `v3:124` says only "Expected result is reported per checker sha" — M5(i). |
| L6 module-private wording | **closed** (moot) | Option A removes the `src/` module, so the packaging wording is gone with it. |

## Item 2 — Soma's rules

**(a) Two separate controls, no escape route — SATISFIED with one gap.** `v3:60-66` pins (i) no
block ⇒ feature off (existing `.4` path, `recipient-admission.ts:104`), (ii) absent selector inside
a present block ⇒ deny that operation, (iii) absent member / empty list ⇒ deny, as three distinct
controls; `v3:141-142` §6 forbids the fallback rather than encoding it. The one remaining escape is
a present-but-empty block collapsing to (i) — M4(ii).

**(b) Barriers kept, missing facts never affirmative — PARTLY SATISFIED.** Kept: §3a records both
moved requirements with acceptance criteria that are stronger than v2's text ("claimed parent never
overrides actual", "equal visible ids in other K/R/epoch never authorize"), and `v3:106` routes any
in-slice need to a §6 stop. Not satisfied: §3.3's presence-only child rule turns an unverifiable
parent fact into affirmative scope — **H1**. Descriptor/ACK-not-authority *is* carried correctly
(`v3:79`, T1 `:94-138`, matching `:133-135` "A structurally valid descriptor with a register
ACK is **not** a scope authorization").

**(c) Reader not presented as issuance/constructor/loader proof; the three loader controls pending
at their gate — SATISFIED.** `v3:44-46`: "It is **not** production issuance, **not** a brand
constructor and **not** loader proof", with an explicit bar on decoding, descriptor/ACK trust,
brand minting and synthesised generation authority; `v3:104` §3a row 4 assigns production issuance
to I6 with "constructor/verifier reviewed; no decoding of unverified objects". The three loader
controls (`t2-off/reconnect`, `t2-off/unconfigured-peer`, and the H config-output byte-equivalence
of `no-policy`/`static-baseline`) are all three named pending at the H loader/overlay gate with
acceptance "as T2-input `:131-135`, against the real loader" (`v3:103`).

## Item 3 — implementability of the injected reader, and the conjunction

**(3a) The injected reader `<F>` is implementable against `aecd4a8c` with zero `.3`/`.4`
production edits. No closed edit list is needed.** Everything the dynamic compiler consumes is
already exported: `compileRecipientAdmission` (`recipient-admission.ts:86`),
`CompiledRecipientAdmissionPolicy` (`:24`), `TrustedRecipientRead` (`:39`), `VerifiedScope`
(`recipient-scope.ts:7`). The exact shape already exists as precedent:
`test/stubs/f-host-4/v2/static-policy.ts:50-58` deep-imports `src/recipient-admission.js` (`:4`),
builds the three predicates as closures and hands them to `compileRecipientAdmission`. The
non-exported `Lane` alias (`recipient-admission.ts:9`) is already handled the same way static-policy
handles it (`:12` `AdmissionLane` from `f-host-3/v1/a-contract.js`, `:54` `new Set(policy.lanes)`),
so it forces no export either. The reader is a parameter of the new test-side compiler and never
touches `src/`. **Condition:** M2's residency must be stated, because the `src/`-resident reading of
`v3:42-43` *does* require either a new `src/` module (contradicting §2) or an edit to
`recipient-admission.ts` (§6 stop).

**(3b) The evaluation-time `read ∧ reply` conjunction needs no widening of
`TrustedRecipientRead`.** `read`, `wake` and `reply` all take the identical tuple
`(key: Key, channelId: string, scope: VerifiedScope, input: Input)`
(`recipient-admission.ts:28-30`), so the compiled `reply` closure can invoke the compiled `read`
closure directly over its own arguments; both close over the same injected reader. The seam
(`:39-42`) is untouched, and `replyChannel` continues to call `reply` with exactly those arguments
(`:127`), as does `admit` (`:211`). Two notes: the conjunction is only *needed* for `reply` at this
base — `wake` is never reachable without a prior `read` (`:199-200` denies on `read` first, and the
channel-less path at `:206` has no scope), so `v3:72`'s forward-looking "same applies to `wake`"
correctly has no witness here; and the conjunction makes `read` run twice per admit and twice per
authorize, hence the purity requirement in L1.

## Item 4 — contradictions and fail-open paths introduced by v3; §4 mutant discrimination

New in v3: **H1** (presence-only child admit), **M1** (ten rows vacated without reclassification),
**M2** (compiler residency), **M3** (AF drop residency vs typecheck), **M4(ii)** (empty-block
escape), **M5(ii)** (EC6WW collision on the file §4 now requires editing), **M6(ii)** (unbudgeted
projector), **L3** (baseline wording), **L5** (unexpressible mutant). v3 also silently drops
T2-input `:136`'s `t2-selector/independence` without an owner — M4(i).

**§4 mutants, discrimination assessment.** All six named mutants are expressible as unique-site
string edits over the new test-side compiler, which is the discipline both existing runners use
(`f-host-3/run-mutants.mjs`, `f-host-4/run-mutants.mjs:37-40`; the f-host-4 runner already mutates
a test file, `b-model.ts`, at `:8`), so the mechanism is sound. Per mutant: "empty list allows",
"reply skips the read conjunction", "DM matches by channel id", "unknown fact allows" —
discriminating. "Sub-conversation without parent admitted" — discriminating for the *absent*-parent
half only; under H1 its sibling (present-but-unauthorised parent) is specified as admit, so the
row's own stated mutants ("ignore actual parent scope") remain unkillable. "Absent-selector falls
back to static" — not expressible as written (L5). Missing from the floor: the reply→copy
degeneracy and the empty-block escape (M4), the memoisation case (L1), and the presence-only child
admit (H1).

## Item 5 — the 8–12 h estimate and the `:703` statement

`:703` verified at 12–20 h for "dynamic scope compile/**bind** coverage **and** feature-off
controls" (`a316200c:docs/design/generic-recipient-host.md:703`). The **overcommit statement is
sound in direction**: 8–12 h of a 12–20 h row leaves 0–12 h (realistically ~4–8 h at the midpoints)
for the loader/overlay boundary, callback installation, the real bind the row names, and the three
loader feature-off controls — and the statement is correctly labelled an unreviewed operator
estimate. T4 is genuinely budgeted elsewhere (`:705`, 10–16 h, includes epoch rotation). Two
defects: the 8 h floor is not credible against §4's unlisted additions, and the projector slice is
unbudgeted rather than "counted separately" (both M6).

## Verification Gates

**None run — by brief constraint** ("No builds/edits/network; git show/grep/sha256sum only"). No
gate receipt is produced or reused by this check. Commands actually run: `sha256sum` on both
briefs; `git log --oneline -1` on all ten pins; `git show <sha>:<path>` on
`src/recipient-admission.ts`, `src/recipient-scope.ts`, `src/recipient-policy.ts`,
`test/stubs/f-host-4/v2/{static-policy,b-contract,tsconfig}`, `test/stubs/f-host-3/v1/a-contract.ts`,
`test/stubs/f-host-3/run-mutants.mjs`, `test/stubs/f-host-4/run-mutants.mjs`,
`test/fixtures/generic-recipient/check-scope-contract.mjs`,
`docs/design/generic-recipient-scope-contract.md`, `docs/design/generic-recipient-host.md`,
`docs/design/f-host-t2-brief.md`, `test/stubs/f-host-t2/v0/*`, AF `src/mcpl/registration-view.ts`;
`git ls-tree -r` on `src`/`test/stubs` at `aecd4a8c` and `test/stubs/f-host-t2` at `9d022502`;
`git diff --stat 29b5ebf3 a0970645`; `git diff 698bb587 8d211982`; `git merge-base --is-ancestor`
on `aecd4a8c`/`8d211982`/`698bb587`; `git log --oneline e276024a..aecd4a8c`;
`diff -u T2CORE-BRIEF-v2.md T2CORE-BRIEF-v3.md`.

## Unmet criteria / test gaps

- No row-level classification exists for the 49 T2 rows, so the slice has no agreed deliverable
  count and no agreed denominator for the report (M1).
- Ten parent-bearing rows would, as specified, be witnessed by a rule that cannot fail (H1).
- `t2-selector/independence` (T2-input `:136`) has no owner and no mutant anywhere in v3 (M4(i)).
- The empty-but-present-block path has no control and no mutant (M4(ii)).
- Predicate purity under double evaluation is untested (L1).
- `docs-contract` has no declared expected outcome, so a FAIL at gate time is unclassifiable
  without re-deriving the FP (M5(i)).
- Not assessable read-only and out of this check's scope: whether the `aecd4a8c` suite is green,
  and whether `tsc` over the proposed `f-host-t2/v1` directory succeeds. Both are gate-time facts.

## Residual risk

1. **Over-claim risk (highest).** The combination of H1 and M1 produces a plausible-looking round
   that reports host-compile coverage for parent/lineage rows while no host code decides them. This
   is the "test that passes without exercising the acceptance criterion" pattern; it survives review
   unless the row classification is fixed *before* implementation.
2. **Mid-slice stop risk.** M2 and M3 each have one reading that terminates in a §6 stop (a `.3`/`.4`
   production edit; an untypecheckable in-repo AF drop). Both are one-sentence fixes now and a lost
   round later.
3. **Cross-task collision.** EC6WW `8d211982` and this slice both edit
   `check-scope-contract.mjs` in the `:222-240` / `:336-351` regions from non-overlapping bases
   (M5(ii)).
4. **Estimate.** The 8 h floor plus the unbudgeted projector slice means the `:703` residual is
   likely worse than §7 states — in the direction §7 already warns about, so this is a planning
   risk, not a correctness one.
5. **Out of scope and unchanged:** `.4` activation blocker (owner items 6/7), I6/I7 qualification,
   AF currentness, publication (#931). v3 `:133-135` §5 records all of these correctly.

## Fix directions (minimal, ordered)

1. **§3.3** — child scopes **deny** in compiler-core; `parent` presence necessary, never sufficient;
   admit path owned by the projector gate. Add the presence-only mutant. *(H1)*
2. **§3.6** — per-row classification table; list the ten projector/T4-owned parent rows as
   witness-only with their owner gate; publish per-class counts. *(M1)*
3. **§2** — state that `compileDynamicSelectors` lives in `test/stubs/f-host-t2/v1/`, mirroring
   `f-host-4/v2/static-policy.ts:50`, with zero `src/` change; drop the "production passes no
   reader" framing in favour of "production does not call this compiler". *(M2)*
4. **§2** — split the AF drop: external `/srv/review-drop/af-a0970645-view/` with SUMS +
   PROVENANCE, plus an in-repo transcribed `view-contract.ts` covering `registration-view.ts:6-32`
   only; name which `RegistrationView` the one-way assertion targets. *(M3)*
5. **§3.1 / §4** — only a syntactically absent block reaches level (i); add the mutants "reply drops
   its own members and mirrors read" and "present but empty block compiles to feature-off"; name
   `t2-selector/independence`'s owner and say intersection ≠ copy. *(M4)*
6. **§4** — name the expected docs-contract result (FAILED, declared-filename FP, never "all gates
   pass") and sequence the input-list edit against EC6WW `8d211982`. *(M5)*
7. **§7** — add the §4 additions to the basis list and revisit the floor; call the projector slice
   unbudgeted against `:700-708`. *(M6)*
8. **LOWs** — L1 purity + mutant, L2 `readable` fact vs seam op, L3 baseline wording, L4 add
   `f-host-2a/v0`, L5 re-word the static-fallback mutant, L6 restore the `.4` report citations.
