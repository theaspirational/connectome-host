# CONV-FMD5N — bounded DELTA check of T2CORE-BRIEF v4.1 → v4.2 (read-only)

Operator Tarn, qualifier Soma. Not an owner decision. Scope: E1–E8 closure, the TS behaviour of the
new exactness control, and new HIGH / fail-open. No repo mutation; no `src/`, worktree or ledger
write. Probe artifacts in `$ORGASMIC_EVIDENCE_DIR/probe/`.

## Pins verified (sha256sum)

| Artifact | Expected | Result |
| --- | --- | --- |
| `…/tarn-rtaln-20261030/T2CORE-BRIEF-v4.2.md` | `e24f6fb3…d44175` | **match** |
| `…/tarn-rtaln-20261030/T2CORE-BRIEF-v4.1.md` | `3345ace2…` | **match** (`3345ace20e37faf60382ca1455ec28bed50d0504dab30bd568bcf21d79608a2f`) |
| `/srv/review-drop/T2CORE-BRIEF-RECHECK3.md` | `50f9d7f9…` | **match** |
| `/srv/review-drop/af-a0970645-view/SHA256SUMS` | `27ecd2d9…938b3796` | **match** |
| drop `sha256sum -c SHA256SUMS` | both files | **OK** |

## Verdict

**READY WITH CHANGES** — no HIGH, no fail-open. E1 (HIGH), E2 and E3 (MEDIUM) and E4–E8 (LOW) are
all closed in substance, and the one technical premise the operator flagged for probing (**key-set
equality catches an optional widening**) is **confirmed true in TS**. One MEDIUM remains: §4's gate
enumeration was not updated to the two new §2 controls, so the only assertion that can detect the
widening has no named discriminating negative. Under §8:268 ("no open HIGH") this does **not** block
start; F1 is a one-clause edit to §4:205-206 and should land first.

## Findings

**F1 MEDIUM (test / gate coverage) — `T2CORE-BRIEF-v4.2.md:205-206` vs `:57` and `:69`: the two new
§2 typecheck controls are absent from §4's negative-control list, so the only control that detects an
optional widening can be written vacuously and no gate would notice.**
v4.2 adds two typecheck controls in §2: `:69` "Optional members are caught by a **key-set equality**
assertion on the type, not by assignability alone", and `:57` "the type has no boolean member, and **a
typecheck negative** pins that". §4's typecheck bullet at `:204-206` still enumerates exactly the
v4.1 pair: "each with negative controls (**the bidirectional facts assertion and the one-way view
assertion** each get a discriminating negative)". Neither new control is named.
This matters because of the asymmetry my probe reproduces (below): the bidirectional facts assertion
**cannot** see an optional widening (`probe/B_opt_assign.ts` → **exit 0**), while the key-set
assertion can (`probe/A_opt_keyseq.ts` → **exit 1**, `TS2344`). So §4 currently demands a
discriminating negative for the control that is blind to the widening, and demands none for the
control that is not. A key-set assertion written vacuously — `KeysEq<VerifiedScopeFacts,
VerifiedScopeFacts>`, or a formulation that always resolves `true` — compiles green and retires
`:52`'s "byte-exact to the AF shape" silently. That is precisely the carrier-drift residual
check 3 raised one level down; the fix moved the control into the brief but not into the gate list.
*Fix:* at `:205-206`, extend the parenthetical to "the bidirectional facts assertion, **the key-set
equality assertion**, the one-way view assertion **and `ParentAuthorityFact`'s no-boolean-member
negative** each get a discriminating negative". For the key-set negative, the brief can name the
shape outright: an added **optional** member must fail the assertion (not merely an added required
one, which the bidirectional assertion already catches).

**F2 LOW (docs / fixture clarity) — `:131` (row C3): "or a bare boolean" is unreachable in a
typechecked double, so C3's fixture is ambiguous.**
C3's third disqualifier is "…or a bare boolean". But `:56-57` and the type make a bare boolean
impossible: probe `G_bool.ts` → `TS2322: Type 'boolean' is not assignable to type
'ParentAuthorityFact'` (**exit 1**) for the uncast case. A fixture exercising that C3 branch can only
be written with an `as any` escape, which is itself the thing `:57` is trying to forbid. The row's
other two disqualifiers (wrong scope, `parent` mismatch) are the real, reachable ones.
*Fix:* at `:131`, move "a bare boolean" out of the C3 verdict row into a parenthetical noting it is
**excluded by the type** (`:57`), not a runtime deny path — so no one writes a cast-based fixture.

**F3 LOW (test) — `:131` (C3) has no named fixture, unlike C1/C6, while `:211` adds the mutant
"parent-authority scope binding skipped (C3)".**
E3's fix correctly pinned fixtures for the two kept witnesses (`:162-165`) and, with the wrong-
`dmUserId` negative, for the new C5 mutant at `:212`. The new C3 mutant at `:211` has no stated
fixture; C3's witness column says only "scope-binding control". Killing it needs a child op where
the fact is mis-bound (fact for another scope, or `parent !== facts(scope).parent`) and every other
conjunct holds — row C3 exactly, but the brief never says a double must supply one. §4:207-208's
runner discipline ("named kills, zero selection rejected") makes this a mid-slice stop rather than a
shipped fail-open, which is why it is LOW and not MEDIUM.
*Fix:* at `:162-165`, add a third kept fixture: a DM child with `subConversations: true` and a fact
whose `parent` is a **different** `RegistrationRef` object than `facts(scope).parent`, asserted deny.

**F4 LOW (docs) — `:54` writes `facts(scope).parent` with no guard for `facts(scope) === undefined`,
and the C1–C6 table has no row for absent scope facts.**
`facts` is typed `(scope: VerifiedScope) => VerifiedScopeFacts | undefined` (`:50`), so under
`--strict` the literal expression `facts(scope).parent` at `:54` does not compile. No fail-open:
`:125-126` ("Missing, unknown or unverifiable facts deny and can never become affirmative scope")
precedes the table and covers the case, and the table's `parent` column implicitly assumes facts
exist. Prose shorthand only.
*Fix:* write it as "`facts(scope)`'s `parent` (facts absent ⇒ deny per `:125`)".

## Checked and closed

- **E1 HIGH — closed, option (a), and the premise probes out true.** `:49-51` now takes **two**
  injected readers, `facts` and `parentAuthority`, with `VerifiedScopeFacts` "byte-exact to the AF
  shape" (`:52`) and `:65-66`'s bidirectional assertion intact. `ParentAuthorityFact` is a separate
  test-only type `{ readonly scope: VerifiedScope; readonly parent: RegistrationRef }` (`:53-54`),
  resident in `test/stubs/f-host-t2/v1/` and **not** added to `VerifiedScopeFacts` (`:67-68`).
  Binding is by `===` on the scope the reader was called with **and** on the facts' `parent`
  (`:54-56`); never derived from `parent` (`:56`); uses no `ref`/`current` (`:57-58`), consistent
  with the §6 stop at `:244`. Both referenced types resolve from the transcribed range: `RegistrationRef`
  at `af-a0970645-view/src/mcpl/registration-view.ts:7`, inside `:6-25`; `VerifiedScope` is the
  existing H .3 brand already used by `facts`. The stale v4.1 single-reader echo is gone
  (`grep -n "generic over" → no hits`).
  - **"VerifiedScopeFacts exact (key-set equality catches an optional widening — does that actually
    work in TS?)" — YES, confirmed.** `probe/A_opt_keyseq.ts`, tuple-wrapped
    `[keyof A] extends [keyof B] ? [keyof B] extends [keyof A] …` against the pinned shape plus
    `readonly parentAuthority?: RegistrationRef` → **exit 1**, `TS2344: Type 'false' does not satisfy
    the constraint 'true'`. The naive undistributed form also catches it (`C_opt_naive.ts` → exit 1),
    and the byte-exact shape passes both controls with no false positive (`D_exact_pass.ts` → exit 0).
    Contrast `B_opt_assign.ts` (bidirectional assignability only) → **exit 0**, reproducing check 3's
    E1 second bullet. An added index signature is also caught (`F_index_keyseq.ts` → exit 1).
  - **The two controls are jointly complete for the realistic widenings.** Key-set equality alone
    misses a required→optional **flip** of an existing member (same key set):
    `E_flip_keyseq.ts` (`readable?: boolean`) → **exit 0**. The bidirectional assignability assertion
    catches exactly that: `E2_flip_assign.ts` → **exit 1**. So `:65-66` + `:69` together close both
    directions — keeping **both** is load-bearing, which is the reason F1 matters.
  - **"A bare boolean is impossible by type" — holds.** `probe/G_bool.ts`: an uncast
    `(_s) => true` against `(s: VerifiedScope) => ParentAuthorityFact | undefined` → **exit 1**,
    `TS2322: Type 'boolean' is not assignable to type 'ParentAuthorityFact'`. Only an explicit
    `as any` escapes, which is review/lint territory, not a brief defect. See F2.
- **E2 MEDIUM — closed, and the table is a superset of Soma's four rows.** `:126-137`. Mapping:
  Soma (1) `subConversations` disabled/missing ⇒ deny → **C1** (`:130`, covering
  "false / missing / selector empty"); Soma (2) parent present, no authority fact ⇒ deny → **C2**
  (`:131`); Soma (3) fact absent or unverifiable/undefined ⇒ deny → **C2** (undefined) + **C3**
  (invalid/mis-bound); Soma (4) controlled `stub-of-projector` affirmative ⇒ admit, test-only →
  **C6** (`:135`). v4.2 adds **C4** (parent absent) and **C5** (a fact conjunct fails), both deny.
  All four rows present; nothing dropped. **C6 = admit only with all conjuncts, no early admit:**
  stated verbatim at `:137-138` — "C6 admits **only if** both child conjuncts **and** every fact
  conjunct for that operation hold. There is no early admit on the child conjuncts. C6 is not a
  completeness claim." The early-admit reading check 3 called a real fail-open is now forbidden in
  text **and** carries a mutant at `:212`. C4 is additionally enforced by type: `ParentAuthorityFact.parent`
  is required, so `undefined === requiredRef` can never bind.
- **E3 MEDIUM — closed, both mutants individually killable.** `:161-165` pins per-row fixtures:
  `dynamic-dm-sub-conversation` = `subConversations: true` + valid bound fact + matching `dmUserId`
  (C6), **plus its negative with a wrong `dmUserId`** (C5); `dynamic-read-sub-conversations-denied` =
  `subConversations: false` **with a valid bound fact present** (C1), "so the deny comes from the
  `subConversations` conjunct alone". That is exactly the fix E3 asked for: with the fact present on
  the read row, deleting the `subConversations` check (`:210`) flips the verdict, so the mutant is
  killable rather than surviving by construction. The added wrong-`dmUserId` negative makes the new
  `:212` early-admit mutant killable too.
- **E4 LOW — closed.** `:3-12` replaces the garbled "v4 was v4 folds" sentence with an explicit
  four-entry provenance chain, each entry carrying its report sha256 (v4.2/`50f9d7f9…`,
  v4.1/`3345ace2…`, v4/`70beae75…`, v3/`fce4db08…`). All four shas re-verified above or in check 3.
- **E5 LOW — closed.** `:13` now reads "A bounded delta check of **v4.2** precedes implementation
  (§8)", agreeing with `:268`.
- **E6 LOW — closed.** `:32` now reads "`registration-view.ts:6–32` signatures, **of which `:6–25`
  is transcribed**", reconciling §1 with §2 `:78`.
- **E7 LOW — closed.** `:81` now reads "`ConnectionKey` (**needed only by `dynamic-policy.ts`, not by
  `view-contract.ts`**; E7)".
- **E8 LOW — closed.** `:159-160` now reads the negative witness as "child denied **for want of a
  parent-authority fact**", replacing "denied by default".

## New HIGH / fail-open sweep

None found. On the operator's specific question — **can the `===` scope binding be faked by a stub
reader that returns `{scope, parent}` for any scope, and does that matter at the test-only level?**
Yes it can be faked, and **no, it does not matter here.** `probe/H_anyscope.ts`: a promiscuous reader
`(s) => ({ scope: s, parent: someParent })` typechecks clean (**exit 0**), so nothing at the type
level stops a double asserting authority for every scope it is handed. But the binding's job in this
slice is not unforgeability — it is to force the compiled predicate to *perform* a check that a
fixture can falsify, which is what makes the `:211` mutant killable. Unforgeability belongs to the
projector slice (`:175`, §3b), and the residual trust in the stub is disclosed, not hidden:
`:121-124` ("exists **only** in test doubles, labelled `stub-of-projector`"; "Compiler-core computes
no parent authority from in-slice facts"), `:137` ("C6 is not a completeness claim"), `:234`
(§5 Not claimed keeps "projector/parent evaluation"), `:268-269` (test/stub-only, no `src/` change,
no runtime issuance). The same disclosure covers a double asserting a bogus claimed parent
(`dynamic-claimed-parent-overrides-actual`), which `:155-156` correctly leaves in the projector list.
The only gap this opens is F3: the brief does not *require* a mis-bound fixture, so the check could
be written and never exercised.

## Verification Gates

Read-only brief delta; no build gate was assigned and none applies to a brief. No
`orgasmic dispatch gate` receipt is claimed.
- `sha256sum` on v4.2, v4.1, RECHECK3 and the drop's `SHA256SUMS`; `sha256sum -c SHA256SUMS` in
  `/srv/review-drop/af-a0970645-view/` → both files **OK**. (Table above.)
- `diff -u T2CORE-BRIEF-v4.1.md T2CORE-BRIEF-v4.2.md` — full delta read; the diff is confined to the
  status block, `:32`, §2, §3.3, §3.6, §4's mutant list and §8, i.e. exactly the E1–E8 fix sites.
- `grep -n` over v4.2 for `parentAuthority|ParentAuthorityFact|key-set|bidirectional|byte-exact|
  ConnectionKey|6–32|6-25|denied for want|truth table|only if|stub-of-projector|generic over|
  discriminating negative` — the evidence for each closure above and for F1.
- `sed -n` reads of `:1-16`, `:44-72`, `:110-140`, `:155-170`, `:195-215`, `:232-234`, `:244`.
- Pinned AF shape read from the drop: `af-a0970645-view/src/mcpl/registration-view.ts:1-40`
  (`RegistrationRef` `:7` is an object brand, so `===` on `parent` is reference identity — the
  binding is implementable without `ref`/`current`; `parent?` optional at `:19`; no
  parent-authority member, confirming `ParentAuthorityFact` had to be separate).
- **Out-of-list probe, disclosed (as in check 3):** eight scratch TypeScript files in
  `$ORGASMIC_EVIDENCE_DIR/probe/`, compiled with
  `tsc --strict --noEmit --target es2022 --module nodenext --moduleResolution nodenext` from a
  typescript install outside the pinned repos (`/tmp/afd-base-22.23.3/node_modules/typescript`).
  Touched no pinned repo, clone or worktree; `base.ts` transcribes only
  `registration-view.ts:6-21` from the verified drop. Results:
  `A_opt_keyseq` exit 1 · `B_opt_assign` exit 0 · `C_opt_naive` exit 1 · `D_exact_pass` exit 0 ·
  `E_flip_keyseq` exit 0 · `E2_flip_assign` exit 1 · `F_index_keyseq` exit 1 · `G_bool` exit 1 ·
  `H_anyscope` exit 0.

## Unmet criteria / test gaps

1. **F1** — the key-set equality assertion and `ParentAuthorityFact`'s no-boolean negative are
   declared in §2 but not enumerated in §4's negative-control list, so neither is required to be
   discriminating.
2. **F3** — the `:211` scope-binding mutant has no pinned killing fixture (the runner discipline at
   `:207-208` converts this to a mid-slice stop, not a shipped gap).
3. Unchanged, correctly disclosed, not re-charged: no production issuance or binding (I6), no real
   projector or verifier, no native verification (T3), no lifecycle barriers (T4), no storage parity
   (T5), no H loader; `:218-223` expects the docs-contract pass to report **FAILED** and must not be
   reported as "all gates pass"; the all-49 per-row table (`:166-167`) and the "49 independent
   controls must not be claimed" rule (`:169`) remain the anti-overclaim controls.
4. Not re-examined this round (outside the delta): the `aecd4a8c` matched-base requirement, the
   one-LOW-commit-beyond caveat on the .4 pin (`:31`), and the §3b projector-slice sufficiency and
   DAG single-count that check 3 confirmed.

## Residual Risk

- **Vacuous exactness control (F1).** The widening-detecting assertion is now in the brief but not in
  the gate list. If it is written vacuously the typecheck stays green and `:52`'s byte-exactness is
  retired with no receipt — the same carrier drift check 3 named, relocated from the type to the gate.
  Both `:65-66` and `:69` are load-bearing in opposite directions (probe B vs E/E2); dropping either
  reopens a hole.
- **Stub-asserted authority (disclosed, not a defect).** Probe `H_anyscope` shows a double can assert
  authority for any scope. Labelling at `:121-124`, `:137`, `:175`, `:234` is explicit and the slice
  is test-only, so the exposure is to *reporting* — the §3.6 counts and the worker's report are where
  scaffolding could leak as coverage, mitigated only by `:166-169` and worker discipline.
- **Unexercised binding check (F3).** Until a mis-bound fixture is pinned, the `===` binding could be
  implemented and never falsified by any test.
- **Planning.** `:262-263`'s disclosure stands unchanged: 11–17 h compiler-core plus 8–14 h projector
  against a 12–20 h `:703` row means `:703` cannot hold the whole of T2. Operator estimate, unreviewed.

## Fix Directions (minimal, ordered)

1. **F1** — at `:205-206`, add the key-set equality assertion and `ParentAuthorityFact`'s
   no-boolean-member negative to the discriminating-negative list, and specify that the key-set
   negative must fail on an added **optional** member (an added required member only exercises the
   bidirectional assertion). One clause.
2. **F3** — at `:162-165`, pin a third kept fixture: a mis-bound fact (different `parent` object, or
   a fact issued for another scope) with all other conjuncts holding, asserted deny, as the named
   kill for `:211`.
3. **F2** — at `:131`, demote "a bare boolean" from a C3 verdict path to a parenthetical noting the
   type excludes it (`:57`), so no cast-based fixture is written.
4. **F4** — at `:54`, reword `facts(scope).parent` to be undefined-safe in prose, pointing at `:125`.
5. Then start per `:267-271`: the drop is verified (re-confirmed above), this delta check closes with
   no open HIGH, and Soma's go remains the last precondition.
