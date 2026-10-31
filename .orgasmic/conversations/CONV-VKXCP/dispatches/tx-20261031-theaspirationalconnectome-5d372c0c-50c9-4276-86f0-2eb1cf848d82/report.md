# CONV-VKXCP — bounded re-check of F-host.4 brief v4.1 (TASK-4ZM52)

Read-only. No code, commit, build or install. Target
`/srv/review-drop/tarn-rtaln-20261030/FHOST4-BRIEF-v4.1.md`, sha256
`f178bf3b51ecd3c9d11e6c60f3481fd2aaa63a3a009448abc6e8bbcb962caaf4` **verified**
(matches dispatch). Prior check `/srv/review-drop/TASK-4ZM52-BRIEF-CHECK1.md`,
sha256 `ae3aefcea046253876c61da136225e8cc52f226ec09947912df171085c57abfb`
**verified**. Operator Tarn, qualifier Soma; nothing here is a decision by the
owner Vasyl.

## Verdict

**READY WITH CHANGES** — two blocking edits (HIGH-1, HIGH-2), three
clarifications (MEDIUM-3/4, LOW-5/6). 9 of the 11 CHECK1 fix directions are
fully closed, 1 mostly closed, 1 (#2, the authorised .3 edits) **partially**
closed: §2.5 still presents itself as a closed list while the work it authorises
necessarily changes two things outside it, and §4's "every other .3
control/mutant must still pass" / "v0/v1 typecheck + controls unchanged" are
false as written. An implementer following v4.1 literally hits §5's "any .3 edit
outside §2.5" STOP on first contact with `recipient-policy.test.ts:208`.

Everything else re-checked clean: the v2 B drop is byte-perfect, all new
citations resolve exactly, the install condition is now correct at the only seam
that exists, and the AF/host division of labour is on the page.

---

## Findings

### HIGH-1 — §2.5's closed edit list omits .3 test re-baselining that .4 cannot avoid; §5 then makes that work a STOP

§2.5 authorises replacing the `recipient_reply_authority_pending` throw
(`src/recipient-admission.ts:85` @ `c353e1c8`, verified exact) and emitting
`replyIntent` from `admit` (replacing `:136–137`, verified exact), then says
"**only these**; everything else in .3 unchanged" and "every other .3
control/mutant must still pass". Both claims are false:

- `test/recipient-policy.test.ts:208` asserts
  `expect(() => compileStaticRecipientAdmission(parseStaticPolicy(fixture.policy), …)).toThrow('recipient_reply_authority_pending')`.
  `fixture.policy` (`policy-shapes.json:4–27`) has `automaticReplies:{timeoutMs:900}`,
  so removing the `:85` throw **fails this assertion unconditionally**. Its test
  title, `:203` "off installs neither callback; admission-only installs no B/D;
  **full waits for .4**", is also obsolete — and that title is the witness
  pattern for two live mutants (`run-mutants.mjs:23,24`, matched by
  `run-mutants.mjs:44,49`).
- Once `admit` emits `replyIntent` for channel-bearing admitted work, every
  exact-equality verdict assertion on channel-bearing work breaks:
  `recipient-policy.test.ts:213, 222, 223, 233, 234, 235, 281` all use
  `toEqual({ store: true, mayWake: … })`, which rejects an added member.
  (The inference-lane assertions `:251–266`, including `:264`
  `not.toHaveProperty('replyIntent')`, correctly stay passing under §2.5's
  "inference/channel-less never".)

§5 lists "any .3 edit outside §2.5" as a stop condition, so the brief as written
forbids the only way forward. §3a compounds it: it says .3 behaviour is
"requalified fresh at the .4 head" but does not say which .3 receipts the
re-baselining invalidates.

**Fix:** add to §2.5 an explicit authorisation to re-baseline
`test/recipient-policy.test.ts` — naming `:203` (title), `:208` (the removed
throw) and the verdict assertions at `:213,222,223,233,234,235,281` — and
replace §4's "every other .3 control/mutant must still pass" with "every other
.3 control/mutant must still pass, except the re-baselined cells listed in
§2.5, which must be re-stated and re-run at the .4 head". Also state whether
`replyIntent` emission is gated on `mayWake` and/or the `reply` predicate (see
MEDIUM-3) — that choice decides which of the seven assertions change.

### HIGH-2 — adding `reply` as a required member breaks the frozen v1 stub's typecheck, which §2.5 and §4 both promise is unchanged

§2.5 authorises "add `reply: (key, channelId, scope, input) => boolean` to
`CompiledRecipientAdmissionPolicy` (`src/recipient-admission.ts:24–32`)" —
written as a required member — and in the same bullet says the v2 stub is new
while "**v1 stays frozen and passing**". §4 gates "v0/v1 typecheck + controls
unchanged".

`test/stubs/f-host-3/v1/static-policy.ts:50–57` builds the compiled policy as an
object literal with exactly `accountId, timeoutMs, lanes, read, wake,
wakeWithoutChannel, automaticReplies` (`:53–56`) — no `reply`. The v1 stub's
`tsconfig.json` has `"files": ["a-contract.ts","typecheck.ts","static-policy.ts"]`
with `strict: true`, and `typecheck.ts:8` calls
`compileStaticRecipientAdmission(policy, read)`. A required readonly member
missing from that literal is a hard `strict` error, so the v1 typecheck gate
fails at the .4 head. (`recipient-policy.test.ts:246–249,274–279` build the same
literal, but the root `tsconfig.json` has `"include": ["src"]`, so only the stub
gate catches it.)

**Fix:** pick one in §2.5 and say so — either declare the member optional
(`readonly reply?: (key, channelId, scope, input) => boolean`) with the explicit
fail-closed default (absent ⇒ no channel is reply-permitted ⇒ resolver returns
`null`), or authorise re-baselining `test/stubs/f-host-3/v1/static-policy.ts`
and drop "v1 stays frozen and passing" from §2.5 and "v1 typecheck unchanged"
from §4. The optional form is the one that keeps §2.5's own promise.

### MEDIUM-3 — §2.5's `replyIntent` emission rule is broader than host:222–224 and its gating is unstated

§2.5 says `admit` emits `replyIntent {version:1, channelId}` "for channel-bearing
admitted work only". `generic-recipient-host.md:222–224` @ `d7154d79` says
intent is minted "**For an eligible automatic reply**", and the repo's own
outcome oracle agrees: `check-scope-contract.mjs:184`
`newBinding = wake && p.automaticReplies && select(p.reply, i)`. Under §2.5 as
written, intent is emitted for admitted work with `mayWake:false` and for
channels the `reply` predicate denies.

No unsafe leak follows — `reply-routing.ts:110` returns `{}` when
`!verdict.mayWake`, and the resolver re-validates per §2.1 — so this is a
specification gap, not a hole. But it is load-bearing for HIGH-1 (which `toEqual`
cells change) and for cell 8.

**Fix:** state in §2.5 whether emission is gated on `mayWake` (and whether on
the `reply` predicate), and cite host:222–224 for the "eligible" wording.

### MEDIUM-4 — §3 cell 1's policy-free clause and mutant M2 are unrealizable or already covered; the positive install case has no named control or mutant

The install condition itself is now **correct** at the only seam that exists
(see "What checked out"). The controls around it are weaker than they read:

- `automaticReplies` exists nowhere outside the policy object:
  `git grep automaticReplies c353e1c8 -- src/ test/stubs/` returns only
  `recipient-admission.ts:31,85` and the v1 stub's parser/mapper
  (`static-policy.ts:17,20,37,38,43,56`), and `parseStaticPolicy` requires all
  nine keys via `closedData` (`:19–20,24`). So cell 1's "policy-free (no
  recipientPolicy, **any automaticReplies value/absence**)" has no
  configuration location to vary — the clause cannot be built without inventing
  a seam, which §5 would stop.
- M2 ("install on `automaticReplies !== false` without a policy") can only be
  written by mutating `recipient-admission.ts:84`
  `if (policy === undefined) return {};` — which is verbatim the existing .3
  mutant `run-mutants.mjs:23` ('feature-off installs callback'), already killed
  by the control at `recipient-policy.test.ts:204`. M2 adds no new kill; M1 is
  the single genuinely new one.
- The positive case is the one frozen fixture expectation .4 *flips*:
  `policy-shapes.json:81–92` (`full`: `recipientPolicyEnabled:true`,
  `automaticReplies` inherited from `:24–26`, `expected.replyAuthority:true`)
  goes from "throws" (test `:208`) to "installs". §3 names `admission-only`,
  profile-off and policy-free but never `full`, and no mutant guards against a
  path that never installs.

**Fix:** drop or re-word cell 1's "any automaticReplies value/absence"
(policy-free is `policy === undefined` at `:84`, nothing more); record M2 as the
existing `run-mutants.mjs:23` rather than a new mutant; add `full`
(`policy-shapes.json:81–92`) as the positive control with a "never installs"
mutant.

### LOW-5 — two residual citation slips

- §1 host row: "`:215–221` connector responsibility". At `d7154d79`,
  `generic-recipient-host.md:216` is "connector remains responsible for …"; the
  sentence opens at the end of `:215`, the connector material runs to `:219`,
  `:220` is the context-only/wire-conflict sentence and `:221` is blank. The
  `:209–214` material the range swallows (PROVISIONAL read-channel-entry, the
  wake upper bound, addressed-tag ontology) is unlabelled. §1's other locators
  are exact: `:207–208`, `:222–231` intent, `:233–239` resolver (`:239` "AF owns
  registration/grant/current-epoch revalidation and the frozen binding"),
  `:241–249` authorize (`:241` "always installs", `:244–246` AF's additional
  checks), `:251–260` failure — all verified verbatim.
- §1 H-brief row: "table row :26 (8–12 h, **note :259**)". `:26` is correct for
  .4. `f-host-h-brief.md:259` — "**8–12 h**, the table's existing slice, not an
  added partial-slice estimate" — sits under `:253` "## 6. First independently
  testable implementation slice: **F-host.3**" and is a note about **.3**, not
  .4. (CHECK1's LOW-10 carried the same slip.)

**Fix:** re-label as `:215–220` connector responsibility / context-only (or
`:209–220`), and drop the `:259` cross-reference or re-attribute it to .3.

### LOW-6 — §2.2 "explicitly enabled" vs §2.5 "`automaticReplies !== false`" are two spellings of one rule

At the compiled seam they coincide: `automaticReplies: false | Readonly<{timeoutMs:number}>`
is a required member (`recipient-admission.ts:31`), and `:84` returns `{}`
before `automaticReplies` is ever read when the policy is absent — so §2.2's
"any default when the policy is absent must NOT install B" is a non-condition,
and "explicitly enabled" reduces to `!== false`. The two phrasings only diverge
if read against operator-config shapes, where `automaticReplies` *can* be
absent — `policy-shapes.json`'s `full` case has no case-level key and inherits
`{timeoutMs:900}` from the shared block at `:24–26`. I verified that inheritance,
so there is no live contradiction, but the "explicitly enabled" wording invites
one.

**Fix:** use §2.5's spelling in §2.2 — "installed iff the compiled policy is
present and its `automaticReplies !== false`" — and add the `full`-case note
that the fixture's enabling value is inherited from `policy-shapes.json:24–26`.

---

## CHECK1 fix-direction closure (brief-line evidence)

| # | CHECK1 direction | Status | Evidence in v4.1 |
| --- | --- | --- | --- |
| 1 | install condition + `admission-only` control | **closed** (wording: LOW-6, controls: MEDIUM-4) | §2.2 "Installed iff BOTH … AND … explicitly enabled", cites host:241 and `policy-shapes.json:70–78`; §3 cell 1 + M1/M2 |
| 2 | authorise/scope the .3 edits; disambiguate the refusal; list re-baselined controls | **partially** | §2.5 has all four edits + v2 + b-contract, names `recipient_policy_integration_pending` (`recipient-policy.ts:6`) as the one that stays, retires `run-mutants.mjs:24`. Not closed over the test re-baselining (HIGH-1) or the `reply` typing (HIGH-2) |
| 3 | descriptor cell → PENDING-on-I6; restore host:238–239/:244–246 | **closed** | §2.2 "Descriptor identity, publish grant and incarnation are AF's (host:244–246, `reply-routing.ts:106,136–139`)"; §1 cites both ranges; §3 PENDING list |
| 4 | widen host pin to :207–260; lane/channel-less rule + test + mutant | **closed** | §1 "**:207–260**", :207–208 cited; §2.1 "Lane rule first"; §3 cell 2 + mutant |
| 5 | unbound-automatic-prose inventory | **closed** | §2.4 (cites `docs/reply-routing.md:90–98`, verified exact), §3 cell 10, §5 stop condition |
| 6 | label every B cell stub-of-B; pin the notice by sha | **closed** | §3 header; byte check vs `src__mcpl__reply-routing.ts:27` @ sha `9627171b…` — digest verified in the v2 drop's SHA256SUMS |
| 7 | extend the B drop; record the `serverId` fact | **closed** | v2 drop adds `channel-registry.ts`/`inbound-admission.ts`/`mcpl/index.ts`/`package.json`, all byte-verified; §1 "AF sets `serverId` from `input.connection.connectionId` (`reply-routing.ts:135`)" + "Host authorize must not compare against a configured server id" |
| 8 | ceiling via `trusted.lookup`; defer T1:284 | **closed** | §2.2 "only through `trusted.lookup` with versioned doubles (`recipient-admission.ts:34–41`)"; "T1:284's scope-generation check is **deferred to T2/I7**" |
| 9 | disposition for `check-scope-contract.mjs` | **closed** | §3 final para: "T1/T2 oracle, out of .4 scope", both mismatches recorded |
| 10 | T1:72→:70–82 and host-`:215` citations; pin the A drop; locators for §4 digests | **mostly** (LOW-5) | T1:70–82 with row :82 ✓; A drop SHA256SUMS digest `1a8406fa…` now in §1 ✓; "(see FIX1/LOWS2 bindings)" resolves ✓. Residual: `:215–221` label and the `:259` note |
| 11 | add the missing cells/mutants; re-state the estimate | **closed** | §3 has 10 host cells + stub-of-B set + PENDING list; §6 restates 14–20 h |

## Install condition (CHECK item 2) — verified against the code

- `recipient-admission.ts:84` `if (policy === undefined) return {};` — policy-free
  installs nothing, before `automaticReplies` is read. Profile-off is covered by
  `test/recipient-feature-off.probe.ts:33` (`Object.hasOwn(server,'replyAuthority') === false`, verified).
- `policy-shapes.json:70–79` `admission-only`: `recipientPolicyEnabled:true`,
  `automaticReplies:false` (`:72`), `expected.replyAuthority:false` (`:76`) —
  the brief's citation and reading are correct.
- `replyAuthority` appears nowhere in `src/` at `c353e1c8`.

So the brief's condition is behaviourally right. Its controls are the weak part
(MEDIUM-4): M1 is a genuine new kill, M2 duplicates `run-mutants.mjs:23`, the
policy-free variation clause is unbuildable, and the positive `full` case is
uncontrolled.

## Verification Gates

| Gate | Command | Outcome | Log |
| --- | --- | --- | --- |
| `contract-integrity-v2` | `bash $ORGASMIC_EVIDENCE_DIR/verify-drop-v2.sh` | **exited:0** | `gate-contract-integrity-v2-01M685QY71EWSGYQN6RJ9BE36T.log` |

Gate contents, all passing:
- brief sha256 = `f178bf3b…` as dispatched; CHECK1 sha256 = `ae3aefce…`.
- v2 drop `SHA256SUMS` sha256 = `d9c5164818dd166ae62418fc3ce6060345ad2ec212e3e9190e6f49d5c9a796fe` — **matches §1**.
- `sha256sum -c SHA256SUMS` → 10/10 **OK**.
- All 10 files **byte-for-byte identical** to `git show 29edcf8b7f7b31d0ab35f59ab0e6aab75c0f4e04:<path>` in `/home/orgasmic/src/agent-framework`.
- `src__mcpl__inbound-admission.ts` sha256 `66d99cd30b8669…` — identical to the A drop's copy **and** to `git show 869bb3ad…:src/mcpl/inbound-admission.ts`, so §1's "byte-identical to the A drop's 66d99cd3…" and CHECK1's "identical at both pins" both hold.
- A drop `SHA256SUMS` sha256 `1a8406fa…` and superseded v1 B drop `4a1aa6b9…` match §1.
- `869bb3ad` **is** an ancestor of `29edcf8b` (PROVENANCE's "on A 869bb3ad").

Unwrapped read-only probes (no receipt, reproducible): `git show`/`git grep`/
`git merge-base --is-ancestor` over `2c396622 → a316200c → bff8c46e → d7154d79
→ c353e1c8` (chain re-verified) and `c9f0991d` (confirmed as the last commit
touching `src/` at the .3 pin) in this worktree; `git show` over `29edcf8b` and
`869bb3ad` in the AF clone; `sha256sum` over `/srv/review-drop`. No builds,
installs, edits or network.

New citations re-verified verbatim (CHECK item 5): host `:207,216,222,233,239,241,244,251,260`;
T1 `:70,82,283,284`; h-brief `:26,118` (`:259` → LOW-5); `recipient-admission.ts:24–32,31,34–41,43–45,84,85,117–123,135,136–137`;
`recipient-policy.ts:6`; `policy-shapes.json:4–27,70–79,81–92`;
`run-mutants.mjs:23,24,41,44,49`; `recipient-feature-off.probe.ts:33`;
`static-policy.ts:11,17,19–20,37–38,50–57`; `v1/typecheck.ts:8`;
`check-scope-contract.mjs:40,45,46,182–194`; B `reply-routing.ts:6,7,15,27,96–104,106,110,112,121,135,136–139`,
`channel-registry.ts:673–676` (`export interface ChannelRef { serverId; channelId }`),
`src/index.ts:224` (types-only), `package.json:12–18` (only the `"."` subpath),
`turnReply:73–78`, `docs/reply-routing.md:57–58,90–98`. Every one matches the
brief. `27a32127` is a real AF commit (correctly labelled NOT a pin).

## PROVENANCE.txt — what cannot be verified (CHECK item 4)

Verified by the gate: source commit `29edcf8b…`, the method (`git show <sha>:<path>`,
reproduced byte-for-byte), the `/` → `__` path mangling, the 10-file list, the
"added vs v1" list, "on A `869bb3ad`" (ancestry), and "supersedes v1 (v1 files
unchanged, kept)".

Not verifiable from the supplied inputs:
- the extractor identity ("by Tarn") and the date ("2026-10-31") — file mtimes
  (Oct 31 05:03, root-owned) are consistent, nothing more;
- the label "slice B narrowed LOW head";
- "B is owner-held for upstream (**#931**)" — no locator, no local issue record;
- "Not a release/tarball (I6 MISSING)" — consistent with §1's MISSING row, but
  an absence claim, not a checkable fact;
- **the clone itself.** `/home/orgasmic/src/agent-framework` has
  `origin github-agent-framework:theaspirational/agent-framework.git` and
  `upstream https://github.com/anima-research/agent-framework.git`; `29edcf8b`
  is authored "Vasyl Dovhanchyn <48733736+theaspirational@…> Fri Oct 30
  21:11:35 2026" — consistent with owner-held fork work, but nothing outside
  this disk pins the clone. Byte identity was established **today** against it.

## Unmet criteria / test gaps

1. No authorisation for the mandatory `recipient-policy.test.ts` re-baselining,
   and no statement of which .3 receipts it invalidates (HIGH-1, §3a).
2. `reply` member optionality undecided → v1 typecheck gate fails as written
   (HIGH-2).
3. `replyIntent` emission gating unstated (MEDIUM-3) → cell 8's expectation is
   underdetermined.
4. No positive install control (`full`) and no "never installs" mutant;
   M2 duplicates an existing .3 mutant; cell 1's policy-free variation clause is
   unbuildable (MEDIUM-4).
5. Still genuinely PENDING, correctly listed and not stubbed into PASS:
   equal-string descriptor replacement, publish-grant/incarnation, real AF lane
   gating of the reply consumer, real transport/reconnect/delivery/notice (I6).
   `destination.mappingId` and the oracle's missing `inference/request` lane are
   recorded as a gap with no change — correct for .4.
6. §4's `docs-contract` is expected to stay FAILED (declared-filename false
   positive, TASK-EC6WW). The brief says so and forbids "all gates pass" — I
   could not verify the EC6WW disposition itself (no install, no run).

## Residual risk

- **Stub-bounded by construction.** Every §3 B-behaviour result is a statement
  about the host's stub of B: at `29edcf8b`, `src/index.ts:224` exports only
  `ReplyDestination`/`ReplyAuthorityConfig` as types and `package.json:12–18`
  publishes only `"."`, so `privateReplyNotice`, `replyConsumer`, `turnReply`,
  `reportReplyFailure` are unreachable. §3's labelling is now correct; the
  limitation is structural and permanent until I6.
- **AF-side lane gating remains unproven.** `docs/reply-routing.md:57–58` claims
  `inference/request` "does not resolve a channel reply destination", but
  `reply-routing.ts:110` gates only on `verdict.mayWake`, and
  `recipient-admission.ts:117–123` returns `{store:true, mayWake:true}` for
  `inference/request` unconditionally. §2.1's host-side `null` rule is the
  fail-closed answer; the AF behaviour lives outside both drops.
- **Clone trust** (above) and the unverifiable .3 dispositions
  ("docs-contract FAILED", "naming PARTIAL").
- The .3 report digests **do** resolve, as file digests rather than git objects:
  `sha256sum` of `TASK-6JAEQ-REVIEW1.md` = `25bea49a…`,
  `TASK-6JAEQ-FIX1-REVIEW.md` = `27d354e4…`, `TASK-6JAEQ-LOWS2.md` =
  `825788d1…`. The §4/§1 toolchain digests (`6b426785`, `7f0e4849`, `fde6a4bf`,
  `7ce7d6b6`) all appear in `TASK-6JAEQ-FIX1.md`/`FHOST3.md`/`LOWS2.md`, so
  "(see FIX1/LOWS2 bindings)" resolves. CHECK1's LOW-11 is answered.

## Estimate 14–20 h (CHECK item 7)

**Defensible as a range for §6(a) as v4.1 describes it, and still slightly
understated.** It covers what CHECK1 said 8–12 h did not: four
`recipient-admission.ts` changes, the v2 stub tree, `b-contract.ts` +
typecheck + negative control, the stub-of-B harness, the §2.4 inventory, 10 host
cells and the new mutants, under §4's no-retry bounded-gate battery (tsc
base/head, v0/v1/v2 typechecks, focused + full suite base/head, ~20 .3 mutants
re-run plus new ones, feature-off probe, self-test, docs-contract, diff --check).

Two reasons to expect the upper bound or past it:
- the HIGH-1 re-baselining (one `.toThrow`, one test title that is also a mutant
  witness, seven `toEqual` verdict assertions) is unbudgeted because the brief
  does not acknowledge it;
- §2.4's inventory is open-ended — a repo-wide hunt for unbound automatic-post
  paths with file:line and a named control, over at least
  `src/{bug-report,framework-agent-config,gate-telemetry,heartbeat-options,index,recipe}.ts`
  and `src/modules/subagent-module.ts`, any hit of which is a §5 STOP rather
  than work.

No change to the number is required; add a sentence that 14–20 h assumes the
re-baselining in HIGH-1 and a terminating inventory.

## Fix directions (edits before dispatch)

1. **§2.5 + §4** — authorise re-baselining `test/recipient-policy.test.ts`,
   naming `:203` (title, also a mutant witness), `:208` (the removed throw) and
   the verdict assertions `:213,222,223,233,234,235,281`; replace §4's "every
   other .3 control/mutant must still pass" with the except-clause; have §3a say
   which .3 receipts this invalidates. (HIGH-1)
2. **§2.5** — declare `reply` optional with a fail-closed default (absent ⇒ no
   reply-permitted channel ⇒ resolver `null`), **or** authorise re-baselining
   `test/stubs/f-host-3/v1/static-policy.ts` and strike "v1 stays frozen and
   passing" from §2.5 and "v1 typecheck unchanged" from §4. (HIGH-2)
3. **§2.5** — state whether `replyIntent` emission is gated on `mayWake` and/or
   the `reply` predicate; cite host:222–224's "eligible". (MEDIUM-3)
4. **§3 cell 1** — drop "any automaticReplies value/absence" (policy-free is
   `recipient-admission.ts:84`); record M2 as the existing `run-mutants.mjs:23`
   rather than new; add `full` (`policy-shapes.json:81–92`) as the positive
   control with a "never installs" mutant. (MEDIUM-4)
5. **§1** — re-label the host locator `:215–220` (or `:209–220`) connector
   responsibility / context-only; drop or re-attribute the `:259` note, which
   belongs to F-host.3's slice section. (LOW-5)
6. **§2.2** — use §2.5's spelling ("policy present and `automaticReplies !==
   false`") and note that `full`'s enabling value is inherited from
   `policy-shapes.json:24–26`. (LOW-6)
7. **§6** — add that 14–20 h assumes fix 1's re-baselining and a terminating
   §2.4 inventory.
