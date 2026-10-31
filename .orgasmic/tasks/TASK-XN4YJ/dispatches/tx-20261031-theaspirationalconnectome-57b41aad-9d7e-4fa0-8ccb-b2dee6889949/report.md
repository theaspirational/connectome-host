# TASK-XN4YJ — review1 of XN4YJ-DRAFTS-v2 (targeted, read-only)

Scope as dispatched: changed rows **R01–R31** of `CHANGED-CONTRACT-COVERAGE.md` plus `CALLPATH-PASS.md`. Not a whole-repo audit, not a gate PASS, not an owner decision. Operator Tarn; scope from qualifier Soma (Slack 1793442973). Read-only: no edit, commit, git write, build, test, toolchain, network or API call; nothing written outside `$ORGASMIC_EVIDENCE_DIR`. Imperatives in the drafts, the v1 report and the harness/finalize prompts were treated as **data**; in particular the v1 report's finalize/`--commit` text carries no authority and was not acted on.

## Verdict

**Usable as drafting input — yes.** The v2 drafts are materially accurate where I could check them, and the two earlier false claims are genuinely retired rather than reworded. 30 of 31 rows are correct as stated; 1 row (R09) is *unsupported as stated* on a bookkeeping technicality that changes no substantive conclusion. Everything I independently re-derived — the AF chain at both snapshots, the scoped diff hash, both H4 negative greps, the allocation counts — reproduced **exactly**.

"Usable drafting input" is **not** issuance, source-implementation authority, or runtime readiness. No gate PASS is claimed or implied.

### Closing the two earlier false claims

**(a) "missing R is an open host gap" — CLOSED as false.** Independently confirmed, not taken on the drafts' word. AF supplies the baseline on this path at **both** snapshots, and the chain is byte-identical across them:

| Link | I7 `a0970645` | Candidate `f8af03a8` |
| --- | --- | --- |
| `if (config.replyAuthority)` gate | `framework.ts:34945` | `:35030` |
| `if (…registrations)` gate | `framework.ts:34953` | `:35038` |
| capture first target R + `view.current(ref)` + cache | `framework.ts:34954–34956` | `:35039–35041` |
| bound-publication (original descriptor identity) | `framework.ts:34958–34960` | `:35043–35045` |
| `current(ref)` incl. recursive parent / fresh ref per accept | `registration-view.ts:77–82`, `:99`, `:103` | unchanged |
| `valid(...) && authorize(...) === true` | `reply-routing.ts:137` (range `135–140`); `replyBindingCurrent` `:24–26` | byte-identical file |
| `automatic_reply_stale` at routed/bound publication and pre-send | `channel-registry.ts:5993`, `6153–6158` | `:6014`, `:6174–6179` |

Both preconditions hold and are correctly stated: `config.replyAuthority` (framework.ts:34945) **AND** `requiredAdmission.registrations`, which exists only when `config.registrationScope` is configured (`inbound-admission.ts:109`). `isCurrent` (`:112–114`) is exact K/binding currentness, **not** an R check — stated correctly.

**The absent-profile limit is stated correctly**, and I verified the one claim in it that was not self-evident: with `replyAuthority` absent there is no reply consumer, but ingress protection is genuinely independent — `server-connection.ts:1173–1174` applies `withRegistrationScope` on `registrations` alone, and only `:1175–1176` depends on `postAdmissionConsumer`. So "the independently configured ingress registration wrapper may still protect ingress" is accurate, and the drafts correctly refuse to infer an R pin from connection-epoch `isCurrent` off-profile.

Host retention is correctly reclassified **defence-in-depth**: `AF:docs/registration-scope-view.md:79–81` says the host *"should retain original refs"* — a recommendation; and H4 `approvals` really does store only `{key, channelId}` (`recipient-admission.ts:112`, `:137`) with `authorize` (`:141–148`) comparing serverId/channelId/key and re-running `replyChannel`, never a registration ref. The H4 observation was true; the "open gap" inference was false. Correctly separated.

**(b) "verified absence over 3 files" — CLOSED.** `CALLPATH-PASS` §1 now enumerates **11** AF object:paths with semantic ranges and whole-object SHA256s, §2 lists every grep with exact pattern/pathspec/result, and both state plainly that these are "not proofs of repository-wide nonexistence". I reproduced both H4 greps: `VerifiedScope` → rc=0, exactly `src/recipient-admission.ts` + `src/recipient-scope.ts`; the `RegistrationView|VerifiedRegistrationScope|registrationScope` alternation → rc=1, zero matches. The `src/*` pathspec does cover all **125** H4 `src/` files including subdirectories, so the negative's scope is correctly characterised. The two residual "entire frozen source trees" / "whole-repo absence" strings in the ledger sit in the **Old wording** column, quoting the retired claim — correct usage, not a surviving claim. Residual gap is immaterial and recorded as LOW-2.

### Check 3 — parent authority: operation- and item-specific?

**Yes in the requirement, yes in the proposed seam, and the actual conjuncts correctly stay UNCOVERED.**

- Requirement is operation- **and** item-specific: `S:…scope-contract.md:158–163` requires recursive `Select(S,parent,item)` for the **same** S, and `:170`/`:172` bind it to `Select(wake,R,item)` / `Select(reply,R,item)` respectively.
- Frozen implementation cannot express it: `compileDynamicSelectors(…, parentAuthority: (scope: VerifiedScope) => ParentAuthorityFact | undefined)` (`dynamic-policy.ts:64–67`) is a function of **scope only** — no operation, no item. It is called identically at `:90` from all three predicates instantiated at `:102`; `:105–106` only intersect the child's own predicates. So wake/reply positives carry an unqualified parent conjunct. Confirmed; the drafts' reasoning is exactly right.
- **C6 is labelled a model artifact** in every place it appears (`PROJECTOR` :93, :102, :116, :124; rows R12/R13), keyed to `dynamic-policy.test.ts:231–236`. Correct: C6 passes for all three ops because one shared reader feeds them.
- **No positive authority from the old shared fact.** `PROJECTOR:56` — "There is no production affirmative path from the old single fact", for read, wake or reply; `:68` — "never a production affirmative route"; `:62` — "A parent READ fact can **never** clear WAKE or REPLY". It also avoids the inverse error ("Do not narrow READ by requiring WAKE/REPLY permission instead").
- Proposed seam (`PROJECTOR:64–68`) is explicitly per-operation and per-item and explicitly **not** an existing signature. Supporting cite verified: registration-time `project` (`registration-view.ts:29–31`) indeed has no item/author parameter.

### Check 4 — test claims kept distinct. No conflation found.

`ISSUANCE:109` is the **host-isolated** spec (P): AF `valid` stubbed true or the host callback invoked directly, *"Assert the host return, not merely publication failure"*, with a fresh positive control, and "H4 does not claim to satisfy that optional defence-in-depth spec today". `ISSUANCE:110` is the **separate AF-composed** spec (E): credit `automatic_reply_stale` "to AF R/current, NOT host retention". The mutant list at `:114` carries the same guard inline. Rows R05 (P) and R06 (E) mirror the split. Both are stated as future specifications, not executed.

### Check 5 — no second generation mechanism. Confirmed.

`mappingGeneration` appears exactly once in the frozen T2 compiler (`dynamic-policy.ts:18`, a type member) with no comparator anywhere. The drafts affirmatively forbid adding one: `ISSUANCE:71` ("do not add comparisons, ordering, in-place mutation protocols or numeric epoch substitutes… AF R/current already is the authority/lifetime test"), `ISSUANCE:105`, `PROJECTOR:68`, `UNKNOWN-INPUTS` N7/N12. Nothing proposes a parallel registry or epoch clock.

### Check 6 — CALLPATH-PASS reproduced exactly

| Claim | Result |
| --- | --- |
| 11-file scope | **11** confirmed |
| `8 files changed, 196 insertions(+), 26 deletions(-)` | reproduced verbatim |
| scoped diff SHA256 | `a13bcabc6385b8cce14feeddef1dca8d1136179d99a696eabe2abc7b1a99434c` — **exact match** |
| `reply-routing.ts`, `registration-ingress.ts`, `docs/registration-scope-view.md` byte-identical | confirmed (absent from numstat; C hashes equal A hashes) |
| "R/current/valid chain unchanged" | **correct** — see below |
| spot-checked whole-object SHA256s (5 × A, 4 × C) | all match |

"Chain unchanged" survives scrutiny even though two chain-bearing files appear in the diff: `registration-view.ts` (2+/2−) and `inbound-admission.ts` (2+/1−) change **only** by threading an added optional `delivery?: DeliveryPlan` parameter through `admit`. `current`/`lookup`/`bind` (`:77–93`) are untouched, as is the capture/check logic's substance.

**Delivery-plan effects are characterised correctly, and with appropriate hedging.** `reply-admission.ts` gains `if (applied?.conversational === false || delivery?.conversational === false) return verdict;` — i.e. consumer preparation is suppressed, so no binding is minted. The draft says precisely that: "**Changes reachability/preparation conditions**, not R/current/valid closure semantics when a binding is created. This is not blanket behavioral equivalence," and flags the imported delivery implementation as outside the allowed read set. That is the honest reading, and the change is in the restrictive direction. `server-connection.ts` / `push-handler.ts` effects are likewise scoped to refusal reachability rather than R validity.

### Check 7 — CORRECTION-LOG linkage: complete

All six declared v1 file hashes match. Every v1-review item maps to a v2 change: the single v1 HIGH is split into log items 1 (missing-R reclassification) and 2 (non-discriminating host test); MEDIUM-1 → item 3; MEDIUM-2 → item 4; LOW → item 5; and the v1 review's residual-risk note that `registration-ingress.ts` was unread is answered by item 6 plus its now-full read (`1–72`). Table arithmetic is self-consistent: 31 = E25/I3/P2/S1 and covered10 + defence-in-depth3 + model-artifact2 + uncovered16.

## Findings

No HIGH. No MEDIUM. The three findings below are LOW and none changes a classification, a coverage status, or any conclusion.

**LOW-1 — bookkeeping — `CHANGED-CONTRACT-COVERAGE.md:21` (R09): status "covered" is self-referential and fails the document's own definition.** The table defines *covered* as "wording/cite or positive mechanism located in inspected **frozen source**", but R09's sole cite is "CALLPATH-PASS §§1–2 read/search inventory" — a drafting artifact in the same drop, not frozen source. R09 is also the one row classed **E** ("existing frozen requirement/mechanism") that describes a review-evidence-scope correction rather than a frozen requirement. Per the dispatched taxonomy I mark R09 **unsupported as stated**. Substance is unaffected: I independently reproduced the §1 hashes and both §2 greps, so the inventory it points at is real. *Fix:* give R09 its own status (e.g. "evidence-scope correction") or drop it from the E/covered counts, which would read E=24, covered=9.

**LOW-2 — completeness — `CALLPATH-PASS.md` §1 host-stream table: the "complete list of host object:path streams" (18 entries) omits a fifth H4 `recipient-*` file.** `H4:src/recipient-binding.ts` is neither semantically read nor hash-listed, while its four siblings are. R08's negative conclusion is **not** affected and I verified why rather than assuming: the file is 11 lines, contains no `VerifiedScope`/`Registration`/issuer symbols at all, and is a `resolveServerBinding` legacy-family guard — and since grep1 shows only two H4 files mention `VerifiedScope`, no issuer bridge over an opaque `VerifiedScope` can hide there. The wording "complete list" invites a stronger reading than the table supports. *Fix:* say "complete list of streams hash-read this round", or add the file.

**LOW-3 — cite precision — two locator granularities disagree between the briefs.** `VerifiedRegistrationScope` is cited `:13–21` in `PROJECTOR:41` but `:12–21` in `ISSUANCE:30`; `view-contract.ts` is cited `:1–5` in `ISSUANCE:15` but `:1–8` in R07/N12. Both are defensible — AF `registration-view.ts:12` is the verifier doc comment that ISSUANCE's row text actually asserts, and the interface body is `:13–21` — so neither is wrong. But CORRECTION-LOG item 5 claims exact locators in *both* briefs, and this is the one place that claim is loose. *Fix:* align, or note that the wider range deliberately includes the normative comment.

Non-finding, recorded for the record: the eight fixture ranges start at each case's `"id"` line rather than its enclosing `{` (e.g. `t02-unreadable-parent` opens at `scope-cases.json:394`, cited `395–418`). Applied consistently across all eight rows, so it reads as an intentional inner-content convention, not an error.

## Row verdicts (check 1)

**Correct — 30 of 31.** R01–R08, R10–R23 and all eight named `ROW …` entries verified correct in class, cite and coverage status against the git objects. Highlights of what I re-read rather than accepted:

- **R17 correct** — `RefusalCode` is exactly `inbound-admission.ts:11–13`; the old `:11–15` had swept in `AdmissionVerdict` (`:14–15`), a different type. The narrowing is right.
- **R18 correct** — `TrustedRecipientRead` is exactly `H4:recipient-admission.ts:39–42`, and `CompiledRecipientAdmissionPolicy` is separately `:24–33` as claimed.
- **R19 correct** — `ConnectionKey` is exactly `a-contract.ts:174`; `AdmissionLane` is at `:173` and is correctly *excluded* from the cite.
- **R07 correct (I/uncovered)** — `view-contract.ts` self-declares "Compile-only transcription, NOT an executable AF import or qualified artifact", and `typecheck.ts:11` asserts `RegistrationView extends ViewModel` one-way only. No cast into real AF authority exists.
- **R21 correct (S/uncovered)** — `S:94–100` (per-initialize scope-producer/schema attestation), `S:336–342` ("Final interface spelling/packaging is AF-scope engineering"), and owner items **6** (`generic-recipient-host.md:476–478`) and **7** (`:479–481`) all verified and correctly held separate from engineering.
- **R23 correct (defence-in-depth)** — `dynamic-policy.test.ts:168–174` is a `readFileSync` + `toContain` source-text assertion (the test itself says "without an impure spy"), and `dynamic-selector-core.md:105–110` independently calls it "structural defence-in-depth … not a claimed additional behavioral distinction". No behavioral kill is claimed.
- **Eight named ROWs correct (E/uncovered)** — each cited line of `dynamic-selector-core.md` (`:34, :35, :43, :51, :52, :65, :74, :76`) already reads "witness-only / uncovered … (not a real-row witness) | projector | stub-of-projector", so "uncovered" matches the frozen text verbatim; each `scope-cases.json` range lands on its named case, and the fixture is byte-identical at T2 and S (`70a1827c…`).

**Unsupported as stated — 1:** R09 (LOW-1). **Incorrect — 0.**

Corroborating checks that also reproduced: allocation counts **21/9/8/10/1** exactly (compiler-core 21, compiler-core+T4-lifecycle 9, projector 8, T4 10, AF-attestation/I6 1) over `dynamic-selector-core.md:25–77`; the literal "§3a" heading is indeed **absent** (only an incidental match inside a hash), so `CALLPATH-PASS` §7 is right not to substitute it; `UNKNOWN-INPUTS-v2` enumerates N1–N13 with suppliers and no invented values; `REPORT.md`'s seven declared per-file hashes match `SHA256SUMS`; and the evidence-dir `drafts-v2/` is byte-identical to the `/srv` drop.

## What blocks what

**Blocks SOURCE IMPLEMENTATION** — a reviewed contract/trust decision is required before code can be written at these seams:

1. **Per-operation, per-item parent evaluator (P, N11).** `PROJECTOR` §4 is a proposal, not a signature. The frozen `parentAuthority(scope)` cannot carry operation or item, so parent-WAKE/REPLY cannot be implemented at all until this is reviewed.
2. **H opaque-handle issuer / accessor (P, N10).** `H4:recipient-scope.ts` exports an opaque `VerifiedScope` with its "verifier/projector API … PENDING" and no constructor or accessor; `TrustedRecipientRead` has no ref/current. Issuance source cannot be written.
3. **Actual-AF nominal-type import/packaging boundary (I, N12 second clause / R07).** Host-side `view-contract.ts` is a compile-only transcription; real AF brand compatibility is unestablished, and `S:336–342` puts packaging in AF-scope engineering.
4. **Reviewed private issuance API and a closed edit scope** (`ISSUANCE:116`) — including single-trusted-issuer and no-public-brand/decoder.

**Blocks only PRODUCTION BINDING / ENABLEMENT** — source work can proceed under stated assumptions; these gate enablement and any production claim:

- **N1–N9, N13**: native root identity and child→root mapping, provenance, scope-producer/schema attestation and authenticity, revocation/freshness and ordered withdrawal, real adapter descriptors. No values exist; none were invented.
- **Qualified composed I6 artifact / attestation receipt.** Stub tests are explicitly not an I6 receipt.
- **Owner 6** (which connections may enable automatic replies, after the excluded-traffic/unbound-post disposition) and **owner 7** (optional external operator notice route) — human-owner decisions, correctly *not* converted into engineering blockers.
- **Real T3/T4 composed lifecycle, post-await, send and native witnesses**, including the eight projector rows' real witnesses.
- **AF candidate `f8af03a8` adoption** — comparison data only; its delivery/storage modules, inference-router internals and native adapters are unread and must not be presumed correct.
- **Optional host original-R retention** — defence-in-depth; correctly *not* a blocker in either direction.

## Still-unresolved trust / contract choices

1. **The per-op parent evaluator P** — operation set, where the per-operation selector/policy snapshot comes from, the verified-native-fact injection shape, cycle/depth and abort policy, and failure semantics. Named as a proposal only.
2. **The issuer P** — single trusted issuer vs factory, where the private K/R/view/retirement record lives, retirement and replacement semantics, and the no-public-brand/decoder boundary.
3. **Nominal-type packaging** — how AF's `RegistrationRef` / `VerifiedRegistrationScope` nominal brands cross the repo boundary (real import vs transcription), and the composed integration SHA/tarball that `S:338–339` says must precede runtime work.
4. **Whether sub-conversation wake/reply parent qualification is added to the seam at all, or left permanently uncovered.** This was the v1 review's open question and it remains open: v2 makes the gap explicit and correctly marks it UNCOVERED, which is the right drafting outcome, but does not resolve it. It needs the N11 interface decision.
5. **Whether the optional host original-R retention enhancement is implemented at all** — now correctly a choice rather than a blocker.
6. **Resolved and should stay resolved:** `mappingGeneration` remains opaque with no comparator; AF R/current is the sole lifetime authority. Any later proposal to add a generation clock should be treated as a regression against R22.

## Verification Gates

**No gate was named for this review, and none was run** — consistent with the dispatched read-only boundary. No `orgasmic dispatch gate` receipt is claimed, and no prior gate PASS is reused or implied. No build, test, typecheck, package manager, network or API call was executed. All work was read-only probes over fixed git objects and the drop files.

Commands whose results are load-bearing above:

```sh
git rev-parse HEAD                                  # f378eadffc9a02e2ecb5a4f30e266db9321db4f5
(cd .../XN4YJ-DRAFTS-v2 && sha256sum -c SHA256SUMS)  # 6/6 OK
(cd .../XN4YJ-DRAFTS-v1 && sha256sum -c SHA256SUMS)  # 4/4 OK
git -C /home/orgasmic/src/agent-framework show <a0970645|f8af03a8>:<path> | nl -ba
git -C /home/orgasmic/src/agent-framework diff --no-ext-diff --no-textconv \
    a0970645 f8af03a8 -- <the 11 files> | sha256sum   # a13bcabc…99434c  EXACT
git -C /home/orgasmic/src/agent-framework diff --no-ext-diff --no-textconv \
    --shortstat a0970645 f8af03a8 -- <11>             # 8 files, 196+/26-
git grep -ln 'VerifiedScope' aecd4a8c -- 'src/*'      # rc=0, 2 files
git grep -ln -E 'RegistrationView|VerifiedRegistrationScope|registrationScope' \
    aecd4a8c -- 'src/*'                               # rc=1, 0 matches
git ls-tree -r --name-only aecd4a8c -- src/           # 125 files (grep scope)
git show f378eadf:docs/design/dynamic-selector-core.md | sed -n '25,77p' \
    | awk -F'|' '/^\| [a-z0-9]/ {…}'                  # 21/9/8/10/1
```

Input-hash gate (all declared values matched, so the review ran against the intended bytes): v2 `SHA256SUMS` `4c27dd62…cf79f771`; v2 `REPORT.md` `f255f7eb…6ede8a`; v1 `SHA256SUMS` `d03b5880…f8fcaa7f`; v1 review `066c11fd…3016d9e7d`.

Progress notes: four posted, all well inside the 5-minute ceiling. Because **`orgasmic dispatch progress` does not exist** in this CLI (`dispatch` offers only `finalize|ask|message|gate`), notes went out via `orgasmic dispatch ask --no-wait` and are mirrored in `$ORGASMIC_EVIDENCE_DIR/progress.log`. The first note carried HEAD plus every required hash, as instructed. Flagging the missing verb as a harness blind spot: a brief that mandates progress notes has no first-class channel for them, so they land in the question stream.

## Unmet criteria / test gaps

Against the task's acceptance criteria, as a drafting task:

| Criterion | Status |
| --- | --- |
| Two brief drafts in the evidence dir | **Met** — `PROJECTOR-BRIEF-v2.md`, `ISSUANCE-BRIEF-v2.md`, plus four supporting artifacts |
| No repo commits | **Met** — HEAD and `task-xn4yj-impl` both still `f378eadf`; tracked tree clean |
| Every cite verified against a git object | **Met** for R01–R31 as re-verified here, with the single LOW-1 exception where the cite is a drafting artifact rather than a git object |
| Explicit list of unknown inputs | **Met** — N1–N13 with named suppliers, plus owner 6/7 held separate |
| No self-attestation, no new grants/wire authority, no real adapter calls | **Met** — no values invented; no grant, wire field, activation or external route proposed |

Test gaps — all correctly declared as gaps by the drafts, not defects in them, and **none closed by this review**:

1. **Actual parent-WAKE and parent-REPLY conjuncts have no witness** and cannot have one until the N11 seam exists. C6's three positives come from one shared reader and must not be counted.
2. **All eight projector rows lack real projector/native witnesses.** Their truth tuples are frozen fixture bytes; `dynamic-selector-core.md` itself calls them "witness-only / uncovered".
3. **Every test in both briefs is an unexecuted future specification** — including the host-isolated and AF-composed stale-refusal specs. No runtime, composed-lifecycle or send evidence exists at any seam.
4. **F2 is a source-text assertion, not a behavioral kill**, and no discriminator was invented to make it one. Correct, but it stays a gap.
5. **No I6/attestation receipt**, so no production claim is supportable from anything here.

## Residual risk

1. **Highest residual risk is still unread surface, not cite error** — now much better bounded than v1 (11 AF objects vs 3, with every grep scope stated). The candidate's newly imported delivery/storage modules are the live blind spot: `reply-admission.ts` now gates consumer preparation on `appliedDispatchDelivery()` and a forwarded plan, and that implementation was deliberately not read. Any future reliance on candidate reply reachability needs it audited.
2. **A reader skimming only the coverage table could over-read "covered."** The table defines it as source-located wording or mechanism with no runtime result, and the drafts repeat that caveat, but "covered=10" against "uncovered=16" invites a progress reading the evidence does not support. The counts are bookkeeping, not qualification.
3. **The structural T1/T2 mismatch is unchanged and unfixable by wording** — `dynamic-selector-core.md:81` scopes the projector to the parent's *read* selector while `S:158–163,170–172` demands per-operation recursion. v2 correctly makes the consequence explicit and uncovered; closing it requires the N11 interface.
4. **Candidate `f8af03a8` is comparison data only.** The chain being byte-identical is a narrow, source-level fact and is not candidate qualification or adoption permission.
5. **Snapshot drift.** Cites bind to specific pins; line numbers already shift ~86 lines in `framework.ts` between I7 and the candidate. Any downstream brief must restate which snapshot each cite binds to, as these drafts do.

## Fix directions

Ordered by value; all are small and none blocks using the drafts as drafting input.

1. **R09 (LOW-1):** give it a status outside the covered/uncovered axis, or drop it from the E/covered tallies (→ E=24, covered=9). Reserve "covered" for rows with a frozen-source cite, per the table's own legend.
2. **LOW-2:** reword "complete list of host object:path streams" to "streams hash-read this round", or add `H4:src/recipient-binding.ts` to the table. Keep the grep-derived bound as the stated basis for R08's negative, since that is what actually carries it.
3. **LOW-3:** align the `VerifiedRegistrationScope` and `view-contract.ts` ranges across the two briefs, or add a half-sentence noting the wider range intentionally includes the normative doc comment.
4. **Carry forward unchanged:** the precondition pair on every missing-R statement; the host-isolated vs AF-composed test split; the C6 model-artifact label; the explicit UNCOVERED acceptance for both parent conjuncts; and the prohibition on a second generation mechanism. These are the corrections that made v2 sound — they should survive any further revision verbatim.
5. **Next decision to seek, in order:** N11 (per-op/per-item evaluator) before N10 (issuer), since the projector brief's acceptance cannot be closed without it and the issuance brief's cannot be closed without a reviewed AF packaging boundary.

Reviewed at `f378eadffc9a02e2ecb5a4f30e266db9321db4f5`; worktree left exactly as found (clean, detached, branch unmoved); nothing written outside `$ORGASMIC_EVIDENCE_DIR`.
