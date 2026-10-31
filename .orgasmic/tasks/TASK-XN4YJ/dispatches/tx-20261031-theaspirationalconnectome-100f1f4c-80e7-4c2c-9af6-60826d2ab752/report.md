# TASK-XN4YJ — independent READ-ONLY review of three DRAFT briefs

Reviewer dispatch, read-only. Operator Tarn; scope from qualifier Soma (Slack 1793441667). **Not an owner decision, not a gate PASS, no implementation cleared.**

Snapshot: `git rev-parse HEAD` = `f378eadffc9a02e2ecb5a4f30e266db9321db4f5` (required value; no branch switch needed). `sha256sum -c SHA256SUMS` in `/srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v1/` returned rc=0 for all four briefs; `REPORT.md` = `afbd5475…` and `T2CORE-BRIEF-v4.3.md` = `6482ad06…` recomputed and match the dispatch brief. The drafts in the reviewed evidence dir are byte-identical to the drop. Full hash log: `progress.md` note 1.

---

## Verdict

| Draft | Verdict |
| --- | --- |
| `PROJECTOR-BRIEF-v1.md` | **Usable with changes** (one MEDIUM: parent-authority scope widens from read to wake/reply inside the frozen seam) |
| `ISSUANCE-BRIEF-v1.md` | **Usable with changes** (one HIGH: an AF-supplied barrier is written up as an open host/T4 gap, and its discriminating test cannot discriminate) |
| `UNKNOWN-INPUTS.md` | **Usable with changes** (N12 inherits the HIGH; N1–N11, N13 and the owner-6/7 separation are correct) |
| `INPUT-VERIFICATION.md` (support) | **Usable as-is.** Every hash, line count and locator claim independently reproduced; zero discrepancies. |

Cite accuracy is unusually high: **38 cites spot-verified, 35 exact, 3 benignly over-wide, 0 wrong**. The row erratum, the §3a/committed-doc locator split, the historical undercount, and the frozen fixture semantics are all preserved faithfully. The defects are not citation defects — they are two scope/consequence errors about what the frozen sources already enforce.

---

## Findings

### HIGH — correctness — `ISSUANCE-BRIEF-v1.md:73`, `:106`; `UNKNOWN-INPUTS.md:22` (N12)

**AF a0970645 already captures and pins the original `RegistrationRef` on the reply/authorize/send path. The brief presents this as an unsolved host/T4 gap and builds a "discriminating" test on it that cannot discriminate.**

The *premise* is correct and I confirmed it: H4 stores only `{key, channelId}` in reply approvals and re-looks-up on authorize —
`git show aecd4a8c:src/recipient-admission.ts | nl -ba`:112 (`approvals = new WeakMap<ReplyInput, Readonly<{ key: Key; channelId: string }>>()`), :137 (`approvals.set(input, Object.freeze({ key: input.connection, channelId }))`), :141-148 (`authorize` compares key identity + destination strings + `replyChannel(input) === approved.channelId`, and `replyChannel` re-calls `trusted.lookup` at :125). No `R` is pinned. ISSUANCE:73's reading of H4 is exact.

The *conclusion* is wrong. AF supplies the barrier itself, and its own doc says so inside a range the brief already cites (`AF:docs/registration-scope-view.md:62–81`, quoted at ISSUANCE:64) — lines 78-81 read: *"B's registered-target validation also freezes the scope ref and checks it synchronously on subsequent authorization/send checks. The host should retain original refs in its resolver/authorize closure…"*. The implementation:

- `git -C /home/orgasmic/src/agent-framework show a0970645:src/framework.ts | nl -ba`:34946 — `const scopeRefs = new WeakMap<object, RegistrationRef>()`
- :34954-34956 — `const ref = scopeRefs.get(target) ?? view?.lookup(target.channelId)?.ref; if (!ref || !view?.current(ref)) return false; scopeRefs.set(target, ref);` → the ref captured on the **first** `valid()` call is reused forever for that reply work; later checks run `current()` on the **captured** ref, never a fresh lookup.
- `src/mcpl/reply-routing.ts`:135 (`destination` frozen once per work), :137 (`valid(input.connection, destination) && authorize(approvedInput, destination) === true` — AF's `valid` is **ANDed** with the host's `authorize`, so a loose host `authorize` cannot widen past it), :140 (`current()` is first invoked at admission time, so the pinned ref is the admission-time generation), :24-26 (`replyBindingCurrent`).
- `src/mcpl/channel-registry.ts`:5993 — at publication: `if (!peer?.requiredAdmission?.isCurrent(binding.connection) || !replyBindingCurrent(binding)) return 'automatic_reply_stale'`.
- Replacement makes the captured ref non-current: `registration-view.ts`:80 (`this.source(record.descriptor.id) !== record`) with :99/:103 minting a fresh ref per acceptance.

**Failure scenario.** An implementer writes ISSUANCE:106 as specified — *"resolve old generation, replace it by an equally named freshly ready registration, then authorize the old work. It MUST fail, even though a new H lookup succeeds. This specifically discriminates the H4 key/channel-only gap."* Against real AF with `registrationScope` configured, that test **passes on AF's `automatic_reply_stale`** (channel-registry.ts:5993) regardless of whether the host ever pinned R. The test is then credited in §6/§7 acceptance as proving host-side original-R retention, and N12 is signed off, while the host closure it was meant to prove was never built. Symmetrically, N12 and §7 list "H binder/resolver/authorize original-R closure wiring" as an engineering *blocker*, which inflates the blocker set and invites a parallel host-side staleness mechanism — exactly the duplicate generation mechanism this review was told to reject.

**Required changes.** (a) In ISSUANCE §4 inv.6, state that AF already freezes and re-checks the scope ref on B's authorization/send path, with the cites above, and reclassify the host-side closure as **defence-in-depth explicitly recommended by `AF:docs/registration-scope-view.md:79–81`**, not an open barrier. (b) State the two conditions AF's barrier is gated on — `registrationScope` configured (`framework.ts:34953`) and `replyAuthority` configured (`:34945`) — since with the profile absent only connection-epoch `isCurrent` applies. (c) Rewrite the ISSUANCE:106 test so it discriminates the host layer: assert the host's own record refuses the old work **with AF's `valid` stubbed to true**, otherwise the assertion is AF's, not the host's. (d) Downgrade N12's first clause from blocker to defence-in-depth; its second clause (reviewed actual-AF nominal-type packaging) stands unchanged. (e) Keep ISSUANCE:69's refusal to add mappingGeneration comparisons / numeric epoch substitutes — that judgement is correct and should be stated as *because* AF R/current already is the lifetime test.

### MEDIUM — correctness — `PROJECTOR-BRIEF-v1.md:29`, `:62`, `:94`, `:116`

**Validating only the parent's *read* selector silently clears the parent for wake and reply, because the frozen seam feeds one `ParentAuthorityFact` to all three predicates. The brief flags the representational limit but its instructions contradict each other and acceptance never requires the wake/reply parent conjunct to be marked uncovered.**

`git show f378eadf:test/stubs/f-host-t2/v1/dynamic-policy.ts | nl -ba`: a single `predicate` factory (:81-101) is instantiated three times at :102 (`read`, `wake`, `reply`), and each instance calls the **same** `parentAuthority(scope)` at :90. The compiler's only parent check is identity (:92, `authority.scope !== scope || authority.parent !== f.parent`); it never evaluates the parent against the wake or reply selector. Wake/reply compose only the **child's** predicates (:105-106).

T1 requires the opposite: `git show bff8c46e:docs/design/generic-recipient-scope-contract.md | nl -ba`:158-163 defines the sub-conversation branch as requiring recursive `Select(S,parent,item)` *for the same S*, and :170-172 builds `WakeCeiling` from `Select(wake,R,item)`. So under T1 a child may wake only if the parent satisfies the **wake** selector.

PROJECTOR:29 instructs the implementer to validate "the actual parent's own **read** selector" — faithful to the frozen allocation (`T:docs/design/dynamic-selector-core.md:81`, "parent's own read selector") — and then PROJECTOR:62 issues two instructions that cannot both be honoured inside the frozen shape: *"Do not overconstrain read by requiring every wake/reply parent selector, and do not treat parent-read success as wake/reply permission."* Returning the fact **is** the wake/reply parent clearance; there is no third option in the frozen seam. The frozen suite demonstrates the outcome: `dynamic-policy.test.ts`:204-237 runs C1-C6 `for (const op of ops)`, and C6 (:231-236) asserts `true` for read, wake **and** reply from one parent fact.

**Failure scenario.** Config `dynamic: { read: { joinedReadable: true, subConversations: true }, wake: { channels: ['mailbox/active'], subConversations: true } }`, child = sub-conversation whose channel is `mailbox/active`, parent = a joined channel not in `wake.channels`. The projector validates the parent's read selector (joinedReadable → pass), returns a positive `ParentAuthorityFact`; the wake predicate re-checks only the child and admits. The child wakes on a parent that fails `Select(wake,parent,item)` — a silent widening versus T1:170-172. The frozen fixture does not catch this because its base policy sets `joinedReadable: true` on both read and wake (`test/fixtures/generic-recipient/scope-cases.json`:9,20) and `parentJoined: true` (:59), so T1 compliance holds there by accident of the fixture, not by the brief's stated validation. §5's eight rows are all read-side, and §7:116's generic "report proof level per row" never names this conjunct.

**Required changes.** (a) In §2, scope the instruction explicitly: the validator qualifies the parent for **read only**, per `T:docs/design/dynamic-selector-core.md:81`. (b) In §4, replace the contradictory pair at :62 with the operational consequence: for `kind === 'sub-conversation'`, **wake and reply admits carry an unqualified parent conjunct and must be reported uncovered**, cross-referenced to N11 and `S:158–163,170–172`. (c) Add that constraint to §7 acceptance so it cannot be signed off silently. (d) In §5, annotate the C6 row (:94) that its `true` for `wake`/`reply` is a model artifact of the shared reader (`dynamic-policy.ts:102`; `.test.ts:231–236`), not parent qualification for those operations.

### MEDIUM — correctness (scope of a negative claim) — `ISSUANCE-BRIEF-v1.md:9`, `INPUT-VERIFICATION.md:55-63`

**The "verified absence" claim is scoped to "these frozen source trees" but only three AF objects were read. This is the root cause of the HIGH finding.**

`INPUT-VERIFICATION.md`:59-63 lists exactly three AF objects: `src/mcpl/registration-view.ts`, `docs/registration-scope-view.md`, `src/mcpl/inbound-admission.ts`. `git -C /home/orgasmic/src/agent-framework ls-tree --name-only a0970645:src/mcpl` returns 40+ files, including `reply-routing.ts`, `reply-admission.ts`, `channel-registry.ts` and `registration-ingress.ts` (the last is imported by `registration-view.ts`:3 and still unread). `git grep -n "privateReplyBinding\|replyBindingCurrent\|reply-routing\|channel-registry\|framework.ts" *.md` over all three drafts returns **rc=1 — zero hits**. The reply-binding currentness machinery was therefore never in evidence when §4 inv.6 was written.

For contrast, the H-side absence claim at ISSUANCE:16 is correctly bounded and I reproduced it: `git grep -ln "VerifiedScope" aecd4a8c -- 'src/*'` → only `recipient-scope.ts`, `recipient-admission.ts`; `git grep -ln "RegistrationView\|VerifiedRegistrationScope\|registrationScope" aecd4a8c -- 'src/*'` → no matches. And ISSUANCE §1:18's headline conclusion ("nothing binds an H handle to authenticated I7 facts plus exact K/R/generation") remains **true** — AF pins its own R to a reply destination, not to an H handle, which does not yet exist.

**Required change.** Restate ISSUANCE:9 as bounded to the objects actually read (list them, as INPUT-VERIFICATION already does), and either read AF's reply/publication path before asserting AF-side gaps or mark such gaps UNVERIFIED pending that read.

### LOW — docs (cite precision) — `ISSUANCE-BRIEF-v1.md:79`, `:12`; `PROJECTOR-BRIEF-v1.md:45`

Three benignly over-wide ranges in briefs whose stated convention (PROJECTOR:7) is exact `nl -ba` line ranges, so an implementer diffing them sees avoidable drift:

1. `ISSUANCE:79` cites the `RefusalCode` union as `AF:src/mcpl/inbound-admission.ts:11–15`; the union is **:11–13** (:14-15 is `AdmissionVerdict`). The nine names listed are exactly correct.
2. `ISSUANCE:12` cites `TrustedRecipientRead` at `H4:…:35–42` while `PROJECTOR:40` cites **:39–42** for the same interface (:35-38 is its doc comment). Both resolve; the two briefs should agree.
3. `PROJECTOR:45` calls `test/stubs/f-host-3/v1/a-contract.ts:173–174` the "T2 key alias"; **:174** is `ConnectionKey`, :173 is `AdmissionLane`.

---

## Required classification

Every material claim/requirement/blocker/proposal, as (E) existing requirement with frozen cite, (S) missing supplied input, (I) genuine missing implementation, (P) proposed contract/trust change.

| # | Claim / requirement / blocker / proposal | Loc | Class | Assessment |
| --- | --- | --- | ---: | --- |
| 1 | HEAD, H3/H4/T1/AF objects exist; H3/H4 ancestors of T; T1 path verified | P:15; I:7 | E | **correct** — reproduced (`merge-base --is-ancestor` rc=0 ×3) |
| 2 | T2 compiler is test/stub-only, not production | P:15 | E | **correct** — `dynamic-policy.ts:1–4` |
| 3 | §3a is a historical-brief heading; committed locator is `dynamic-selector-core.md:25–77`; no table invented | P:23; IV:74 | E | **correct** — no literal §3a in committed doc (grep); `## 3a.` at `T2CORE-BRIEF-v4.3.md:176`. Distinction preserved as required |
| 4 | Responsibility counts 21/9/8/10/1 = 49 | P:25 | E | **correct** — recomputed from column 5 |
| 5 | Proof classes 19/11/19 are distinct from responsibility counts | P:25 | E | **correct** — `dynamic-selector-core.md:17–19` |
| 6 | Erratum tx `e9dfd0e3` verified; counts match; historical/REVIEW1 "7 projector" undercount preserved; NEXT-SOURCE-WORK's "8 projector + 3 T4" mislabels all 11 as parent-family | P:27 | E | **correct** — ledger `b0bf960e:…/TASK-YXASE/journal.org:83–118`; `REVIEW1.md:164`; `NEXT-SOURCE-WORK-v1.md:15`. Erratum table itself says "10 (incl. 3 parent-family)" |
| 7 | Scope = 7 parent-family projector rows + `dynamic-unknown-native-root` | P:27 | E | **correct** — of the 8 projector rows, only :65 is non-parent-family |
| 8 | T4 retains exactly 3 parent-family rows | P:109 | E | **correct** — `A:53,63,64` |
| 9 | All 8 projector row names + fixture ranges + outcome tuples | P:72–81 | E | **correct** — all 8 names byte-exact vs frozen table and erratum; 8 fixture ranges verified byte-exact |
| 10 | `overrides-actual` fails two conjuncts and is not the isolated agreement witness; `claim-native-disagreement` is that control | P:78,80 | E | **correct** — `S:370–379`; fixture notes `F:910,1669` |
| 11 | Fixture SHA `70a1827c…`, byte-identical to the T1 fixture | P:68 | E | **correct** — recomputed both |
| 12 | Frozen types: `VerifiedScope`, `VerifiedScopeFacts`, `ParentAuthorityFact`, `Selector`, `DynamicConfig`, `compileDynamicSelectors`, `CompiledRecipientAdmissionPolicy`, `TrustedRecipientRead`, AF `RegistrationRef`/`ReadonlyData`/`VerifiedRegistrationScope`/`RegistrationView`/`RegistrationScopeConfig`/`ConnectionKey` | P:35–45; I:24–31 | E | **correct** — all verified at the cited objects (see Gates) |
| 13 | C1–C6 compiler child controls | P:87–94 | E | **correct** — `dynamic-policy.test.ts:204–237` maps 1:1. C6's wake/reply `true` needs the annotation at MEDIUM-1(d) |
| 14 | Compiler child conjuncts all still run; negative `undefined` → `false`; no early admit | P:56,64 | E | **correct** — `dynamic-policy.ts:86–100,105–106` |
| 15 | Candidate R initially noncurrent; don't require `current(candidate)` in `project`; accepted parent R must be current | P:60; I:30,43 | E | **correct** — `registration-view.ts:103,105,114` and :125 |
| 16 | AF detaches/clones the projector result, so raw object identity cannot be the binding | I:43 | E | **correct** — `registration-view.ts:116` (`data(raw, this.refs)`) |
| 17 | AF `project` must not do network effects or mutate the framework | P:49; I:30 | E | **correct** — `registration-view.ts:28`; `AF docs:39` |
| 18 | ACK success ≠ scope readiness; late/aborted/timed-out project grants nothing | I:74,99 | E | **correct** — `registration-view.ts:112,115`; `AF docs:50–55` |
| 19 | Every accepted (re-)registration is a new generation; removal/replacement/epoch/shutdown invalidate synchronously; parent invalidation is transitive; equal parent never revives | I:64,68,71 | E | **correct** — `AF docs:62–69`; `registration-view.ts:77–82` |
| 20 | `mappingGeneration` is opaque immutable data; **no** comparison/ordering/numeric-epoch substitute — AF R/current is the lifetime test | I:69 | E | **correct, and the correct rejection of a duplicate generation mechanism** — `registration-view.ts:20,77–82`; `dynamic-selector-core.md:85` |
| 21 | AF captures the ref before A and re-checks it after awaits | I:72 | E | **correct** — `registration-view.ts:134–148` |
| 22 | H4 reply approvals capture key/channel, not R | I:73 | E | **correct** — `recipient-admission.ts:112,137,141–148` |
| 23 | …therefore original-R retention is an open host/T4 engineering gap | I:73; U:22 | I | **INCORRECT as scoped** → **HIGH**. AF already freezes/re-checks the ref on B's authorize/send path (`framework.ts:34946,34954–34956`; `reply-routing.ts:137`; `channel-registry.ts:5993`; `AF docs:78–81`). Valid only as defence-in-depth |
| 24 | H has no constructor/accessor; nothing binds an H handle to I7 facts + exact K/R/generation; T allocates issuance to I6 | I:11,16,18 | E | **correct** — `recipient-scope.ts:1–7`; H-side greps reproduced; `dynamic-selector-core.md:83` |
| 25 | H `initialize` reads only neutral `{version:1, accountId}`, insufficient for T1's scope-producer attestation | I:16; U:15 | E | **correct** — `recipient-presentation.ts:10–23`; `S:94–100` |
| 26 | T2 doubles are cooperative Maps + a cast; not an issuer or bridge; casts are not authenticity | I:14,15 | E | **correct** — `doubles.ts:1–3,19–32`; `typecheck.ts:9–11`; `view-contract.ts:1–5` |
| 27 | Refusal table: existing names only, layer-specific; `project` has no reason field; `initialize_attestation_refused` is an Error, not a RefusalCode | I:83–90 | E | **correct** — `inbound-admission.ts:11–13,33–35,161–169,177–197`; `registration-view.ts:95–129,138–148`; `recipient-admission.ts:161,130–149`; `recipient-policy.ts:3–13`. One over-wide range → LOW(1) |
| 28 | Production `recipient_policy_integration_pending` barrier remains | P:120; I:90 | E | **correct** — `recipient-policy.ts:3–13` |
| 29 | Owner items 6/7 are owner decisions, undecided, and do not block drafting; native/attestation gaps are engineering, not owner items | U:29–32; P:120; I:118 | E | **correct** — `generic-recipient-host.md:476–481`. Correctly refuses to launder engineering gaps into owner escalations |
| 30 | Per-operation recursive `Select(S,parent,item)` is unrepresentable in the frozen operation-independent reader; richer evaluator = new reviewed contract | P:62; U:21 | I | **correct as a limit statement**, **incomplete as guidance** → **MEDIUM-1**. `S:151–173` vs `dynamic-policy.ts:20,64–67,102` |
| 31 | Registration-time `project` has no ingress author/item parameter, so no author-dependent DM ancestor verdict | P:62 | E | **correct** — `registration-view.ts:29–31` |
| 32 | Recursive ancestry and cycle rejection stay T4-owned | P:62,109 | E | **correct** — `S:410–412`; `dynamic-selector-core.md:82` |
| 33 | F2 is a source assertion, not a behavioral kill; conversion stays BLOCKED until an authorized semantic extension | P:111; U:38 | I | **correct and commendably honest** — `dynamic-policy.test.ts:168–174`; `dynamic-selector-core.md:105–110` |
| 34 | N1–N5, N7–N9: native root/relationship/provenance, real descriptors, attestation schema, revocation freshness, mapping-generation origin, native send evidence are NOT SUPPLIED | U:11–19 | S | **correct** — grounded in `S:94–138,149–163,251–261,324–334`; `AF docs:37–48`; `registration-view.ts:12–21`. No values invented |
| 35 | N6: `dynamic-missing-attestation` stays uncovered; its fixture is a wrong-account snapshot, not a producer-attestation witness | U:16 | S | **correct** — `dynamic-selector-core.md:50`; fixture `:856–880` (`accountMatches:false`) |
| 36 | N13: supplied I7 source/drop is local source, not a qualified I6 release | U:23 | S | **correct** — `S:336–342` ("requires a new composed integration SHA/tarball"); `dynamic-selector-core.md:3–4`; AF drop is 2 files + PROVENANCE |
| 37 | N10/N12 second clause: no trusted fact-reader/issuer bridge; reviewed actual-AF nominal-type packaging needed | U:20,22 | I | **correct** — `recipient-scope.ts:1–7`; `recipient-admission.ts:39–42`; `view-contract.ts:1–8` |
| 38 | PROJECTOR's "native fact reader" is a NEW unapproved local seam, not an existing export | P:47 | P | **correct and correctly labelled** — no such export exists in H4 `src/` or the AF objects read |
| 39 | NEW PROPOSED PRIVATE HOST BINDING CONTRACT: single private issuer, object-identity private record, candidate/ready separation, consumer closures | I:33,39–60 | P | **correct as a proposal, properly fenced** — explicitly "not an existing export…not authorization to edit `src/`", needs separate reviewed edit scope + qualifier go. AF remains sole issuer of K/R. No public factory name, wire payload or RefusalCode chosen |
| 40 | Proposed host invariants 1–5, 7, 8 (one handle per accepted R per epoch; retire on epoch change; parent propagation; capture-before-await; late completion issues nothing; admitted store/wake survive a failed send) | I:68–75 | P | **correct**; each mirrors an existing AF/T1 rule rather than inventing one (`AF docs:62–81`; `S:187–192,251–261`) |
| 41 | Required conjunction for a positive parent result; lookup-by-claim never replaces the actual native parent | P:58 | E | **correct** — `S:102–138,151–163,370–379`; `AF docs:46–48` |
| 42 | Two output layers must not be conflated (`VerifiedRegistrationScope` at registration vs `ParentAuthorityFact` at the T2 seam) | P:55–56 | E | **correct** — `registration-view.ts:29–31,95–129`; `dynamic-policy.ts:20,64–67` |
| 43 | Non-goals: no self-attestation, no new grants/wire authority, no real adapter calls, no production claims | P:120; I:118; U:3 | E | **correct** — see Check 6 |

---

## Specific checks

**1. Parent authority — is the ACTUAL operation and item accounted for?** Partly. Both drafts correctly refuse to treat `ParentAuthorityFact` as proof of parenthood (P:58, P:77-80, I:57) and PROJECTOR:62 correctly states that a `(scope, parent)` pair carries no per-operation or per-item input. But the frozen reader is consumed identically by read, wake and reply (`dynamic-policy.ts:102`), so §2's read-only validation instruction silently widens the parent's clearance to wake and reply. **Flagged — MEDIUM-1.** No narrowing found. I:57 correctly keeps issuance from inferring parent authority from parent presence.

**2. Missing-R risk — concrete path traced end to end.** H4 capture site: `recipient-admission.ts:112,137` (key+channelId only) → authorize `:141–148` re-looks-up via `replyChannel`/`:125`. AF binding/send: `reply-routing.ts:135,137,140` (`privateReplyBinding`/`mint`, host `authorize` ANDed with AF `valid`), `:24–26` (`replyBindingCurrent`), `framework.ts:34945–34957` (`scopeRefs` captured-ref pin, `view.current(ref)`), `channel-registry.ts:5993` (`automatic_reply_stale`). Registration-view `current`/`project`/epochs: `registration-view.ts:71–94,95–129,134–148`. Validity/replacement: `:80` (`source(record.descriptor.id) !== record`), `:82` (recursive parent), `:99/:103` (fresh ref per acceptance), `inbound-admission.ts:112–131` (epoch rebind). **Conclusion: the H4 capture claim is CORRECT; the derived "open gap" conclusion is INCORRECT as scoped — AF already supplies generation/epoch invalidation and original-ref pinning on this path.** The drafts propose **no** duplicate generation mechanism — I:69 explicitly forbids one — so there is nothing to reject on that axis; the correction needed is reclassification, not removal. **HIGH + MEDIUM-2.**

**3. Native facts and attestation provenance.** Clean. No native fact is sourced from a descriptor or ACK self-assertion: P:44 ("detached descriptor is only the evidence carrier/lookup claim"), P:47, I:41 ("a stub `verified:true` flag or self-attesting brand must never replace them"), I:51 ("TypeScript brand does not" authenticate), U:3. Unknown inputs stay unknown — N1-N9, N13 all record "NOT SUPPLIED" with a named supplier and a fail-closed default, and U:37 refuses to assert any SHA/payload/test result for them. The NEW issuer proposal is consistently fenced as a proposal (I:33,39,60) and never cited as evidence that an issuer exists. **No self-attestation and no invented value found** — a value scan over all four briefs returned only fixture-derived `mailbox/*` and `sample-*` identifiers, all byte-matching the frozen fixture; zero URLs, endpoints, credentials or native tuples.

**4. Locators.** §3a kept distinct from the committed doc locators (P:23; IV:74,101) — verified: no literal §3a in `dynamic-selector-core.md`, `## 3a.` at `T2CORE-BRIEF-v4.3.md:176`. Row erratum preserved verbatim including the undercount wording (P:27). All **8** projector rows named exactly against the frozen table (`A:34,35,43,51,52,65,74,76`) and against the erratum's own list. **Pass.**

**5. Cite accuracy — 38 spot-verified** (target ≥15), each listed in Verification Gates below. 35 exact, 3 over-wide (LOW), 0 wrong.

**6. Non-goals.** All six hold. No self-attestation (check 3). No new grants or wire authority — P:120, I:118, and I:33/I:79 decline to choose any factory name, wire payload spelling or new `RefusalCode`; the refusal table reuses only existing AF/H names. No real adapter calls — IV:93-95 records read-only git/hash/`nl`/`grep` plus stdlib content assertions, no node/npm/tsc/test/build/install/network, which is consistent with the clean worktree and zero repo writes. No production claims — P:116-118, I:112, U:39 keep the production barrier and I6 qualification explicitly unmet, and REPORT.md:28 disclaims any gate receipt or inherited PASS.

---

## Verification Gates

**No dispatch gate was named for this review and none was run.** This is a read-only document review; no build, test, compiler, package manager, adapter, API or network call was made, and no gate receipt is claimed. Evidence is `git show`/`git grep`/`git cat-file`/`git merge-base`/`sha256sum`/`nl`/`sed`/`awk`.

Input integrity — all reproduced:
- `sha256sum -c SHA256SUMS` in the drop → rc=0, 4/4 OK; `REPORT.md` and `T2CORE-BRIEF-v4.3.md` match the dispatch brief.
- All 18 host/AF/ledger source-byte hashes and line counts in `INPUT-VERIFICATION.md:36–53,61–63,68` recomputed → **all match**.
- All 9 external document hashes in `INPUT-VERIFICATION.md:19–28` recomputed → **all match**.
- `sha256sum -c SHA256SUMS` in `/srv/review-drop/af-a0970645-view/` → 2/2 OK, and both files equal the `git show` bytes.
- Drafts in the reviewed evidence dir are byte-identical to the drop.

Repo state (acceptance "no repo commits") — **met**: `HEAD` = `f378eadf`, `git status --porcelain=v1` empty, `task-xn4yj-impl` = `f378eadf`, `git reflog` shows no commit. Independently confirms the operator record of 2026-10-31 10:14.

The 38 cites spot-verified (object:path:lines → result):

*T2 core `f378eadf`* — `docs/design/dynamic-selector-core.md`: 3–4 ✓ · 15–23 ✓ · 25–77 ✓ (all 49 rows, 8 projector) · 50 ✓ · 53,63–64 ✓ · 79–85 ✓ · 98–103 ✓ · 105–110 ✓ · 114 ✓. `test/stubs/f-host-t2/v1/dynamic-policy.ts`: 1–4 ✓ · 11–19 ✓ · 20 ✓ · 21–26 ✓ · 27–34 ✓ · 64–67 ✓ · 81–106 ✓ · 87–100 ✓ · 102,105–106 ✓. `…/dynamic-policy.test.ts`: 168–174 ✓ · 180–187 ✓ · 204–237 ✓. `…/typecheck.ts`: 9–11 ✓ · 9–36 ✓. `…/view-contract.ts`: 1–5 ✓ · 1–8 ✓ · 1–23 ✓. `…/doubles.ts`: 1–3 ✓ · 19–32 ✓. `test/stubs/f-host-3/v1/a-contract.ts`: 173–174 ⚠ over-wide. `test/fixtures/generic-recipient/scope-cases.json`: 4–87 ✓ · 395–418 ✓ · 421–447 ✓ · 642–666 ✓ · 856–880 ✓ · 882–905 ✓ · 908–934 ✓ · 1267–1290 ✓ · 1603–1625 ✓ · 1667–1692 ✓. `docs/design/generic-recipient-host.md`: 476–478 ✓ · 479–481 ✓.

*H `aecd4a8c`* — `src/recipient-scope.ts`: 1–7 ✓ · 6–7 ✓. `src/recipient-admission.ts`: 8 ✓ · 24–33 ✓ · 24–42 ✓ · 35–42 ⚠ over-wide (vs :39–42 ✓) · 86–95 ✓ · 110–148 ✓ · 130–149 ✓ · 161 ✓ · 168–170 ✓ · 171–199 ✓. `src/recipient-policy.ts`: 3–13 ✓. `src/recipient-presentation.ts`: 10–23 ✓. *H `c353e1c8`* — `src/recipient-scope.ts` identical declaration ✓ (same blob hash).

*T1 `bff8c46e`* — `docs/design/generic-recipient-scope-contract.md`: 94–100 ✓ · 94–138 ✓ · 102–123 ✓ · 106–121,129–138 ✓ · 149–163 ✓ · 151–163 ✓ · 151–173 ✓ · 158–163 ✓ · 229–233 ✓ · 237–261 ✓ · 251–261 ✓ · 311–334 ✓ · 324–334 ✓ · 336–342 ✓ · 370–379 ✓ · 410–412 ✓. `test/fixtures/…/scope-cases.json` byte-identical to T ✓.

*AF `a0970645`* — `src/mcpl/registration-view.ts`: 6–7 ✓ · 8–9 ✓ · 10–11 ✓ · 12–21 ✓ · 13–21 ✓ · 22–25 ✓ · 26–32 ✓ · 63–94 ✓ · 77–82 ✓ · 95–129 ✓ · 112–128 ✓ · 113–127 ✓ · 134–148 ✓ · 138–148 ✓. `src/mcpl/inbound-admission.ts`: 10 ✓ · 11–15 ⚠ over-wide (union is 11–13) · 22–30 ✓ · 34–35 ✓ · 112–131 ✓ · 121–140 ✓ · 161–169 ✓ · 177–197 ✓. `docs/registration-scope-view.md`: 37–48 ✓ · 50–60 ✓ · 62–81 ✓.

*Reviewer-added AF cites, absent from all three drafts* — `src/mcpl/reply-routing.ts:17–21,24–26,63–71,135–140`; `src/mcpl/channel-registry.ts:5993`; `src/framework.ts:34945–34957`; `git ls-tree a0970645:src/mcpl` (40+ files vs 3 read).

*Ledger* — `b0bf960e:.orgasmic/tasks/TASK-YXASE/journal.org:83–118` ✓ (tx `e9dfd0e3`, 8 rows, 21/9/8/10/1, 19/11/19).

---

## Unmet criteria / test gaps

Task acceptance, independently checked:

| Criterion | Status |
| --- | --- |
| Two brief drafts in the evidence dir | **Met** — plus `UNKNOWN-INPUTS.md`, `INPUT-VERIFICATION.md`, `SHA256SUMS` |
| No repo commits | **Met** — HEAD/status/branch/reflog all verified clean |
| Every cite verified against a git object | **Met** — 38/38 resolved; 3 benignly over-wide, 0 wrong |
| Explicit list of unknown inputs | **Met** — N1–N13 + owner-6/7 separation; N12's first clause needs reclassification per HIGH |
| No self-attestation, no new grants/wire authority, no real adapter calls | **Met** |

Test-specification gaps (all future specs; nothing was executed, correctly):

1. **`ISSUANCE:106` passes without exercising its criterion.** Against real AF it is satisfied by `automatic_reply_stale` (`channel-registry.ts:5993`), not by host-side R pinning. Must stub AF's `valid` to isolate the host layer. **HIGH.**
2. **No wake/reply parent-conjunct coverage.** All eight §5 rows and the §6 mutants are read-side; nothing asserts the parent against the wake or reply selector, and C6 already yields `true` for all three operations from one fact. **MEDIUM-1.**
3. **F2 remains unconvertible** under frozen pure-subset semantics (`dynamic-policy.test.ts:168–174`). Correctly carried as BLOCKED by P:111 and U:38 — a disclosed gap, not a defect.
4. **Every positive is synthetic.** No row can rise above "projector logic with invented doubles"; §7:116's proof-level ladder states this correctly, but no row reaches authenticated adapter evidence, composed AF lifecycle or qualified I6.
5. **No AF-composed probe is possible** from the 3 transcribed AF objects; cross-repo nominal-type compatibility stays compile-only and one-way (`view-contract.ts:1–8`).

---

## Residual risk

1. **Highest risk is under-read AF surface, not cite error.** The one materially wrong conclusion came from reading 3 of 40+ AF `src/mcpl` files while scoping the absence claim to "these frozen source trees" (MEDIUM-2). Other AF-side claims in §4/§5 rest on the same three objects and may carry the same defect; `registration-ingress.ts` (imported at `registration-view.ts:3`) is unread.
2. **Blocker-set inflation.** HIGH and the LOW cite slips both push toward over-stating what is missing. For a brief whose purpose is to bound future engineering, over-stating blockers wastes a reviewed-contract cycle; it is the mirror image of, and safer than, under-stating them, but it is still wrong.
3. **The frozen T2/T1 mismatch is structural, not a drafting slip.** `dynamic-selector-core.md:81` scopes the projector to the parent's *read* selector while `S:158–163,170–172` requires per-operation recursion. No wording fixes this; MEDIUM-1 only makes the consequence explicit and uncovered. Closing it needs the reviewed per-operation interface already named in N11.
4. **Synthetic-positive risk.** If the briefs are implemented as written, every positive witness is a cooperative double (`doubles.ts:1–3`). The drafts are disciplined about saying so, but a downstream reader skimming §7 could read "all eight IDs" as coverage. The proof-level ladder should stay attached to every future status report.
5. **Unreviewed proposals remain unreviewed.** The PROJECTOR native-fact reader (P:47) and the ISSUANCE private binding contract (I:33) are both NEW. Neither is cleared here; both need a closed edit scope and qualifier go before any `src/` work.
6. **Not checked by me:** the Slack qualifier timestamps (dispatch context, not network-authenticated — U:104 says the same), and whether `EM8Q2-DAG-v2.md` / `TASK-R5BF7-H-BRIEF.md` content is consistent with the drafts (hash-verified only; they are planning context, not frozen contracts).

---

## Fix directions

Ordered. Each is a drafting change; none authorizes implementation.

1. **ISSUANCE §4 inv.6, §6, §7 and UNKNOWN-INPUTS N12** — reclassify the original-R requirement from open blocker to defence-in-depth. Add `framework.ts:34946,34954–34956`, `reply-routing.ts:137`, `channel-registry.ts:5993` and `AF docs:78–81`; state the `registrationScope`/`replyAuthority` gating conditions; rewrite the ISSUANCE:106 test to stub AF's `valid` so it isolates the host closure. Keep I:69's refusal of a duplicate generation mechanism and restate its justification as "AF R/current already is the lifetime test".
2. **PROJECTOR §2, §4, §5, §7** — scope the validator to the parent's read selector per `dynamic-selector-core.md:81`; replace the contradictory pair at :62 with the explicit consequence that sub-conversation wake/reply admits carry an unqualified parent conjunct and are **uncovered**; add that to acceptance; annotate C6's wake/reply `true` as a shared-reader artifact.
3. **ISSUANCE §1:9** — rebound the absence claim to the objects actually read (list them), or read AF's reply/publication path before asserting AF-side gaps. Then re-audit the remaining §4/§5 AF claims against the broader tree.
4. **Cite hygiene** — `ISSUANCE:79` → `inbound-admission.ts:11–13`; reconcile `TrustedRecipientRead` to `:39–42` in both briefs; `PROJECTOR:45` → `a-contract.ts:174` for `ConnectionKey`.
5. **Leave unchanged** — the row accounting and erratum handling (§2), the §3a locator split, the eight-row truth table and its two-conjunct/agreement-only distinction, the C1–C6 table, the F2 BLOCKED classification, the refusal-name table's layer discipline, N1–N11/N13, the owner-6/7 separation, and every non-goal. These are the strongest parts of the drafts and should survive revision intact.

**This review is not a gate PASS. No implementation is cleared. No owner decision is made or implied.**

---

## Explicit UNKNOWN list

Unknown after this review — not asserted, not invented:

1. **Native root identity and child→root mapping** (N1) — no connector grammar or root field is frozen; no real tuple values exist.
2. **Native parent-relationship provenance** (N2) — no authenticated verifier; no `parentVerified` runtime flag exists or may be invented.
3. **Native readability / joined membership / DM counterpart / author provenance / account association** (N3).
4. **Real adapter descriptors and the authenticated evidence-payload schema/spelling** (N4) — `RegistrationDescriptor` is a detached `ChannelDescriptor`, not a native authority format.
5. **Scope-producer/schema attestation mechanism and its binding to producer/account/K** (N5) — H's `recipientIdentity` account echo is insufficient.
6. **Qualified I6 composed-release attestation receipt** (N6, N13) — the `dynamic-missing-attestation` row stays uncovered; the AF drop is local source, not a qualified release. `S:336–342` requires a new composed integration SHA/tarball.
7. **Revocation/freshness delivery guarantees and the synchronous withdrawal bridge** (N7).
8. **`mappingGeneration` origin, format, ordering and equality semantics** (N8) — deliberately uninterpreted.
9. **Real native effect/write validation and destination after waits; real refusal and private-notice evidence** (N9).
10. **Trusted fact-reader/issuer bridge from the opaque H scope to accepted AF K/R/view** (N10) — proposed only; no constructor or accessor exists.
11. **Per-operation, per-item recursive ancestor evaluation interface** (N11) — and, specifically, whether sub-conversation wake/reply parent qualification is to be added to the seam or left permanently uncovered (MEDIUM-1 makes the gap explicit but does not resolve it).
12. **Reviewed actual-AF nominal-type import/package boundary** (N12 second clause) — the compile-only transcription is one-way and proves no cross-repo brand identity.
13. **F2 behavioral discriminator** — unavailable under frozen pure-subset semantics; conversion stays BLOCKED.
14. **Whether AF supplies further relevant mechanisms in the 37+ unread `src/mcpl` files** at `a0970645` — unknown to the drafts and only partially probed by me (`reply-routing.ts`, `reply-admission.ts` imports, `channel-registry.ts:5993`, `framework.ts:34945–34960`). `registration-ingress.ts` remains unread by anyone in this chain.
15. **Slack qualifier timestamps 1793441074 / 1793441667** — supplied dispatch context, not independently authenticated. No owner decision is recorded for owner items 6 or 7.
