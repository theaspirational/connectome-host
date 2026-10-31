# CONV-M8YB5 — review of the F-host.4 brief/contract (TASK-4ZM52)

Read-only. No code changed, no commit. Target: `/srv/review-drop/tarn-rtaln-20261030/FHOST4-BRIEF-v3.md`
(sha256 `37dcdbae…` **verified**, matches the dispatch).

## Verdict

**NOT READY** — the brief must be edited before dispatch.

The pins, the ancestry chain and both contract extractions are clean (gate
`contract-integrity`, exit 0). What fails is the *scope statement*: the brief
describes .4 as additive host-side work against a frozen .3, but the frozen .3
compiler-core has no reply ceiling, never emits `replyIntent`, and actively
throws on positive `automaticReplies`. .4 cannot be implemented without
changing `src/recipient-admission.ts` and the v1 stub — which the brief neither
authorises nor budgets. Separately, §2.2/§3 assign host `authorize` a check the
B contract gives to AF, and §2.2's install condition contradicts the repo's own
frozen fixture in a way that would silently enable automatic publication under
`admission-only`.

Paths: host repo worktrees `…/task-conv-m8yb5-review` (docs) and `…/task-6jaeq`
(.3 pin `c353e1c8`); AF `/home/orgasmic/src/agent-framework`.

---

## Findings

### HIGH-1 — §2.2 / §3 install condition would enable B under `admission-only`

`FHOST4-BRIEF-v3.md:41` says synchronous `authorize` is *"always installed when
a policy is configured"*. The source says something else and the repo's frozen
fixture says something else:

- `generic-recipient-host.md:241` (@`d7154d79`): "The host **always installs this
  optional B hook**" — i.e. unconditionally for a B-opted connection, not
  "because a policy exists".
- `test/fixtures/generic-recipient/policy-shapes.json:70–78` (@`c353e1c8`): case
  `admission-only` has `recipientPolicyEnabled: true` **and**
  `automaticReplies: false`, with `expected.replyAuthority: false`.

A policy *is* configured in `admission-only`. An implementer following
`:41` literally installs `replyAuthority` there, enabling automatic publication
for an operator who declined automatic replies. This is exactly the shape the
.3 mutant `test/stubs/f-host-3/run-mutants.mjs:23` ("feature-off installs
callback") exists to kill, and §2.4's "profile-off/feature-off: neither callback
installed" does not cover `admission-only` (which is policy-on).

**Fix:** reword to "installed if and only if the compiled policy's
`automaticReplies !== false`", and add `admission-only` to the §3 control list
alongside profile-off, citing `policy-shapes.json:70–78`.

### HIGH-2 — .4 cannot be built without changing the frozen .3 interface; the brief implies it can

§2.1 requires the resolver to return a channel only when "permitted by
`replyChannels`" and §2.2 requires authorize to check the "current host reply
ceiling". Neither is expressible against the .3 pin:

- `src/recipient-admission.ts:24–32` — `CompiledRecipientAdmissionPolicy` has
  `read`, `wake`, `wakeWithoutChannel`, `automaticReplies`. **No `reply`
  predicate and no `replyChannels`.**
- `test/stubs/f-host-3/v1/static-policy.ts:36,42` parses and freezes
  `replyChannels`, but `compileStaticRecipientAdmission` (`:50–57`) **never
  passes it through** — the literal at `:53–56` has no reply member (and a TS
  excess-property check would reject one today).
- The repo's own reply oracle already expects it:
  `test/fixtures/generic-recipient/check-scope-contract.mjs:45`
  (`policyKeys = ['read','wake','reply','automaticReplies']`), `:184`
  (`newBinding = wake && p.automaticReplies && select(p.reply, i)`), `:193`
  (`privateNotice: … && !reply`). `scope-cases.json:26,112,175,…` carries
  `reply` selectors per case.
- `src/recipient-admission.ts:85` — `if (policy.automaticReplies !== false)
  throw new Error('recipient_reply_authority_pending')`. .4 must remove or
  relax this. The brief never names this refusal; §2.4 names only the
  *production-loading* one (`recipient_policy_integration_pending`,
  `src/recipient-policy.ts:6`) and says ".4 must not lift the .3 interim
  refusal" — ambiguous between two different refusals, one of which .4 must lift.
- `src/recipient-admission.ts:136–137` — `// No replyIntent until B exists` and
  `return { store: true, mayWake }`. **A's `admit` never emits `replyIntent`.**
  B passes `verdict.replyIntent` to the resolver
  (`reply-routing.ts:121`), so with .3 unchanged the resolver always receives
  `replyIntent: undefined` and §2.1's "unique registered **intended** channel"
  has no input. .4 must extend `admit` to emit `{version:1, channelId}`
  (validated by the already-wired `isRecipientReplyIntent`,
  `recipient-admission.ts:43–45,98`).

**Fix:** §2 must explicitly authorise and scope the .3 edits — add `reply:
(key, channelId, scope, input) => boolean` to `CompiledRecipientAdmissionPolicy`;
plumb `replyChannels` → `reply` in the stub (a **v2** stub, since §1 pins
`test/stubs/f-host-3/v1`); emit `replyIntent`; replace the `:85` throw with the
`automaticReplies !== false` install path — and state which .3 controls are
re-baselined. Also re-specify `run-mutants.mjs:24` ("full silently offered
before B"), which becomes vacuous once `:85` is legitimately removed.

### HIGH-3 — §3 specifies an authorize test the host signature cannot satisfy

§2.2 `:42` and §3 `:88` require host `authorize` to refuse an "equal-string
descriptor replacement". The host cannot see that. Per the B contract:

- `reply-routing.ts:15` — `authorize: (input: ReplyInput, destination:
  ReplyDestination) => boolean`; `ReplyDestination` is `{serverId, channelId}`
  only (`:6`, `ChannelRef`).
- `reply-routing.ts:136–139` — `current()` = `valid(input.connection,
  destination) && authorize(approvedInput, destination) === true`. Descriptor
  identity is checked by `valid`, an **AF-internal** callback
  (`replyConsumer(config, valid, …)`, `:106`), not by the host hook.
- `generic-recipient-host.md:244–246` says so explicitly: "AF **additionally**
  checks current publish grant, connection incarnation and registered
  descriptor identity at binding and immediately before open/send" — a sentence
  the brief's §2.2 **omits entirely**.

A stub test for this cell can only pass by having the host's own fake `valid`
do the work, which proves nothing about the host deliverable.

**Fix:** move the equal-string-descriptor cell out of the host-`authorize` test
list into a PENDING-on-I6 cell, and restore host:244–246 and host:238–239 ("AF
owns registration/grant/current-epoch revalidation and the frozen binding") to
§2 so the division of labour is on the page.

### MEDIUM-4 — inference lane / channel-less items: resolver will be called with no reply intent, and the brief has no rule or test for it

The brief's cited host range starts at `:215`, which **excludes** the governing
sentence at `generic-recipient-host.md:207–208`: "Inference can be explicitly
allowed by lane, with no channel reply intent. No channel-less item acquires an
automatic destination." The brief mentions lanes nowhere; §2.1 and §3 have no
inference/channel-less case.

This is not theoretical. `src/recipient-admission.ts:117–123` returns
`{ store: true, mayWake: true }` for `inference/request` **unconditionally** (no
`addressed` requirement), and B's `replyConsumer` gates only on
`verdict.mayWake` (`reply-routing.ts:110`) — there is **no lane check in the
extracted B code**, despite `docs__reply-routing.md:57–58` claiming
"`inference/request` … does not resolve a channel reply destination". Whatever
enforces that lives in AF code outside the extraction. So unless the host
resolver returns `null` for non-`channels/incoming` work itself, an inference
request is the most likely path to an unintended resolver invocation.

**Fix:** widen the §1 host pin to `:207–260`; add to §2.1 "lane must be
`channels/incoming` (or `push/event` with validated `origin.mcplChannelId`);
`inference/request` and any channel-less item → `null`"; add the test and a
mutant ("resolver reads `item.channelId` regardless of lane").

### MEDIUM-5 — B's documented opt-in prerequisite is absent from §2 and §5

`docs__reply-routing.md:90–94`: "Opting in **also refuses** unbound automatic
prose aimed at that connection (for example timer/home/heartbeat prose or an
unrelated module wake). Such prose gets a private failure notice, not a guessed
destination. **Check existing unbound-post dependencies before opting in.**"
`:95–98` further exempts wakes carrying private admitted work from the
redundant-wake drop.

Installing `replyAuthority` therefore changes host behaviour for prose that has
nothing to do with .4. The brief's §2.3 ("never refuses admitted work, hides it
or widens its wake ceiling") does not cover this, §2 "Out:" does not exclude
it, and §5's stop conditions do not list it.

**Fix:** add an §2 item requiring an inventory of existing unbound automatic-post
paths on a B-opted connection, with a named control; or add "an existing
unbound-post dependency is found" to §5.

### MEDIUM-6 — §3's B-behaviour cells are self-referential and cannot assert B's notice

The host cannot reach any of B's runtime values. At the pinned B commit,
`src/index.ts:224` exports **only types**: `export type { ReplyDestination,
ReplyAuthorityConfig } from './mcpl/reply-routing.js'` — that is the sole
reply-routing line in `index.ts`, and `package.json` `exports` declares only the
`"."` subpath (no `./src/*`). So `privateReplyNotice` (`reply-routing.ts:27`),
`replyConsumer`, `withReplyConsumer`, `turnReply`, `replyBindingCurrent`,
`sameDestination`, `reportReplyFailure` are all unreachable.

Consequence: §3's cells "throw → private + notice", "two connections, same
channel id", "revocation mid-turn → private", shared-turn approval and the
mutants "exception content in notice" / "resolver rerun on reconnect" all
exercise a **host-written stub of B**, not B. §3 marks only the reconnect cell
as "(stub of B's frozen binding)"; the rest read as if they test the contract.

**Fix:** label every B-behaviour cell as stub-of-B; and since the notice text
cannot be imported, pin `privateReplyNotice`'s exact string with a byte check
against `src__mcpl__reply-routing.ts:27` @ `9627171b…` rather than asserting it
from the package.

### MEDIUM-7 — §2.4's "cite file:line + sha from the extraction" is not satisfiable for the types .4 actually needs

The B extraction is missing the transitive declarations:
- `ReplyDestination = Readonly<ChannelRef>` (`reply-routing.ts:6`) —
  `ChannelRef` lives in `src/mcpl/channel-registry.ts:673–676` (verified via
  `git show`), **not in the extraction**. The destination's `{serverId,
  channelId}` shape, which §2.2's "exact destination equality" turns on, cannot
  be cited from the B drop.
- `ReplyInput = AdmissionInput & { replyIntent? }` (`reply-routing.ts:7`) —
  `AdmissionInput` (and its `signal`) is in `inbound-admission.ts`, absent from
  the B drop. It is recoverable only by crossing into the **A** drop; I verified
  `src/mcpl/inbound-admission.ts` is byte-identical at both pins, so this is
  safe — but the brief does not say so.
- `src/mcpl/index.ts` is in neither drop.

Also worth recording for the implementer, because it is not in the brief and is
easy to get wrong: AF, not the host, sets `serverId` —
`reply-routing.ts:135` `Object.freeze({ serverId: input.connection.connectionId,
channelId: channel.value })`. A host authorize comparing `destination.serverId`
against a configured server id will refuse everything.

**Fix:** either extend the B extraction with `channel-registry.ts` (ChannelRef),
`inbound-admission.ts` and `mcpl/index.ts` and re-issue SHA256SUMS, or add an
§1 note that `AdmissionInput` is cited from the A drop (identical at both pins)
and `ChannelRef` is `{serverId: string; channelId: string}` at
`channel-registry.ts:673–676`.

### MEDIUM-8 — the "current host reply ceiling" depends on an I7-gap seam, unflagged

§2.2 lists the ceiling check as deliverable now. Its only input path is
`TrustedRecipientRead.lookup(key, channelId): VerifiedScope | undefined`
(`src/recipient-admission.ts:38–41`), whose own doc comment says "No
implementation/default verifier exists here. Versioned doubles may supply
opaque scopes; real constructors/accessors wait for the reviewed integration"
(`:34–36`). T1:284 likewise requires "add current scope-generation check in
**host authorize**" — deferred to T2/I7. §2a says I7 is a gap "for dynamic
claims", which under-states it: the *static* ceiling check is also stub-only.

**Fix:** state in §2.2 that the ceiling is evaluated through `trusted.lookup`
with versioned doubles only, and that T1:284's scope-generation check is
explicitly deferred to T2/I7.

### MEDIUM-9 — the reply oracle in-repo is not reconciled with §3

`check-scope-contract.mjs` is the repo's declared outcome oracle for
`newBinding` / `reply` / `privateNotice` / `destination` (`:46,184,188–194`).
§3 and §4 never mention it; it is wired to no `package.json` script
(`c353e1c8:package.json:20–31`), so nothing runs it. Two concrete mismatches
to resolve before .4's stub tests are trusted:
- `:194` `destination: { connection, channelId, mappingId }` — B's
  `ReplyDestination` has no `mappingId`.
- `:40` `enums.lane = ['channels/incoming','push/event']` — the oracle does not
  model `inference/request` at all (cf. MEDIUM-4).

**Fix:** either name `check-scope-contract.mjs` as a .4 control and reconcile
the destination shape, or state in §3 that it is a T1/T2 oracle out of .4's scope.

### LOW-10 — citation precision

- §2a `:67` cites "T1:72 staging" for `replyChannels`. T1:72 is the superseded-shape
  paragraph; the row naming `replyChannels` is **T1:82**. Cite `T1:70–82`.
- §1 `:20` labels host `:215–260` as starting at "intent". The intent paragraph
  is `:222–231`; `:215–221` is connector responsibility. See also MEDIUM-4 on
  the range's lower bound.
- §1 `:19` ".4 input row" reproduces `f-host-h-brief.md:118` **accurately**
  (`Host:233–260 and T1:283–284 B requirements; I4` / `.2a/.3 results, I6 …,
  I7/T2 …`). Resolver `:233–239`, authorize `:241–249`, failure `:251–260` all
  verified exact.

### LOW-11 — the A extraction is not pinned at a verifiable digest; §4's gate digests have no locator

- §1 `:23` gives the A drop with **no per-file shas**, unlike the B row `:24`.
  Its `SHA256SUMS` digest is `1a8406fa18e4f2b5c777ae773c68772eacea50a5a6c2f0882bd0e1fa6ffec2b2`
  (unrecorded in the brief); its `PROVENANCE.txt` also omits the method/extractor/date
  that B's records. Both drops verify today, so this is hygiene, not corruption.
- §1 `:22` and §4 `:104–107` cite ten bare truncated digests (`25bea49a`,
  `27d354e4`, `825788d1`, `6b426785`, `7f0e4849`, `fde6a4bf`, `7ce7d6b6`, plus
  the docs-contract/naming dispositions) with no path or locator. None resolve
  as git objects in the host repo (`git cat-file -t` → "Not a valid object
  name"), so an implementer cannot verify any of them, and §4's gate pins are
  unenforceable as written. Add locators.

---

## What checked out (no finding)

- **Pins and ancestry.** All of `2c396622 → a316200c → bff8c46e → d7154d79 →
  c353e1c8` verified as a real ancestor chain (`git merge-base --is-ancestor`).
  §1's ".3 runtime src = c9f0991d" is correct: the last commit touching `src/`
  at the .3 pin is `c9f0991d`; `c353e1c8` is docs+fixture only.
- **Both contract extractions.** `sha256sum -c` passes for both, every per-file
  digest in §1 `:24` matches, the `SHA256SUMS` digest `4a1aa6b9…` matches, and
  all 11 files are **byte-for-byte identical** to `git show <pin>:<path>` in
  `/home/orgasmic/src/agent-framework`. Gate `contract-integrity`, exit 0.
- **Signatures the brief needs that *are* present.** `ReplyAuthorityConfig`
  with `timeoutMs` / `resolve` / `authorize` (`reply-routing.ts:8–16`);
  `ReplyDestination` (`:6`); literal-`true`-only (`:15,137`, `docs:21`);
  separate B deadline that cannot change the admission verdict (`:9–10`,
  `docs:33–42`); `signal` bounding via the derived controller (`:118–127`);
  64-pending overload → private (`:112`); private fallback
  (`privateReplyBinding` `:69–71`, `privateReplyNotice` `:27`); all-causes
  shared-turn approval (`turnReply` `:73–79`, `docs:70–75`); re-authorize the
  **frozen** destination with no resolver rerun (`:136–140`, `docs:74–75`);
  bounded exception diagnostic with no exception content (`:96–104`,
  `docs:48–51`); explicit tools separate (`docs:86–88`). A-side
  `isReplyIntent` is a **required** config member
  (`inbound-admission.ts:24,46–48`) and is already wired at .3
  (`recipient-admission.ts:98`).
- **Soma's §2a conditions** each hold against the sources: superseded-only
  `replyChannels` (T1:70–82, host:96); failure never changes A's verdict and has
  no default destination (`reply-routing.ts:9–10,110–112,140`,
  `docs:41–42,67–68`); literal-true synchronous (`:15,137`); reconnect to the
  same frozen destination with no replay (`:136–140`, `docs:70–79`); explicit
  tools separate (`docs:86–88`); I6/I7/.2a as real gaps (H brief `:102,103,106`).
  The `admission-only` install gap (HIGH-1) is the one place §2.2 drifts from §2a.
- **Feature-off reuse** (§2.4) is sound: `test/recipient-feature-off.probe.ts:33`
  already asserts `Object.hasOwn(server, 'replyAuthority') === false`.
- **Separate A/B timeouts** are testable at the compiler seam today:
  `static-policy.ts:11` `admissionTimeoutMs` vs `:17`
  `automaticReplies.timeoutMs`.
- **Commit trailer** in §4 matches repo practice exactly: `Vasyl Dovhanchyn
  <…@users.noreply.github.com>` + `Co-Authored-By: GPT-6.1-Sol
  <noreply@openai.com>` on all four .3 commits.
- **§1/§6 estimate provenance** is honest: 8–12 h is the H brief's own table
  value (`f-host-h-brief.md:26`, note at `:259`), not an invented figure.

## Verification Gates

| Gate | Command | Outcome | Log |
| --- | --- | --- | --- |
| `contract-integrity` | `bash $ORGASMIC_EVIDENCE_DIR/verify-contracts.sh` | **exited:0** — brief sha OK; both SHA256SUMS OK; 11/11 files byte-identical to `git show` | `gate-contract-integrity-01M6858ZV2A34SJYV9TPVFARRY.log` |

Unwrapped read-only probes (no receipt, reproducible): `git show`/`grep` over
`d7154d79`, `bff8c46e`, `c353e1c8`, `c9f0991d` in the two host worktrees, and
over `29edcf8b`/`869bb3ad` in `/home/orgasmic/src/agent-framework`;
`git merge-base --is-ancestor` for the ancestry chain; `cmp` for the
extractions. No builds, installs, worktree edits or network.

## Unmet criteria / test gaps

Missing from §3 (each needs a cell, and the first four a mutant):

1. `admission-only` → `replyAuthority` absent (HIGH-1). Mutant: install B
   whenever a policy exists.
2. `inference/request` and channel-less `push/event` → resolver returns `null`
   / is not consulted (MEDIUM-4). Mutant: resolver reads `item.channelId`
   regardless of lane.
3. `replyIntent` absent, or present but failing `isRecipientReplyIntent`
   (wrong version, extra member, accessor) → `null`. Mutant: resolver trusts
   `replyIntent.channelId` unvalidated. `recipient-policy.test.ts:296–300`
   covers the predicate itself but nothing consumes it for routing yet.
4. `destination.serverId !== input.connection.connectionId` → refuse
   (MEDIUM-7). Mutant: compare against a configured server id.
5. B's 64-pending overload → private: §2.3 names "overloaded" but §3 has no cell
   (`reply-routing.ts:112`).
6. `reportReplyFailure` throttling — at most once per 60 s per connection, shared
   across authority/consumer faults **and across reconnects**
   (`reply-routing.ts:96–104`, `docs:48–51`). §3 asserts the notice but not the
   window.
7. Compile-time assignability of the host's compiled `replyAuthority` to the
   pinned `ReplyAuthorityConfig` — the B analogue of
   `test/stubs/f-host-3/v1/typecheck.ts:9`. Needs a pinned `b-contract.ts`
   stub; §2.4 asks for file:line+sha citation but §3 has no control enforcing it.
8. Unbound automatic prose on a B-opted connection → private notice, not a
   guessed destination (MEDIUM-5, `docs:90–94`).
9. Re-baselined .3 controls: `run-mutants.mjs:24` becomes vacuous; the v1 stub
   needs a v2; `check-scope-contract.mjs` needs a disposition (MEDIUM-9).

**Estimate:** 8–12 h is the H brief's inherited slice value and is defensible
for §6(a) *as the brief currently describes it*. It does **not** cover the
un-budgeted work this review surfaces: the four `recipient-admission.ts`
changes (HIGH-2), a v2 stub plus `b-contract.ts`/typecheck, the stub-of-B
harness for two-connection/reconnect/revocation/shared-turn, nine added
cells and five added mutants, and the unbound-post inventory. Expect the
upper bound or above once §2 is corrected; re-state the estimate against the
amended scope rather than carrying the table value forward.

## Residual risk

- **Unverifiable from the supplied inputs:** the .3 review dispositions
  (`25bea49a`, `27d354e4`, `825788d1`), "docs-contract FAILED (declared filename
  false positive, EC6WW)", "naming PARTIAL", and §4's manifest/lock/toolchain
  digests. All treated as claims (LOW-11).
- **Extraction-bounded:** whether AF gates the reply consumer by lane
  (MEDIUM-4) lives in `server-connection.ts`/`framework.ts`, outside both
  drops. The host-side `null`-for-non-channel rule is the fail-closed answer
  regardless, but the AF-side behaviour stays unproven until I6.
- **Structural:** every §3 B-behaviour result is a statement about the host's
  stub of B (MEDIUM-6). I6/I7/.2a remain MISSING, so §6(b) is correctly
  unclaimable and no notice/delivery/runtime-binding claim can be made.
- Byte-for-byte identity of the drops was verified **today** against a local
  AF clone; nothing pins that clone itself.

## Fix directions (required edits before dispatch)

1. §2.2 `:41` → "installed iff the compiled policy's `automaticReplies !==
   false`"; add the `admission-only` control (HIGH-1).
2. New §2 item authorising and scoping the .3 changes: `reply` on
   `CompiledRecipientAdmissionPolicy`; `replyChannels → reply` in a **v2** stub;
   `replyIntent` emission from `admit`; replacement of the
   `recipient_reply_authority_pending` throw. Disambiguate "the .3 interim
   refusal" by naming which of the two refusals is meant, and list the
   re-baselined .3 controls (HIGH-2).
3. Move the equal-string-descriptor cell to PENDING-on-I6; restore host:238–239
   and host:244–246 to §2 (HIGH-3).
4. Widen the §1 host pin to `:207–260`; add the lane / channel-less `null` rule
   and its test+mutant to §2.1/§3 (MEDIUM-4).
5. Add the unbound-automatic-prose inventory to §2, or its discovery to §5
   (MEDIUM-5).
6. Label all §3 B-behaviour cells stub-of-B; pin `privateReplyNotice`'s literal
   string by sha rather than importing it (MEDIUM-6).
7. Extend the B drop with `channel-registry.ts` / `inbound-admission.ts` /
   `mcpl/index.ts` and re-issue SHA256SUMS — or add the §1 note that
   `AdmissionInput` is cited from the A drop (verified identical at both pins)
   and `ChannelRef` from `channel-registry.ts:673–676`. Record that AF sets
   `serverId` from `connection.connectionId` (MEDIUM-7).
8. §2.2: say the ceiling runs through `trusted.lookup` with versioned doubles
   only, and defer T1:284's scope-generation check to T2/I7 (MEDIUM-8).
9. Give `check-scope-contract.mjs` a disposition in §3/§4 (MEDIUM-9).
10. Fix the T1:72→T1:70–82 and host-`:215` citations (LOW-10); pin the A drop by
    per-file sha and give §4's digests locators (LOW-11).
11. Add the nine missing cells and five missing mutants, and re-state the
    estimate against the amended scope.
