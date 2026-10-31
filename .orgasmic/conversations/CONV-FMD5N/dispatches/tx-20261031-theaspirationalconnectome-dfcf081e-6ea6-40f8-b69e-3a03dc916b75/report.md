# CONV-FMD5N — bounded DELTA check of T2CORE-BRIEF v4 → v4.1 (read-only)

Operator Tarn, qualifier Soma. Not an owner decision. Scope: D1–D6 closure, §3b sufficiency,
new HIGHs, plus Soma's relayed child-truth-table addition (msg-d3730e99). No repo mutation; no `src/`, worktree or ledger write.

## Pins verified (sha256sum / cmp / git show)

| Artifact | Expected | Result |
| --- | --- | --- |
| `…/tarn-rtaln-20261030/T2CORE-BRIEF-v4.1.md` | `3345ace2…608a2f` | **match** |
| `…/tarn-rtaln-20261030/T2CORE-BRIEF-v4.md` | `70beae75…` | **match** (`70beae75c78feba8c0f756d8dc9a6729094458e8a01f0ad3bd7caaaf3ff940f5`) |
| `/srv/review-drop/T2CORE-BRIEF-RECHECK2.md` | `2f343c0a…` | **match** (`2f343c0ade5667b6f9632d34693020ed251a6d82d1abcdb85adac374acc705ef`) |
| `/srv/review-drop/af-a0970645-view/SHA256SUMS` | `27ecd2d9…938b3796` | **match** |
| drop `sha256sum -c SHA256SUMS` | both files | **OK** (`src/mcpl/registration-view.ts`, `docs/registration-scope-view.md`) |
| drop vs `git show a0970645:<path>` (AF) | byte-identical | **cmp OK**, both files |
| PROVENANCE claim "`registration-view.ts` byte-unchanged across `29b5ebf3..a0970645`" | — | **holds** (`git diff --stat 29b5ebf3 a0970645 -- src/mcpl/registration-view.ts` → empty) |

D5 is therefore closed in substance as well as in wording: the drop now exists, verifies, and
§8:233 orders it as the first precondition.

## Verdict

**READY WITH CHANGES** — one HIGH (E1) and two MEDIUMs (E2 from Soma's relayed addition, E3). D2–D6 are closed. D1 is closed *in rule* but its required
second conjunct has **no declared carrier**, and the one place the brief could put it is foreclosed
by §2:54. Because §8:234 itself makes "no open HIGH" a start precondition, implementation should not
start until E1 is closed; E1 is a one-paragraph edit, not a redesign.

## Findings

**E1 HIGH (correctness / unmet acceptance) — `T2CORE-BRIEF-v4.1.md:102-105` vs `:45-46` and `:54-55`:
the projector-supplied parent-authority fact has no carrier, and the only named fact type is
explicitly forbidden from carrying it.**
`:102-104` now requires, for `kind === 'sub-conversation'`, conjunct (b) "an injected
**projector-supplied parent-authority fact** … present for this scope". The slice has exactly one
fact channel: `:45-46` `compileDynamicSelectors<F>(config, facts: (scope: VerifiedScope) => F | undefined)`
— a single reader, "generic over the fact type" (singular, echoed at `:159`). The only `F` the brief
names is `:54-55` `VerifiedScopeFacts` = "**structurally exactly** the pinned
`VerifiedRegistrationScope`, with a **bidirectional** assignability assertion against the pinned drop".
The pinned shape has **no** parent-authority member: `af-a0970645-view/src/mcpl/registration-view.ts:13-21`
carries only `accountId`, `kind`, `readable`, `joined?`, `dmUserId?`, `parent?: RegistrationRef`,
`mappingGeneration`. `:56-57`'s separate record is the `ref`/`current` **view**, not facts, and
`:57` says it "isn't consumed by the compiled ops in this slice".

So the implementer has three moves, and the brief sanctions none of them:
- **widen `VerifiedScopeFacts` with a required member** — breaks the §2:54 bidirectional assertion
  and the §4:172-174 `test/stubs/f-host-t2/v1` typecheck. Probe (`$ORGASMIC_EVIDENCE_DIR/q.ts`,
  `tsc --strict --noEmit`): `TS2741: Property 'parentAuthority' is missing in type
  'VerifiedRegistrationScope' but required in type 'VerifiedScopeFacts'` → exit 2. A mid-slice stop.
- **widen it with an optional member** — passes silently. Probe (`p.ts`, same tsc): **exit 0**, both
  assignment directions and both `extends` directions. The §4:173-174 "discriminating negative" for
  the bidirectional facts assertion therefore **cannot** detect the widening (TS applies no
  excess-property check to non-fresh types), so `:54`'s "structurally exactly" control is silently
  defeated by precisely the change D1's fix forces. No gate in §4 catches it.
- **read `parent` presence as the fact** — reopens D1 verbatim. That is the `:181` mutant
  ("sub-conversation admitted without the parent-authority conjunct (parent presence alone)") and is
  banned at `:104-105`.
*Fix (one of, stated in §2):* (a) add a **second** injected reader for the parent-authority fact,
e.g. `compileDynamicSelectors<F, P>(config, facts, parentAuthority: (scope: VerifiedScope) => P | undefined)`,
keeping `VerifiedScopeFacts` byte-exact to the AF shape and the bidirectional assertion intact; or
(b) keep one reader but declare `VerifiedScopeFacts` as an explicit **host superset** — rename the
exactness control to a one-way assertion against the pinned `VerifiedRegistrationScope` plus a
named, enumerated host extension, and give §4 a negative that fails if any member other than the
single named extension is added (the current bidirectional negative will not do it). Either way,
state that the fact is **not** derived from `parent`, and keep `:108`'s "missing … facts deny".
**Soma's addition folds into this same fix site:** the brief must also say that a *naked
parent-presence flag or boolean* does not count as authority, and that the fact is **bound to the
requested scope** with no reuse from another scope or `ref`. Neither is pinned today: `:104` says
only "present for this scope", and the reader `:46` `(scope: VerifiedScope) => F | undefined` is
keyed by scope but places no binding requirement on `F`'s contents, so a stub `parentAuthority: true`
inside the facts object would satisfy `:102-104` as written while being exactly the naked boolean
Soma rules out. Note the scope-binding requirement must not be written so as to need `ref`/`current`
in the compiled ops — that is the §6:210 stop and belongs to T4 (`:144`); bind it to the scope value
the reader was called with, or to `parent` *plus* the fact, never to `current(ref)`.

**E2 MEDIUM (test / unmet acceptance; Soma's addition) — §3.3 `:102-109`: v4.1 states the child rule
as prose, and pins no child truth table against the two kept witnesses.**
Checked each of Soma's four rows against v4.1:
- **(1) `subConversations` disabled or missing ⇒ deny** — *derivable, not pinned.* `:102-103`'s
  "deny unless both (a) … `subConversations: true`" covers disabled; `:83` (iii) "Selector present,
  member missing or list empty ⇒ deny" covers missing. Two separate places, neither tied to the row.
- **(2) parent merely present, no authority fact ⇒ deny** — *explicit.* `:104-105` "`parent`
  presence is necessary, never sufficient, and never admits on its own", with the `:181` mutant.
- **(3) fact absent, or unverifiable/undefined from the reader ⇒ deny** — *explicit.* `:108`
  "Missing, unknown or unverifiable facts deny and can never become affirmative scope"; the reader's
  `F | undefined` (`:46`) makes the undefined case the same path; `:182` mutant "unknown fact allows".
- **(4) the controlled `stub-of-projector` affirmative ⇒ admit, test-only** — **not stated at all,
  and this is the fail-open-adjacent gap.** v4.1 contains no affirmative child sentence: `grep -n
  '⇒ admit\|admitted only if'` returns only `:143`, which is the *projector slice's* acceptance, not
  this slice's rule. The admit path exists only as the residue of "deny unless both", which an
  implementer can read as "admit **if** both" — i.e. an early admit on the two child conjuncts that
  never evaluates `:98-101`'s `readable` fact, `joined`/`joinedReadable`, `dmUsers` identity,
  `channels` membership or account match. That reading is a real fail-open for children.
It stays MEDIUM rather than HIGH because the literal wording is a deny rule, §3.3 places it inside
the one numbered item that enumerates the general fact conjuncts, and `:108-109` ends the item with
the deny-on-missing-facts and non-authoritative-descriptor rules — so the conjunctive reading is the
natural one. But it is nowhere *required*, and the two kept witnesses (`:131-133`) are exactly the
tests that would pass under the early-admit reading.
*Fix:* put Soma's four rows in §3.3 as a table, each naming its witness, and state row (4)
explicitly as "admit **only if** the two child conjuncts hold **and** every `:98-101` fact conjunct
for the operation holds — test-only, `stub-of-projector`". Soma's remaining three checks are already
pinned and need no change: the injected fact is test-only (`:49-51`, `:106`, `:234-235`), production
issuance is pending at I6 (`:146`, `:70`, `:35`), and `ref`/`current`/epoch is pending at T4
(`:144`, `:210`, `:200`).

**E3 MEDIUM (test) — `:132` and `:180`: `dynamic-read-sub-conversations-denied`'s mutant survives
unless the fixture is required to supply the parent-authority fact.**
`:131` correctly pins the DM row's fixture ("with the stub parent-authority fact"). `:132` pins only
"(the `subConversations` conjunct)" for the read row. If that row's double omits the fact, the deny
comes from conjunct (b), deleting the `subConversations` check (mutant `:180`) changes no verdict,
and the mutant survives by construction — the same defect as D1, one conjunct down. The §4:175-176
runner discipline ("named kills, zero selection rejected") turns this into a mid-slice stop rather
than a shipped fail-open, but the row's witness claim at `:132` is overstated as written.
*Fix:* at `:131-132`, state the fixture for each kept row: `subConversations: false` **with** the
`stub-of-projector` fact present (read row), and `subConversations: true` with the fact present
(DM row). Then both `:180-181` mutants are individually killable.

**E4 LOW — `:5`: the Status line is garbled:** "v4 was v4 folds re-check 1 (CONV-FMD5N, …)". Reads
as an unfinished edit of v4's own status sentence. Cosmetic, but this line is the provenance chain
for three prior review shas.

**E5 LOW — `:9` vs `:233-234`:** `:9` still says "A bounded re-check of **v3** precedes
implementation"; `:233-234` correctly says a delta check of **v4.1**. Delete or update `:9`.

**E6 LOW — `:28` vs `:61-69`:** §1 still advertises the I7 supply as "`registration-view.ts:6–32`
signatures" while §2 now narrows the transcription to `:6-25` and drops `RegistrationScopeConfig`
(D3). Harmless for the §6:215 stop (the drop holds the whole file), but the two ranges should agree
or `:28` should say "signatures `:6–32`, of which `:6-25` is transcribed".

**E7 LOW — `:66`: `ConnectionKey` is cited but is not reachable from the transcribed range.**
`ConnectionKey` appears in `registration-view.ts` only at `:29` (`RegistrationScopeConfig.project`)
and `:37` (`AcceptedRegistration`), both outside `:6-25` and both now untranscribed. The citation
(`test/stubs/f-host-3/v1/a-contract.ts:174`, verified correct at `aecd4a8c`) is right but dead for
`view-contract.ts`; it is presumably needed by the sibling `dynamic-policy.ts`. Say which file needs
it, so the implementer does not import it into `view-contract.ts` and trip the no-unused-import rule.

**E8 LOW — `:130`:** "In-slice, these rows have only the negative witness '**child denied by
default**'" is stale under the new rule — there is no longer a blanket default deny. Substantively
still true (no fact ⇒ deny), but the phrase should read "child denied for want of a parent-authority
fact" so the 7 projector rows' in-slice witness is not mistaken for the removed blanket deny.

## D1–D6 disposition

- **D1 (HIGH) — rule closed, carrier open.** `:102-108` restores the conjunction the delta check
  asked for: deny unless (a) `subConversations: true` **and** (b) the injected projector-supplied
  parent-authority fact; "`parent` presence is necessary, never sufficient, and never admits on its
  own"; "Compiler-core computes no parent authority from in-slice facts"; the fact exists "**only**
  in test doubles, labelled `stub-of-projector`"; "not completion of the child/parent rule (T1 :65)".
  - *No affirmative path from in-slice facts:* **confirmed as written.** The fact is injected, never
    computed; `:108` keeps "Missing, unknown or unverifiable facts deny and can never become
    affirmative scope"; `:146` keeps production issuance at I6; `:206` keeps the `src/` writer stop;
    `:234-235` makes the slice test/stub-only with no runtime issuance.
  - *The two rows really have a witness, and it is not a completeness claim:* **yes.** Under the
    conjunction, `dynamic-dm-sub-conversation`'s mutant ("deny valid DM child identity scope",
    `9d022502:docs/design/f-host-t2-brief.md:217`) now changes a verdict, and `:131-133` labels both
    witnesses `stub-of-projector` "with no claim about the real projector"; `:156` moves the real
    acceptance to the projector slice; `:200` keeps "projector/parent evaluation" in §5 Not claimed.
    The read row's fixture is under-specified — **E3**; the affirmative row is not pinned — **E2**.
  - *Does the fact itself become a fail-open?* The **deny-without-fact** direction is explicit and
    safe (`:104-105`, `:108`). The **carrier** is not named, and the one candidate is forbidden —
    **E1**. The residual trust in the stub fact (e.g. a double could assert authority for a bogus
    claimed parent, as in `dynamic-claimed-parent-overrides-actual`) is disclosed scaffolding, not a
    defect, given test-only residency and `:107`/`:133`/`:200`.
- **D2 (MEDIUM) — closed, conditional on E1.** `:180-181` names two distinct conjuncts
  ("without the `subConversations` conjunct"; "without the parent-authority conjunct (parent presence
  alone)") and asserts "two distinct sites (D2)". Under the restored rule the compiled predicate
  genuinely has ≥2 separately-deletable conjuncts, so the unique-site discipline
  (`aecd4a8c:test/stubs/f-host-4/run-mutants.mjs:40`) and distinct names (`:36`) are satisfiable.
  Both sites exist only once E1's carrier exists; and see E2 for the first one's fixture.
- **D3 (MEDIUM) — closed; verified resolvable at `aecd4a8c`.** `:61-69` transcribes
  `RegistrationRef`, `ReadonlyData`, `VerifiedRegistrationScope`, `RegistrationView` from
  `registration-view.ts:6-25`. Checked at the pins:
  - the range includes `:6` `declare const registrationIdentity: unique symbol`, which `:7`
    `RegistrationRef` needs — correct, and the reason the range starts at 6 rather than 7;
  - `RegistrationDescriptor` (`:11` `DeepReadonly<ChannelDescriptor>`) → `ReadonlyData`, the
    `f-host-2a/v0/stubs.ts:19` precedent: **`:19` is exactly** `readonly descriptor: ReadonlyData;`
    at `aecd4a8c`. `git grep -E 'ChannelDescriptor' aecd4a8c -- src test` → **no hits**, confirming
    there is no host counterpart and the substitution is required, with the assertion correctly
    stated one-way in that direction only;
  - `RegistrationScopeConfig` dropped, which also removes the `ConnectionKey` and `AbortSignal`
    dependencies of `:29-31` (see E6);
  - `f-host-3/v1/a-contract.ts:174` is `export type ConnectionKey = …`, and
    `f-host-4/v2/b-contract.ts:1-11` is the sha-citation header, both as cited.
  Nothing in `:6-25` after the substitution is unresolved, so the §4 `f-host-t2/v1` typecheck can
  declare its assertion target. The D3 mid-slice stop is removed.
- **D4 (LOW) — closed.** `:104` "necessary, never sufficient" is live again: the slice does read
  `parent`, and `:181` mutates its sufficiency.
- **D5 (LOW) — closed.** `:233` makes the drop the first ordered precondition; the drop exists and
  verifies (table above).
- **D6 (LOW) — closed as disclosure.** `:228-229` states plainly that the 17 h ceiling can exceed
  `:703`'s 12 h floor and labels it a planning risk; `:220` keeps the estimate as an unreviewed
  operator estimate. The thin per-witness density is unchanged but was LOW-as-disclosed.

## §3b projector slice — sufficient

Scope `:153-157` (verify claimed vs actual parent `RegistrationRef`, evaluate the parent's own read
selector, reject claim/native disagreement and unknown native roots, issue the fact T2-core
consumes) with acceptance = the 7 projector rows and named mutants. Inputs `:158-159` (T1 @
`bff8c46e` :296-340; I7 `project(...)` @ `a0970645`; the T2-core compiler and fact type; the .3/.4
seam). Dependencies `:160-161` (T2-core, I7 source, I6 for production; T4 rows stay in T4).
Estimate `:162-163`, 8–14 h, operator-labelled.
- *Counted once:* **yes.** `:163` "added **once** as a node in EM8Q2-DAG (v3), parallel to T4 after
  T2-core, not as a new bucket"; `:227-228` reconciles it to the previously-unbudgeted line.
  `grep -n projector /srv/review-drop/tarn-rtaln-20261030/EM8Q2-DAG-v2.md` → no hits, so there is no
  pre-existing node to double-count. (DAG v3 does not exist yet; `:151`'s "to be added" is the
  correct tense for that.)
- *Row allocation consistent:* the §3.6 projector list `:125-127` is exactly 7 rows, matching `:143`
  and `:156`.
- *No owner menu:* **yes.** The §3a header is now "Responsible engineering slice" / "Acceptance
  evidence at that slice", and `:148-149` states "engineering work, not an owner permission menu; no
  policy choice is open here", with the §6 STOP preserved. `:8` "Nothing here is an owner decision"
  and §5 `:200-202` are intact. Soma's rule is applied without converting an allocation into a
  permission.

## Verification Gates

Read-only inspection; no build gate was assigned and none is applicable to a brief delta. No
`orgasmic dispatch gate` receipt is claimed.
- `sha256sum` on the four pinned artifacts; `sha256sum -c` on the drop — all match (table above).
- `git show a0970645:<path>` + `cmp` for both drop files; `git diff --stat 29b5ebf3 a0970645 --
  src/mcpl/registration-view.ts` (PROVENANCE claim); `git log --oneline -1` on both AF pins.
- `git show aecd4a8c:<path>` for `test/stubs/f-host-2a/v0/stubs.ts`,
  `test/stubs/f-host-3/v1/a-contract.ts`, `test/stubs/f-host-4/v2/b-contract.ts`;
  `git grep -cE 'ChannelDescriptor' aecd4a8c -- src test`.
- `diff -u T2CORE-BRIEF-v4.md T2CORE-BRIEF-v4.1.md` (full delta read).
- For Soma's addition: `grep -n "truth table|⇒ admit|admitted only if"` over v4.1 → only `:143`
  (the projector slice's acceptance); `grep -n "for this scope|bound to|reuse|another scope|naked"`
  → only `:104` and an unrelated `:170`. Both greps are the evidence for E2 and for E1's binding
  sub-requirement.
- **Out-of-list probe, disclosed:** two scratch TypeScript files in `/tmp/d1probe` (copied to
  `$ORGASMIC_EVIDENCE_DIR/p.ts`, `q.ts`), compiled with `tsc --strict --noEmit` from a typescript
  install outside the pinned repos. This is the evidence for E1's second bullet. It touched no pinned
  repo, clone or worktree — it reproduces only the brief's declared assertion shape. Results:
  optional extra member → exit 0 (control silently passes); required extra member → exit 2 /
  `TS2741` (control fails).

## Unmet criteria / test gaps

1. **E1** — conjunct (b) of the slice's central rule is not implementable from the brief; the §4
   bidirectional-facts negative cannot detect the widening the fix forces.
2. **E2** — no child truth table; in particular the affirmative row is unstated, so nothing in the
   brief forbids an early child admit that skips the `:98-101` fact conjuncts.
3. **E3** — `dynamic-read-sub-conversations-denied` has no stated fact-present fixture, so its
   `:180` mutant is not yet guaranteed killable.
4. Unchanged from the prior round, correctly disclosed, not re-charged here: no production issuance
   or binding (I6), no real projector or verifier, no native verification (T3), no lifecycle
   barriers (T4), no storage parity (T5), no H loader; `:184-189` expects the docs-contract pass to
   report **FAILED** and must not be reported as "all gates pass"; the per-row table for all 49 rows
   (`:134-135`) and the "49 independent controls must not be claimed" rule (`:137`) are the real
   anti-overclaim controls and are intact.

## Residual Risk

- **Carrier drift (from E1).** The most likely implementer resolution is the silently-passing
  optional member, which keeps the typecheck green while retiring the `:54` exactness control
  against the AF shape. The fact double then diverges from `VerifiedRegistrationScope` with no gate
  recording it — and when the projector slice (`:156`, `:159`) later issues the real fact, it
  inherits an undocumented host-side shape.
- **Early child admit (from E2).** Until row (4) is written as a conjunct, the two kept witnesses
  are satisfiable by a child branch that admits on `subConversations` + fact alone; both tests would
  be green and both `:180-181` mutants still killed, so no gate in §4 would surface it.
- **Scaffolding read as coverage.** The two kept rows now pass on a `stub-of-projector` fact. The
  labelling at `:131-133`, `:107`, `:200` is explicit, but the §3.6 counts and the report wording
  are where this would leak; the per-row table and `:137` are the mitigation, and they depend on the
  worker honouring them.
- **Planning.** `:228-229`'s disclosure stands: 11–17 h for compiler-core plus 8–14 h for the
  projector against a 12–20 h `:703` row means `:703` cannot hold the whole of T2. Operator estimate,
  unreviewed, correctly labelled.
- **`:170` matched base.** The `aecd4a8c` matched-base requirement and the one-LOW-commit-beyond
  caveat on the .4 pin (`:27`) are unchanged by this delta and were not re-examined.

## Fix Directions (minimal, ordered)

1. **E1** — in §2, name the parent-authority fact's carrier: either a second injected reader
   (preferred; keeps `VerifiedScopeFacts` byte-exact and the bidirectional assertion meaningful) or
   an explicitly enumerated host extension with `:54` demoted to one-way plus a §4 negative that
   fails on any *unnamed* added member. State that the fact is never derived from `parent`.
2. **E2** — add Soma's four-row child truth table to §3.3, with row (4) stated as an *additional*
   conjunct over `:98-101`, not a substitute for it, and each row naming its witness.
3. **E3** — in `:131-132`, pin each kept row's fixture (fact present in both; `subConversations`
   false for the read row, true for the DM row).
4. **E4–E5** — repair the `:5` Status sentence; update or delete `:9`'s "re-check of v3".
5. **E6–E8** — reconcile `:28`'s `:6–32` with `:61-69`'s `:6-25`; say which file needs
   `ConnectionKey`; reword `:130`'s "denied by default".
6. Then start per `:233-236` (drop verified — already true; delta check with no open HIGH; Soma's go).
