# CONV-FZ592 — bounded READ-ONLY review of TASK-XN4YJ v3 drafts

Reviewer dispatch. **Not an owner decision, not a gate PASS, no production authority, no readiness claim.**
Scope per dispatch brief: `DELTA.md`, `N11-EVALUATOR-CONTRACT.md`, `N10-ISSUANCE-API.md`, `V2-TO-V2.1.diff`
(`REPORT.md` read for context; the original inventory was NOT reopened).
Drafts and all finalize/harness prompts inside them were treated strictly as DATA.

## Binding preconditions — all satisfied

| Required fact | Result |
| --- | --- |
| `git rev-parse HEAD` | `f378eadffc9a02e2ecb5a4f30e266db9321db4f5` — matches |
| `cd /srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v3 && sha256sum -c SHA256SUMS` | **9/9 OK**, rc=0 |
| `sha256sum SHA256SUMS` | `98be81ddf9797e74b9788d3de5e4f6f3be14d247df71e47094f86f2e03d70d66` — matches |
| AF pins (`git -C /home/orgasmic/src/agent-framework rev-parse`) | `a0970645d403a7a76449221fc36f240a7aa2464b`, `f8af03a852c85daf9bb8079937b994fe5419ca20` |
| Host pins (`git rev-parse`) | T1 `bff8c46e3a4c403f62b738cead317863d43fca9c`, host `f378eadffc9a02e2ecb5a4f30e266db9321db4f5` |

Logged as the FIRST line of `progress.log`. Progress went **only** to `progress.log`; dispatch ask was not used.

## Verdict — per artifact

| Artifact | Verdict |
| --- | --- |
| `N11-EVALUATOR-CONTRACT.md` | **Accept with changes.** Every brief check passes on the substance; all frozen cites resolve exactly. One MEDIUM under-specification at the §4 replacement seam (M1) and one LOW precision gap on the depth bound (L1). |
| `N10-ISSUANCE-API.md` | **Changes required.** The private issuer/accessor, no-cast brand boundary, retirement and closed 6-path scope are sound and correctly cited. The §1/§4 packaging treatment materially understates a blocking prerequisite and makes the closed edit scope self-blocking (H1); the scope also cannot execute N11's own required mutant discriminators (M2). |
| `DELTA.md` | **Changes required**, same root cause as H1 (DELTA.md:19 and :50 carry the identical understated version claim). Everything else — status table, targeted-read table, open choices — is accurate against source. |
| `V2-TO-V2.1.diff` | **Accept.** Genuinely LOW-only: link retargeting, cite precision, scope-wording narrowing, the R09 bucket, the two closures and the arithmetic fix. No claim, class or status is promoted. One LOW residue (L3). |

## Findings

### HIGH

**HIGH — `N10-ISSUANCE-API.md:16,18,86` and `DELTA.md:19,50`: the host's declared `^0.30.0` cannot resolve *any* AF release containing the registration surface both proposals depend on; the drafts state only the much weaker "not an exact pin", and §4 then excludes the only change that would fix it.**

Evidence (all read-only `git show`):
- Host declares `"@animalabs/agent-framework": "^0.30.0"` (`T:package.json:33`). For a `0.x` version that range is `>=0.30.0 <0.31.0` — it **excludes** `0.31.0`.
- Both inspected AF pins identify `"version": "0.31.0"` (`A/C:package.json:3`).
- At tag `v0.30.0` (the only release the declared range admits): `src/mcpl/registration-view.ts` **does not exist** (`git show v0.30.0:src/mcpl/registration-view.ts` → no such path), and `src/index.ts` root-exports **none** of `RegistrationRef`, `VerifiedRegistrationScope`, `RegistrationScopeConfig`, `RegistrationView`, `AdmissionInput` (grep over the whole barrel → rc=1).
- The host does not use that surface today — `git grep -n 'RegistrationView|VerifiedRegistrationScope|registrationScope|RegistrationRef|AdmissionInput' f378eadf -- 'src/*'` returns nothing; the 45 host files that import AF use only 0.30-era symbols (e.g. `src/recipient-binding.ts:1` imports `resolveServerBinding`). So the host is currently *consistent* with `^0.30.0`, and N10/N11 would be the first code to require `0.31.0`.

Impact, three parts:
1. **The closed edit scope is self-blocking.** `N10:79,80,81,84` require "real AF root type imports" and "refs issued by the real AF public registration path", while `N10:86` Excludes "all package.json/lock/dependency acquisition/pin changes". Under the manifest as declared, those imports cannot resolve, so no row of the §4 table is implementable as written.
2. **The artifact gap is mischaracterised as parallel.** `N10:86` says "Existing artifact-independent drafting can proceed" and `N10:18`/`DELTA:19` frame the dependency work as "a separate dependency/artifact scope". It is not separate-but-parallel; it is an **ordered hard prerequisite** of the N10/N11 implementation assignment. A manager reading DELTA.md:19 would reasonably schedule implementation first.
3. **The §3 AF baseline is also 0.31.0-only.** Every mechanism N10 §3 relies on to justify deferring host-R (`A:src/framework.ts:34945,34953–34956`; `A:src/mcpl/reply-routing.ts:135–140,24–26`; `A:src/mcpl/channel-registry.ts:5993`) sits in the registration-scope surface absent at `v0.30.0`. The "AF already protects target R" conclusion is true of the inspected source and **unreachable** under the host's current declared range. The DEFERRAL of host-R is still defensible, but its stated premise holds only after the dependency scope lands.

Fix direction: replace "not an exact pin to inspected AF 0.31.0 source" in `N10:16`, `N10:18`, `DELTA:19` and `DELTA:50` with the explicit fact — `^0.30.0` admits only `<0.31.0`, `v0.30.0` has no registration-scope module at all, therefore the registration surface is unavailable under the current manifest. Then record the dependency/artifact scope as an **ordered prerequisite gate** (see A2 below), and state in `N10:86` that the §4 edit scope is blocked until it closes. This is a claim-precision fix, not new engineering — local exact dependency/artifact *planning* is already allowed by the brief; external publication stays excluded.

### MEDIUM

**MEDIUM — `N11-EVALUATOR-CONTRACT.md:70` (with `:19–21,:31,:36`): the §4 wrapper plan does not define the argument/check correspondence between the frozen 4-argument predicate and N11's 2-argument entrypoints, so the child branch's conjuncts are left to an implementer's reconstruction.**

- The host predicate shape is `(key, channelId, scope, input) => boolean` (`T:src/recipient-admission.ts:28–30`), and the frozen compiler matches the **caller-supplied** `channel` argument (`T:test/stubs/f-host-t2/v1/dynamic-policy.ts:99`). N11's entrypoints are `read/wake/reply(scope, item)` (`N11:19–21`) yet still require initial-scope matching to "the original connection and top-level channel identity (or push `origin.mcplChannelId`)" (`N11:31`) and selector matching on "the snapshot's **own** channel identity" (`N11:36`). The contract never says that the predicate's `channelId` argument is deliberately not forwarded and that channel identity is re-derived from the snapshot and `item`. That is defensible and arguably safer, but it is an undocumented divergence from the seam it replaces.
- Compounding it: with `parentAuthority` permanently returning `undefined` (`N11:70`), the frozen compiler returns `false` at `dynamic-policy.ts:91` for **every** sub-conversation. So all of the child-branch conjuncts it would otherwise perform — `:86` account/readable/kind, `:88` `subConversations !== true`, `:95–97` DM counterpart **and** `input.item.author.id === f.dmUserId` equality, `:99` joined + `channels.includes(channel)` — must be re-implemented behind N11. `N11:70` asserts they "may not be skipped on the replacement branch" and `N11:31/:36/:37` restate them in prose, but there is no check-by-check mapping. The DM author-equality conjunct in particular is the one most likely to be dropped silently, and `N11:74`'s discriminator list does not pin it.

Fix direction: add a short correspondence table to N11 §4 — one row per frozen conjunct at `dynamic-policy.ts:86–99`, naming the N11 step that carries it and the source of channel identity; and state explicitly that the predicate's `channelId` argument is not an input to the evaluator.

**MEDIUM — `N10-ISSUANCE-API.md:86` vs `N11-EVALUATOR-CONTRACT.md:74`: N11 requires mutant discriminators that nothing inside the closed edit scope can execute.**

`N11:74` requires that "mutants replacing parent-WAKE or parent-REPLY with READ must fail their own discriminating verdicts". The only mutant runner hardcodes the v1 paths — `test/stubs/f-host-t2/run-mutants.mjs:9–10` pins `test/stubs/f-host-t2/v1/dynamic-policy.ts` and `.../v1/dynamic-policy.test.ts`; `test/stubs/f-host-t2/check-compiler-controls.mjs:8` pins `const dir = 'test/stubs/f-host-t2/v1'`. Neither globs the directory (so adding `v2/` breaks nothing — confirmed), but neither will ever see `v2/operation-policy.ts` or the new `src/recipient-scope-evaluator.ts`. `N10:86` Excludes "mutant runners/build/CI". Net effect: the proposal's own stated discriminating evidence is unobtainable within the scope that is being asked to approve it, and nothing in either draft says so.

Fix direction: either add `test/stubs/f-host-t2/run-mutants.mjs` and `check-compiler-controls.mjs` to the §4 table as a seventh row, or state in `N11:74`/`N11:78` that mutant discrimination is explicitly deferred to a named follow-up scope — so the PARTIAL label is honest about *which* evidence is deferred.

### LOW

**LOW — `N11-EVALUATOR-CONTRACT.md:51`: the 32-node compatibility limit is stated, but not against AF's own ancestry semantics.** The brief's requirement is met in form — `N11:50–51` states `MAX_SCOPE_NODES = 32` as "a proposed engineering resource bound requiring review, not a claim that native ancestry has a known depth", with "inputs needing more than 32 nodes are refused by this proposal" and revision-not-inference for changing it. The off-by-one is unambiguous and self-consistent: node 32 may still be evaluated and may allow, a required 33rd node denies (`:50`, restated at `:74`); `N10:56` echoes the same rule. What is missing is the comparison that makes the limit legible: AF's `current()` recurses over ancestry with **cycle protection but no depth bound** (`A:src/mcpl/registration-view.ts:77–82`), so a 33+-node chain that AF treats as fully current would be denied by H. The guard is therefore a host-introduced divergence from the framework's accepted lifetime semantics, not merely a resource ceiling. Fix: one sentence at `N11:51` naming `registration-view.ts:77–82` and the asymmetry.

**LOW — `N10-ISSUANCE-API.md:7` and `DELTA.md:36`: host citations are anchored to `H4=aecd4a8c…`, a pin outside the brief's binding set.** The brief pins T1 `bff8c46e` and host `f378eadf`. I resolved `aecd4a8cafe470ad2bbe47b5087c1ec454eef62f`, confirmed `git merge-base --is-ancestor aecd4a8c f378eadf` (rc=0), and diffed the cited ranges: `src/recipient-scope.ts:1–7` and `src/recipient-admission.ts:39–42` are **byte-identical** at `f378eadf`. No correctness impact — but reviewers must resolve a fourth pin to verify cites that would read identically at the frozen HEAD. Fix: cite these at T and note H4 only where the historical pin matters.

**LOW — `CHANGED-CONTRACT-COVERAGE-v2.1.md:24,35`: two cross-references were missed by the v2.1 retarget pass.** Row R11 still cites `PROJECTOR-BRIEF-v2 §4` and row R22 `ISSUANCE-BRIEF-v2 §4.2`, while every other cross-reference in the five v2.1 copies was systematically retargeted to the `-v2.1` names (see `V2-TO-V2.1.diff` lines 11–21, 46–51, 105–117, 150–186). The v2 documents are preserved so the links are not dangling; this is an internal inconsistency in an otherwise clean pass. Fix: retarget both, or state that intra-row provenance cites intentionally stay at v2.

## Brief checks closed (no finding)

**1. N11.** Parent recursion carries the same OPERATION and the same ITEM — `N11:9,:25,:31,:37`; verified against `S:docs/design/generic-recipient-scope-contract.md:158–163` (sub-conversation requires "recursive `Select(S,parent,item)`", "reject cycles, unknown parents and cross-connection lineage"), `:170` `WakeCeiling … Select(wake,R,item)`, `:172` `AutomaticBindingEligible … Select(reply,R,item)`. `N11:25` correctly binds "item" to the identical `AdmissionInput` envelope object (`A:src/mcpl/inbound-admission.ts:18–20`, root-exported at `A:src/index.ts:221`) and forbids child-channelId matching for parents. Native-parent provenance with mismatch/unknown → deny: `N11:31,:37,:38` plus the §3 evidence table — "a parent lookup, parent field or READ flag cannot replace native proof". The single refusal name reuses the **existing** `admission_denied` (`A:src/mcpl/inbound-admission.ts:11–13`), with no new `RefusalCode`. Cycle detection is sound as specified: tracking "every visited accepted RegistrationRef by exact object identity" works because AF mints exactly one frozen ref object per accepted record (`registration-view.ts:99,:103,:105`) and `data()` returns already-known refs unchanged rather than cloning them (`:48`) — so `facts.parent` is the real ref object. Abort is checked before each pass, each node and immediately before allow, fails closed, adds no await/timer/listener (`N11:52`); the single final R/current recheck (`N11:38`) is sufficient precisely because the pass is synchronous. A READ fact is never promoted — `N11:43,:44,:46` and §3 rows for WAKE/REPLY ("No affirmative from a READ fact or from frozen C6 WAKE true"). No production affirmative path from the frozen `parentAuthority(scope)`: `N11:70,:72`, and I confirmed the mechanism — with `parentAuthority` returning `undefined`, `dynamic-policy.ts:91` returns `false`, so the old shared fact cannot yield an affirmative for any operation. The bound is framed as a proposed resource guard, not as proof of unrestricted hierarchy equivalence (`N11:51`; `DELTA:7`) — see L1 for the one precision gap.

**2. N10.** The verifier's truth source is handled correctly: an accepted/current registration is required as a **conjunct** at every affirmative, and I found no wording treating native attestation (or a structural sidecar) as sufficient. `N10:54` — project keeps correlation but "candidate R is not yet current, so do not require current(candidate) here or issue H handles" (correct: `registration-view.ts:114` calls `project` before `ready`, so `current(ref)` is false then). `N10:55` — `lookup` requires bound K/view **and** accepted settled scope **and** `current(R)` **and** the private correlation **and** freshness. `N10:56` — `access` requires "same original K/view/ref still current". `N10:28,:47,:49` — "not a caller's `verified:true` assertion", "the callback's TypeScript shape alone is NOT proof", "its shape alone is not authority". `N11:31` step 1 — "Type shape/brand/descriptor/ACK alone proves none of these facts." Single private issuer/accessor with no model/descriptor/ACK factory and no cast/decoder/public brand constructor: `N10:26,:56,:63` (and `:63` correctly identifies that `H4:src/recipient-scope.ts:6–7` is a *declared* symbol with a `never` member, i.e. uninhabitable today — confirmed identical at `f378eadf`). No duplicate generation mechanism: `N10:67` — "`mappingGeneration` remains opaque immutable AF data (:20). No comparison, counter, ordering, custom epoch, persistent identity or replacement registry." Retirement uses AF's existing identities and current rules: `N10:57` — "AF `current` remains the authoritative lifetime check even if cleanup has not run. Old handles never migrate to a new binding or revive when equal data is re-registered." The closed scope is exactly 6 rows and each target checks out: `compileRecipientAdmission` exists at `src/recipient-admission.ts:86`; `src/recipient-scope.ts` and `src/recipient-admission.ts` exist; `src/recipient-scope-evaluator.ts`, `test/stubs/f-host-t2/v2/*` and `test/recipient-scope.test.ts` are correctly absent-and-new. Minimality holds for the stated design, subject to M2. One observation, not a defect: `N10:33–44` types the API on AF `ConnectionKey` while the in-scope seam uses the structurally identical local `Key` (`src/recipient-admission.ts:8`), so the two are mutually assignable and the compiler gives no protection against a fabricated key — `N10:56`'s runtime issuer-owned identity table is correctly the sole guard, and the draft says so.

**3. Packaging.** Real root-exported AF types are preferred and verified at **both** pins: `src/index.ts:226` re-exports `RegistrationRef, RegistrationView, RegistrationDescriptor, RegistrationScopeConfig, VerifiedRegistrationScope, ReadonlyData`; `:221` carries `InboundAdmissionConfig, AdmissionInput, AdmissionVerdict, AdmitInbound, AdmissionLane, ConnectionKey, RefusalCode`. `git diff --no-ext-diff --no-textconv a0970645 f8af03a8 -- package.json src/index.ts` is empty, so the claim of byte-identical package/barrel across pins holds. Declaration/artifact identity is correctly kept separate from source availability: `N10:15` — `dist/src/index.d.ts` unresolvable at either pin is "**not** a claim that a built package lacks types". Local exact dependency/artifact planning is treated as allowed engineering work and external publication is excluded (`N10:18,:86`). No private absolute path appears in anything proposed as a tracked change — the §4 targets carry none, and the existing precedent `test/stubs/f-host-t2/v1/view-contract.ts:2` cites a bare SHA with no local path. The `/home/orgasmic/src/agent-framework` strings are confined to the citation-convention lines of the PRIVATE/LOCAL drafts (`N11:7`, `N10:7`, `DELTA:38`), which the brief permits; worth carrying a "strip local paths" note forward if any of this text is ever promoted. The version claim itself is H1.

**4. Host-R.** Deferred, and the AF baseline is stated exactly — `N10:69` "conditional on **`config.replyAuthority` AND `requiredAdmission.registrations` present**, the latter created from configured `registrationScope`". Verified: `A:src/framework.ts:34945` `if (config.replyAuthority)` and `:34953` `if (connection.requiredAdmission?.registrations)` jointly gate the exact-ref branch at `:34954–34956`; `registrations` is constructed from `config.registrationScope` at `A:src/mcpl/inbound-admission.ts:109`; first target R is cached at `:34954/:34956`; AF ANDs `valid(...) && authorize(...) === true` at `A:src/mcpl/reply-routing.ts:137` and replays it via `replyBindingCurrent` (`:24–26`); routed/bound publication returns `automatic_reply_stale` at `A:src/mcpl/channel-registry.ts:5993`. The caveat at `N10:69` is also correct: `isCurrent` at `framework.ts:34951` is unconditional but is a **connection-epoch** check, not a target-R pin. `N10:71` and `DELTA:20` carry the same statement consistently. (Premise reachability is covered by H1 part 3.)

**5. LOW delta (v2.1 diff) — all four sub-checks close.**
- *R09 in its own unsupported bucket*: **closed.** Removed from the contract table (`V2-TO-V2.1.diff` hunk `@@ -18,7 +20,6 @@`) and re-entered in a new "Unsupported / evidence-scope correction — outside the coverage axis" section with Class `—  (not a contract row)`, evidence marked "not frozen source", bucket "unsupported / evidence-scope correction". Explicitly "retained, not deleted" and "the inventory is not asserted false".
- *Totals honest*: **closed, independently recounted from the table rows rather than from the summary.** 30 contract rows; classes E=24 (16 R-rows + 8 ROW-rows), I=3 (R07, R08, R16), P=2 (R05, R11), S=1 (R21) → 30. Statuses covered=9 (R01, R02, R03, R06, R17, R18, R19, R20, R22), defence-in-depth=3 (R04, R05, R23), model artifact=2 (R12, R13), uncovered=16 → 30. Plus R09 → 31. Matches the stated `E24/I3/P2/S1` and `9+16+2+3=30`, `31 = 30 + 1`. The old figures (`E=25`, `covered=10`) decrement by exactly one because R09 was an E/covered row — the arithmetic correction is sound in both directions.
- *Cites aligned*: **closed.** `VerifiedRegistrationScope` body is `:13–21` with the normative verifier comment `:12` cited separately, consistently in `N10:14`, `PROJECTOR-BRIEF-v2.1.md:43` and `ISSUANCE-BRIEF-v2.1.md` (the diff shows `:12–21` → `:13–21 (normative verifier comment separately :12)`); verified against `A:src/mcpl/registration-view.ts` where `:12` is the comment and `:13–21` the interface body. `view-contract.ts:1–8` is used uniformly (`CHANGED-CONTRACT-COVERAGE-v2.1:21`, `ISSUANCE-BRIEF-v2.1:17`, `PROJECTOR-BRIEF-v2.1:47`), with `:9–23` reserved for the remaining type surface; verified at `T` where `:1–6` is the comment, `:7–8` the nominal declaration. LOW-2 is also closed: "complete list of host object:path streams" → "host object:path streams were hash-read **during the v2 round** … this is not an inventory of every H4 recipient module", with a new header paragraph defining inherited "this round" as v2.
- *Two false-claim closures linked*: **closed.** Both appear under "Earlier false claims — explicitly CLOSED, not renamed gaps": missing-R-as-open-baseline-gap → CLOSED AS FALSE, and verified-absence-over-three-files → CLOSED AS UNSUPPORTED, each linked to `XN4YJ-DRAFTS-v1-REVIEW1.md` and `XN4YJ-DRAFTS-v2-CHECK1.md`. I verified both files exist and hash exactly as quoted: `066c11fdda0948102f3b437298543d7883c3692216cea7bbe24645f3016d9e7d` and `fb3109763cc8416a3d18477896b773b3557938b4f02975f7f7dcc07ec489a6f9`. The closures correctly do **not** close the genuine H-issuer/native/artifact dependencies.
- *Scope of the diff*: I read all five file sections. Every hunk is link retargeting, cite precision, scope narrowing, the R09 bucket, the closures or the totals fix. No row class, status, tuple, count or conclusion is promoted — the "LOW-only" label is accurate.

## Verification gates

The brief named **no executable gate**, and node/npm/bun/tsc, tests, builds, network and acquisition are forbidden in it. No gate was run and **no gate receipt is claimed**; nothing here inherits a prior PASS. All verification was read-only `git show` / `git grep` / `git rev-parse` / `git merge-base` / `sha256sum`, with `GIT_OPTIONAL_LOCKS=0`.

| Read-only check | Result |
| --- | --- |
| HEAD, manifest 9/9, manifest self-digest, four source pins | All match the binding list (table above) |
| `S:docs/design/generic-recipient-scope-contract.md:145–180` | `:158–163`, `:170`, `:172` confirm same-operation/same-item recursion and separate wake/reply predicates |
| `A:src/mcpl/inbound-admission.ts:1–30,40–60,104–118` | `admission_denied` at `:11–13`; `AdmissionInput` at `:18–20`; registrationScope deadline guard `:47–51`; `registrations` construction `:109`; `isCurrent` `:112–113` |
| `A:src/index.ts:215–232` at **both** AF pins | `:221` and `:226` root exports present; scoped two-file diff across pins empty |
| `A:src/mcpl/registration-view.ts:1–35,36–61,70–120` | `:6–7`, `:12`, `:13–21`, `:22–25`, `:26–32`, `:20`, `:77–82`, `:99`, `:103`, `:116` all as cited; `data()` ref-identity passthrough at `:48`; unbounded-depth `current()` recursion at `:77–82` |
| `A:src/framework.ts:34940–34960`; `reply-routing.ts:20–30,130–145`; `channel-registry.ts:5982–5998` | Conditional B baseline, `valid && authorize`, `replyBindingCurrent`, `automatic_reply_stale` all as cited |
| `T:test/stubs/f-host-t2/v1/dynamic-policy.ts:1–111` | `:21–34`, `:64–67`, `:90`, `:102`, `:105–106` as cited; `:91` confirms `parentAuthority → undefined` denies every child |
| `T:src/recipient-admission.ts:1–45,86`; `T:src/recipient-scope.ts:1–15`; `T:test/stubs/f-host-t2/v1/view-contract.ts:1–25` | Closed-scope symbols exist/absent as the table claims; H4-pinned ranges byte-identical at T |
| `T:package.json:28–38`; `git show v0.30.0:{package.json,src/index.ts,src/mcpl/registration-view.ts}` | Basis for H1 |
| `git grep` of 0.31-only symbols over `f378eadf -- 'src/*'` | No hits — host does not use that surface today |
| `test/stubs/f-host-t2/{run-mutants,check-compiler-controls,check-view-contract}.mjs` | All hardcode v1 paths, none glob — basis for M2 |
| Independent recount of all 31 coverage rows by class and status | Matches the corrected totals exactly |
| `sha256sum` of the two linked review documents | Match the quoted digests |

No edit, commit, staging, git write, package/toolchain execution, network/adapter/API call or acquisition occurred. Nothing was written outside `$ORGASMIC_EVIDENCE_DIR`.

## Unmet criteria / test gaps

No scoped artifact is missing and the drafts claim no implementation, test result or qualified behavior — that self-limitation is accurate and consistently repeated. Gaps that remain, as gaps:

1. **No behavioral evidence of any kind exists** for either proposal, by design. Both are (P). The eight projector witnesses, actual parent-WAKE and parent-REPLY conjuncts, and F2 behavioral discrimination remain UNCOVERED; `N11:78`/`DELTA:16` label a read-only milestone PARTIAL and refuse "permanently uncovered" as an option — correct and worth preserving verbatim into any implementation assignment.
2. **M2**: the mutant discriminators N11 itself requires cannot be produced inside the proposed scope.
3. **M1**: the replacement-branch conjunct mapping is the most likely place for a test to pass without exercising the criterion — specifically the DM counterpart/author-equality conjunct (`dynamic-policy.ts:95–97`), which `N11:74`'s discriminator list does not pin.
4. **Native inputs N1–N9 remain UNKNOWN** and are not filled by a brand, sidecar or structurally typed callback. N6 is still the uncovered I6 attestation row.
5. **No emitted declaration/artifact receipt**; `dist/src/index.d.ts` is untracked at both pins, correctly reported as unprovided rather than absent.

## Minimal remaining SOURCE-CONTRACT gates

These are missing *input contracts*. Each must be supplied and reviewed before **any** affirmative verdict is implementable; none is satisfiable by drafting, by a host brand or by AF packaging work.

- **SC1 — N4/N5**: authenticated native evidence/descriptor schema plus producer/account/session attestation. Gates the `verify` parameter at `N10:36` and every row of the `N11` §3 table.
- **SC2 — N1/N2**: exact native root/child mapping and independently verified parent relationship with claim/native agreement. Gates `N11:37` and `N10:56` parent resolution.
- **SC3 — N3**: current account readability/membership or DM counterpart, plus actual-item author provenance. Gates `N11:36`.
- **SC4 — N7**: freshness and ordered withdrawal guarantees. Gates the `N10:55`/`:56` freshness prerequisites.
- **SC5 — N6**: the qualified attestation row (I6) — still uncovered.
- **SC6 — N9**: effect/root/access validation for REPLY effect, separate from the scope ceiling.
- **SC7 — review-only, no new input**: close the `N11` §4 argument/check correspondence (M1). The only source-contract gate that this review alone can retire.

## Artifact / enablement gates (separate, and separately ordered)

- **A2 — host dependency resolution/pin scope (N12/N13 dependency half). Ordered PREREQUISITE of any N10/N11 implementation assignment**, per H1. Must deliver a manifest that can actually resolve the registration surface, plus the exact composed artifact identity.
- **A1 — reviewed composed-snapshot archive, integrity digest and emitted declaration closure** (N13 artifact half). Independent of A2's range fix.
- **A3 — mutant/compiler-control harness scope** for the new v2 wrapper and `src/recipient-scope-evaluator.ts` (M2).
- **A4 — owner items 6/7**: automatic-reply opt-in and optional external-notice route. Not engineering, and not substitutes for SC1–SC6.
- **A5 — production loader/opt-in barrier**: unchanged and still refused; `H4:src/recipient-policy.ts:3–13` opt-in remains refused.

Ordering that follows from the evidence: **A2 → (A1, A3) → implementation assignment**, with SC1–SC6 gating activation independently of all of it. Approving the N10/N11 contracts does not authorize source work, and the §4 list is not an implementation permission.

## Residual risk

1. **Highest**: the dependency-range fact in H1 is invisible in the current drafts, so the most likely failure mode is an implementation assignment scheduled before A2 and discovering at first typecheck that none of the required AF types resolve — after source edits have been authorized against frozen surfaces.
2. The 0.31.0-only nature of the registration surface also means the host-R DEFERRAL rests on a baseline the host cannot currently reach. The deferral decision is still reasonable; its *stated justification* needs the A2 qualifier.
3. M1's unmapped conjuncts are a silent-weakening risk: a v2 wrapper that forwards children to N11 while dropping the DM author-equality check would still look correct against the prose.
4. 32-node refusal (L1) is a deliberate availability trade AF does not impose; deep legitimate ancestry would be denied by H and no review artifact currently surfaces that asymmetry to whoever accepts the constant.
5. Unchanged and correctly held in the drafts: all native values remain UNKNOWN; no grants, wire authority, native writes, external route, deployment, candidate adoption or completeness claim. Modules outside the targeted reads are not verified by this round, and I reopened no inventory.
6. My own review inherits the drafts' pins. I verified every cite in scope at those pins but ran no executable gate — no behavioral property of any proposed interface has been demonstrated by anyone, including me.

## Fix directions (ordered)

1. **H1** — in `N10:16`, `N10:18`, `N10:86`, `DELTA:19`, `DELTA:50`: state that `^0.30.0` admits only `<0.31.0`, that `v0.30.0` contains no `src/mcpl/registration-view.ts` and root-exports none of the required types, and therefore that the registration surface is unavailable under the current manifest. Reclassify the dependency/artifact scope from "separate" to **ordered prerequisite** (A2), and note that the §4 edit scope is blocked until it closes. Keep the correct separation of declaration/artifact identity from source availability — only the strength and ordering of the claim change.
2. **M1** — add a conjunct-correspondence table to `N11` §4, one row per frozen check at `dynamic-policy.ts:86–99`, and state that the host predicate's `channelId` argument is not forwarded to the evaluator.
3. **M2** — either extend the §4 table with the two harness files, or declare mutant discrimination explicitly deferred so the PARTIAL label names what is deferred.
4. **L1** — one sentence at `N11:51` citing `registration-view.ts:77–82` and the depth asymmetry.
5. **L2** — re-anchor the H4 host cites to T (byte-identical), keeping H4 only where the historical pin matters.
6. **L3** — retarget `CHANGED-CONTRACT-COVERAGE-v2.1.md:24,35` to the v2.1 names, or state the exception.

Nothing above is a readiness, activation, qualification or completeness claim, and this review grants no edit, implementation or production authority.
