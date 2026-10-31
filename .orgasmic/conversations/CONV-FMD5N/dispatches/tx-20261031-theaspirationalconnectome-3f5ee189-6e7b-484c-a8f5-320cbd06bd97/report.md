# CONV-FMD5N — bounded DELTA check of T2CORE-BRIEF-v4 (v3→v4), read-only

Pins verified by `sha256sum`: v4 `70beae75…f940f5` ✓, v3 `625210b9…f505` ✓, re-check 1
`5a918d35…c739` ✓. Clones/pins as re-check 1: host `bff8c46e`, `9d022502`, `a316200c`,
`c353e1c8`, `aecd4a8c`; AF `a0970645` — all resolve. Read-only: `git show` / `git grep` /
`git log` / `git ls-tree` / `diff` / `sha256sum`. No build, edit, install, gate run or network.

## Verdict

**READY WITH CHANGES** — required: **D1**, **D2**, **D3**.

v4 closes re-check 1's H1 in the direction asked (children now deny-by-default, no affirmative
path) and closes M1–M6 and L1–L6. Soma's rules (a), (b) and (c) all hold under v4 — (b) is now
fully satisfied, where v3 was only partly. No fail-open path is introduced by v4.

The new defect is the opposite error: §3.3 **over-corrected** past re-check 1's stated fix
("deny *unless* a projector-supplied parent authority is present") to an **unconditional** child
deny. That makes v4's own claim at `:122` — that two rows keep a host witness — false, and makes
two of its §4 child mutants a single unkillable site. Fail-closed, so not a security finding, but
it is the same unexercised-criterion/over-claim pattern re-check 1 ranked as residual risk #1.

## Findings

**D1 HIGH (correctness / unmet acceptance) — `T2CORE-BRIEF-v4.md:96-99` vs `:122`: the
unconditional child deny destroys the host witness v4 still claims for
`dynamic-dm-sub-conversation` and `dynamic-read-sub-conversations-denied`.**
`:96-98`: "`kind === 'sub-conversation'` ⇒ **deny** in this slice … **No child is admitted in
compiler-core**." `:122` nevertheless states both rows "keep their host witness".
- `9d022502:docs/design/f-host-t2-brief.md:217` — `dynamic-dm-sub-conversation`, mutant to reject:
  "**deny valid DM child identity scope**". v4's specified in-slice behaviour *is* that mutant: a
  valid DM child is denied by the `kind` rule before `dmUserId` identity is ever evaluated. The row
  is positive; it cannot pass under v4's rule except by being silently redefined as negative.
- `:228` — `dynamic-read-sub-conversations-denied`, mutant "**ignore read `subConversations`
  selector**". Under a blanket child deny, deleting the `subConversations` selector check changes
  no verdict, so the mutant **survives by construction** and the host test passes without
  exercising its criterion.
T1 `bff8c46e:…scope-contract.md:49-50` makes `subConversations:true` part of Mapping X read, and
`:65` requires "both the child rule **and** its verified parent rule" — a conjunction, not an
unconditional deny.
*Fix:* restore re-check 1's wording: child ⇒ deny **unless** the selector's `subConversations` is
true **and** a projector-supplied parent authority fact is present; `parent` presence necessary,
never sufficient; the parent-authority fact itself is supplied only by the projector gate (§3a
row 1, `:132`), so compiler-core still admits no child on in-slice facts. Then either keep `:122`
as written (the two rows regain real witnesses) or move both rows into the witness-only list and
correct `:122`. Do not leave `:122` and `:96-98` as they stand.

**D2 MEDIUM (test) — `:154`: the two child mutants collapse to one mutation site and are not
expressible under the existing runner discipline.**
`:154` requires both "sub-conversation without parent admitted" and "sub-conversation admitted on
parent presence alone". Under `:96-98` there is no parent-presence branch to mutate — the only
site is the single blanket deny, and both mutants reduce to deleting it. The runners require a
unique site per edit (`aecd4a8c:test/stubs/f-host-4/run-mutants.mjs:40`,
`assert.equal(changed.split(needle).length - 1, 1, 'unique mutation site')`) and distinct names
(`:36`), so one of the two is a duplicate kill and the pair over-states coverage.
*Fix:* with D1 applied, both become expressible (drop the `subConversations` conjunct; drop the
parent-authority conjunct). Without D1, delete one and say the other is the sole child mutant.

**D3 MEDIUM (bug / mid-slice stop) — `:59-62`: the `view-contract.ts` transcription list is
incomplete for its own one-way assertion target, so the §4 `f-host-t2/v1` typecheck cannot pass.**
`:59-62` transcribes "only `registration-view.ts:6-32`" naming `RegistrationRef`, `ReadonlyData`,
`VerifiedRegistrationScope`, `RegistrationView`, `RegistrationScopeConfig`, and states the
assertions run against "**this** AF `RegistrationView` (`:22-25`)". At the pin,
`a0970645:src/mcpl/registration-view.ts:23` types `lookup`'s `descriptor` as
`RegistrationDescriptor`, which is `:11` `DeepReadonly<ChannelDescriptor>` imported from
`./types.js` (`:4`) — and `ChannelDescriptor` **does not exist anywhere in the host tree at
`aecd4a8c`** (`git grep -E 'ChannelDescriptor' aecd4a8c -- src test` → no hits). `:29`'s
`project` likewise needs `ConnectionKey` (resolvable, `test/stubs/f-host-3/v1/a-contract.ts:174`,
but unnamed in the list) and `AbortSignal`. As written the file cannot declare its assertion
target: `tsc --strict` over `test/stubs/f-host-t2/v1` fails on an unresolved name — the same
mid-slice stop re-check-1 M3 removed, one layer narrower.
The precedents both resolve this explicitly: `aecd4a8c:test/stubs/f-host-4/v2/b-contract.ts:12`
imports its transitive type from the a-contract and inlines the rest; `9d022502:test/stubs/
f-host-t2/v0/stubs.ts:9` and `aecd4a8c:test/stubs/f-host-2a/v0/stubs.ts:19` sidestep it by typing
descriptor as `ReadonlyData`.
*Fix:* name `RegistrationDescriptor` and its stand-in in `:59-62` — either a local opaque
`ChannelDescriptor` placeholder declared in `view-contract.ts` (citing that it is a placeholder,
not an AF shape) or `ReadonlyData` as in `f-host-2a/v0:19`, with the resulting assertion stated as
one-way in that direction only — and name `ConnectionKey`'s source as `a-contract.ts:174`. If
`RegistrationScopeConfig` is not consumed by this slice (`:57` says the `ref`/`current` model
"isn't consumed by the compiled ops"), drop it from the transcription instead.

**D4 LOW — `:97`:** "`parent` presence is necessary, never sufficient" is dead under `:98` "No
child is admitted in compiler-core": nothing in the slice reads `parent` at all. Harmless if D1 is
applied (it becomes true again); otherwise delete the sentence so the implementer does not write an
unreachable check and count it as a control.

**D5 LOW — `:56-58`:** `/srv/review-drop/af-a0970645-view/` does not exist yet (only
`af-a869bb3a-contract`, `af-b29edcf8-contract`, `af-b29edcf8-contract-v2` are present). `:56` says
"The operator makes this drop", but §4's "Pinned-drop byte check" (`:150`) does not state the drop's
existence as a precondition of starting. One sentence in §8 ordering it before coding closes it.

**D6 LOW — `:193-197`:** the raised 11 h floor is still thin against the basis it now lists.
After the ten new witness-only rows (`:115-121`) and the four earlier ones plus the 4-row
`lookup → undefined` collapse (`:120-124`), ~32 distinct host witnesses remain; the listed infra
(per-row table, view-contract, four typechecks with negatives, ~10 mutants, checker input edit,
human neutrality read, two 3000 s-bounded suite runs) plausibly consumes 5–6 h of the 11, leaving
<10 min per witness. Also, the 17 h ceiling now exceeds the **12 h floor** of host `:703`
(`a316200c:docs/design/generic-recipient-host.md:703`, 12–20 h) — compiler-core alone can consume
the whole row. `:197-200` already labels `:703` overcommitted and the projector unbudgeted, so this
is disclosed, not hidden; it is a planning risk only. The estimate remains correctly labelled an
unreviewed operator estimate.

## Item 1 — re-check 1 disposition (H1, M1–M6, L1–L6)

| Re-check 1 | v4 | Citation |
| --- | --- | --- |
| **H1** presence-only child admit (fail-open) | **closed** (over-corrected) | `v4:96-99`: `kind === 'sub-conversation'` ⇒ deny, "no child is admitted in compiler-core", admit path owned by the projector gate, "no host witness is claimed here". Deny-by-default, **no affirmative path** — the fail-open is gone and Soma (b) is satisfied. New defect from the over-correction: **D1**, **D2**, **D4**. |
| **M1** ten vacated rows unclassified | **closed** | `v4:115-121` lists all ten with owner gate; verified per row against `9d022502:docs/design/f-host-t2-brief.md:172-228` — projector 7: `t02-unreadable-parent` `:182`, `t02-parent-outside-read` `:183`, `t03-parent-access-revoked` `:191`, `dynamic-claimed-parent-widening` `:200`, `dynamic-claimed-parent-overrides-actual` `:201`, `dynamic-parent-not-asserted` `:225`, `dynamic-parent-claim-native-disagreement` `:227`; T4 3: `dynamic-cross-connection-parent` `:202`, `dynamic-stale-parent-epoch` `:212`, `dynamic-parent-removed-before-ingress` `:213`. **All ten match**, correct owner split, and `t01-unreadable-parent` `:177` is correctly excluded (native-only). `v4:123-124` requires the per-row table for all 49 as the first commit with per-class counts. The `:122` retained-witness line is wrong — D1. |
| **M2** compiler residency | **closed** | `v4:40-44`: `test/stubs/f-host-t2/v1/dynamic-policy.ts`, "zero `src/` change", no closed edit list. Citation verified: `aecd4a8c:test/stubs/f-host-4/v2/static-policy.ts:50` is `compileStaticRecipientAdmission`, `:51`/`:53` call `compileRecipientAdmission` via the `:4` deep import, `Lane` handled by the `:5` a-contract `AdmissionLane` alias. The "production passes no reader" framing is replaced by `v4:45-46` "Production does not call this compiler at all", as asked. |
| **M3** AF drop residency vs typecheck | **partly closed** | `v4:56-58` moves the byte drop outside the repo with SUMS + PROVENANCE; `v4:59-62` adds the in-repo transcribed surface and names which `RegistrationView` the one-way assertion targets (AF `:22-25`, not the v0 alias). The transcription's symbol list is unresolvable — **D3**; drop not yet made — **D5**. |
| **M4** one-sided conjunction mutant, empty-block escape | **closed** | (i) `v4:85-87`: "`reply = reply ∧ read` is an **intersection, not a copy**: reply keeps its own members", and `t2-selector/independence` (`:136`) is named as staying in this slice; `v4:154` adds "reply drops its own members and mirrors read". (ii) `v4:71-74`: **only** syntactic absence reaches level (i); a present `{}` is level (ii) and "**never** `policy === undefined`" — matches `aecd4a8c:src/recipient-admission.ts:104` `if (policy === undefined) return {}`; `v4:153` adds the "present-but-empty block compiles to `undefined`/feature-off" mutant. |
| **M5** docs-contract expected result, EC6WW collision | **closed** | `v4:157-161`: expected **FAILED** on the declared-filename FP (`run-mutants`), "never 'all gates pass'"; sequencing stated — T2 edits the `aecd4a8c` checker, EC6WW `8d211982` rebases over it later as its own task, no merge; the Crockford-token note is routed to the human read. |
| **M6** estimate basis, projector budget | **closed** | `v4:193-197` raises 8–12 → **11–17 h** and enumerates the §4 additions in the basis (purity, view-contract, per-row table, optional v0, four typechecks with negatives, t2 runner + ~10 mutants, checker edit, neutrality read, matched-base suite base/head). `v4:198-200` replaces "counted separately" with T4 budgeted at `:705` (10–16 h, verified) and the **projector slice unbudgeted** against `:700-708`, needing its own incremental estimate. Residual floor/ceiling arithmetic — **D6**. |
| **L1** purity under double evaluation | **closed** | `v4:88-89` requires pure predicates and reader, states `read` runs twice per admit and per authorize (verified: `recipient-admission.ts:199` then `:211`; `:135` then `:148`); `v4:155` adds the "predicate memoises its first verdict" mutant. |
| **L2** `readable` fact vs seam | **closed** | `v4:91-93`: "the injected fact `readable`, not `trusted.readable`; the seam keeps its own check at `:199`/`:126`, so this is defence in depth". |
| **L3** baseline wording | **closed** | `v4:144-145`: matched base "for tsc and the full suite **only**"; §3.5 controls are same-head seam or stub-vs-stub. |
| **L4** missing `f-host-2a/v0` typecheck input | **closed** | `v4:147-148` names `test/stubs/f-host-t2/v0` and "its transitive `test/stubs/f-host-2a/v0`". Verified: `9d022502:test/stubs/f-host-t2/v0/stubs.ts:7` imports `../../f-host-2a/v0/stubs`. |
| **L5** unexpressible static-fallback mutant | **closed** | `v4:151-153`: "absent selector compiles to allow-all", static fallback demoted to a typecheck/compile negative "since the dynamic compiler never imports `static-policy.ts`". |
| **L6** `.4` pin report citations | **closed** | `v4:25` adds `TASK-4ZM52-REVIEW1` sha256 `8f7c7341…`, `TASK-4ZM52-LOWS1` `dfc8a3c7…`. Both verified at `/srv/review-drop/`: `8f7c7341ff4d5bda…` and `dfc8a3c7f53db34a…` — **match**. |

## Item 2 — Soma's rules under v4

**(a) Two separate controls, no escape route — SATISFIED.** `v4:71-76` pins three distinct levels,
closes the present-but-empty escape re-check 1 left open (`:72-74`), and `v4` §6 still forbids the
static fallback rather than encoding it.

**(b) Barriers kept, missing facts never affirmative — SATISFIED** (was partly, in v3). `v4:96-99`
removes the affirmative child path entirely; `v4:132` keeps the moved parent requirement with
acceptance at the projector gate ("claimed parent never overrides actual; unknown native root
denies"); descriptor/ACK non-authority carried at `v4:99` (T1 `:94-138`). The over-correction
(D1) is a fail-**closed** error, so it does not weaken (b).

**(c) Reader not issuance/constructor/loader proof; the three loader controls pending — SATISFIED,
unchanged.** `v4:45-49` keeps the explicit bars (not production issuance, not a brand constructor,
not loader proof; no decoding of unverified objects, no descriptor/ACK trust, no brand minting, no
synthesised generation authority) and now states production does not call the compiler at all — a
stronger statement than v3's. §3a rows 3–4 (loader feature-off trio, I6 issuance) are byte-unchanged
from v3.

## Item 3 — new contradictions / fail-open introduced by v4

**No fail-open path is introduced.** Every v4 delta moves in the deny direction. The contradictions
are **D1** (`:122` vs `:96-98` — and specifically: under deny-by-default
`dynamic-dm-sub-conversation` `:217` loses its `dmUserId` witness and
`dynamic-read-sub-conversations-denied` `:228` loses its `subConversations` witness, so **neither
keeps a host witness, contrary to `:122`**), **D2** (collapsed mutant sites), **D3** (untypecheckable
transcription), **D4** (dead "necessary" clause). The remaining v4 deltas — residency, drop split,
three-level wording, intersection-not-copy, purity, L2/L3/L4/L5 wording, docs-contract expectation
and sequencing, estimate — introduce no contradiction I can find against T1 `bff8c46e`, T2-input
`9d022502`, or `aecd4a8c`.

## Item 4 — the 11–17 h estimate

Basis list now covers the §4 additions (M6 closed). Direction is sound and correctly disclosed:
`:703` = 12–20 h verified at `a316200c:docs/design/generic-recipient-host.md:703`; T4's separate
10–16 h at `:705` verified; the projector is correctly called **unbudgeted** against `:700-708`.
Two residual arithmetic points, both LOW and both already flagged by the brief's own overcommit
sentence: the 11 h floor leaves <10 min per remaining host witness after infra, and the 17 h ceiling
exceeds `:703`'s 12 h floor (**D6**).

## Verification Gates

**None run — by brief constraint** (READ-ONLY; `git show`/`grep`/`diff`/`sha256sum` only). No gate
receipt produced or reused. Commands actually run: `sha256sum` on v4/v3/re-check 1 and on
`TASK-4ZM52-{REVIEW1,LOWS1}.md`; `diff -u T2CORE-BRIEF-v3.md T2CORE-BRIEF-v4.md`;
`git log --oneline -1` on `bff8c46e`, `9d022502`, `a316200c`, `c353e1c8`, `aecd4a8c` (host) and
`a0970645` (AF); `git show` on `9d022502:docs/design/f-host-t2-brief.md` (`:168-232`),
`bff8c46e:docs/design/generic-recipient-scope-contract.md` (`:40-95`),
`a0970645:src/mcpl/registration-view.ts` (`:1-35`), and at `aecd4a8c`
`src/recipient-admission.ts` (`:95-135`, `:195-215`), `test/stubs/f-host-4/v2/{static-policy,b-contract}.ts`,
`test/stubs/f-host-4/run-mutants.mjs`, `test/stubs/f-host-3/v1/a-contract.ts`,
`test/stubs/f-host-2a/v0/stubs.ts`, plus `9d022502:test/stubs/f-host-t2/v0/stubs.ts`;
`git grep -nE 'ChannelDescriptor|ConnectionKey' aecd4a8c -- src test`;
`git ls-tree -r --name-only 9d022502 -- test/stubs/f-host-t2`; `ls /srv/review-drop/`.

## Unmet criteria / test gaps

- `dynamic-dm-sub-conversation` has **no** passing host witness under v4 as written, and
  `dynamic-read-sub-conversations-denied`'s mutant is unkillable (D1). The `subConversations`
  selector member — a Mapping X requirement at T1 `:49-50` — gets no in-slice control at all.
- Two of the ten §4 child mutants are the same site (D2).
- The one-way view assertion has no declarable target, so its required discriminating negative
  control (`:148-149`) cannot be written yet (D3).
- Not assessable read-only and out of scope: whether the `aecd4a8c` suite is green, and whether
  `tsc` over the proposed `f-host-t2/v1` succeeds. Both are gate-time facts.

## Residual risk

1. **Over-claim (highest, unchanged in kind from re-check 1 but now fail-closed).** `:122` asserts
   coverage that v4's own `:96-98` removes. A round could report "two child rows witnessed" with
   both tests passing for the wrong reason. One-sentence fix now.
2. **Mid-slice stop.** D3 terminates in an unresolved-name typecheck failure inside the directory
   §4 requires typechecking.
3. **Precondition ordering.** The external AF drop does not exist (D5).
4. **Planning.** 11 h floor and an unbudgeted projector (D6), in the direction `:197-200` already
   warns about.
5. **Out of scope and unchanged from v3/re-check 1:** `.4` activation blocker (owner items 6/7),
   I6/I7 qualification, AF currentness, publication #931 — §5 records all correctly.

## Fix directions (minimal, ordered)

1. **§3.3 `:96-98`** — restore the conditional: child ⇒ deny **unless** `subConversations` is true
   **and** a projector-supplied parent authority is present; keep "parent presence necessary, never
   sufficient" (which then becomes live again, D4). Compiler-core still admits no child on in-slice
   facts. *(D1, D4)*
2. **§3.6 `:122`** — with (1) applied, `dynamic-dm-sub-conversation` and
   `dynamic-read-sub-conversations-denied` genuinely keep their witnesses; without (1), move both to
   the witness-only list and delete the sentence. Do not ship the pair as-is. *(D1)*
3. **§4 `:154`** — with (1), restate the two child mutants against their two distinct conjuncts;
   without (1), delete one. *(D2)*
4. **§2 `:59-62`** — name `RegistrationDescriptor` and its in-repo stand-in (local opaque
   `ChannelDescriptor` placeholder, or `ReadonlyData` as `f-host-2a/v0:19`), name
   `ConnectionKey`'s source (`f-host-3/v1/a-contract.ts:174`), and drop
   `RegistrationScopeConfig` if the slice does not consume it. *(D3)*
5. **§8** — order the `/srv/review-drop/af-a0970645-view/` drop before coding. *(D5)*
6. **§7** — optional: note that the 17 h ceiling exceeds `:703`'s 12 h floor. *(D6)*
