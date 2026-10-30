# Review: TASK-KY73N T1 — generic admission scope contract

Head reviewed: `60b506c6754f811b53c1d011a3b7067033297bc4` on frozen parent
`a316200c4781a844d6109a6f8c812c3be99e20b0`. Diff is exactly three added files
(327 + 260 + 1372 lines): the contract doc, the fixture, the checker. No
production code, no dependency/lockfile change, no threaded-typing work.

## Verdict

**APPROVE.** The contract is substantively correct on the hard parts the brief
names, the API-gap table is accurate at the pinned SHAs (12/12 ranges I opened
matched their claims), and all four acceptance bullets are met. Findings below
are MEDIUM completeness/hygiene items for T2 to close, not blockers.

What I confirmed positively, because these were the widening risks:

- **Non-enumerable read sources are genuinely covered.** `joinedReadable` admits
  a post-startup join with no startup enumeration (doc:105-107; case
  `a04-joined-after-startup`), and `dmUsers` holds *user identities* checked
  against `peerId` with `authorId === peerId`, so a first-contact DM channel id
  that did not exist at startup is admitted on identity alone (doc:108-110;
  `a04-first-contact-peer-dm`). The checker structurally enforces the identity/
  channel distinction (`check-scope-contract.mjs:82`), and
  `dynamic-dm-channel-is-not-user` / `dynamic-unpermitted-dm-user` prove an exact
  allowed *channel* id cannot substitute for a permitted user.
- **No accept-every-registration.** `registrationAccepted` and `scopeVerified`
  are independent inputs; `dynamic-arbitrary-registration` (ACK with no verified
  sidecar) denies everything. This matches AF reality: `channel-registry.ts:1668`
  accepts any nonempty id, so the ACK really is not authority.
- **Claimed parent/label/metadata are structurally inert.** `claimedParent`,
  `claimedLabel` and `interveningDestination` exist in the fixture schema and are
  read by *nothing* in the oracle (`check-scope-contract.mjs:143-173`). The
  oracle therefore cannot be fooled by a claim even in principle — the right
  design for this control, and stronger than asserting case-by-case.
- **Read/store vs wake vs reply are separate in the right direction.** `store`
  omits `authorHasAccess`, `wake` requires it (`:158-160`), so
  `t02-author-access-storage-only` stores without waking — exactly what the
  parent fixtures spec mandates (`generic-recipient-fixtures.md:133`: "must not
  narrow ingress storage below the Mapping X read ceiling"). The DM-only
  author=counterpart conjunct lives in the read selector, matching the old
  oracle's DM-lane-only check at `host-recipient-protocol.ts:114-116`.
  `t02-reply-only-denied` proves a reply denial does not deny store or wake.
- **The ACK-vs-scope ordering hazard was anticipated.** AF answers
  channels/register *before* reconciliation to avoid RPC deadlock
  (`channel-registry.ts:1679-1682`), and `registerEntry` (`:1570`) makes the
  channel an eligible ingress target immediately. Doc:171-172 and :245-246
  ("missing sidecar means no protected channel use, even if ordinary registry
  registration succeeded") close that window instead of widening it.
- **The push lazy-registration hole is correctly identified.**
  `channel-registry.ts:1899-1900` documents that discord's DM case
  lazy-registers via `ensureChannelRegistered` on push/event; doc:172-173 and
  `dynamic-push-before-registration` forbid that path from supplying scope.
- **Old-oracle citations check out.** Doc:26-28 attributes lines 104-111
  (readable access + non-self provenance), 113-118 (permitted DM user +
  matching author), 120-123 (joined readable public/private) and 125-127
  (addressed wake separate from storage) correctly, and `writeChannels` is
  indeed only shape-validated (`:82-83`), never a storage ceiling.

### API-gap conclusion: "AF-scope plumbing: yes, narrowly" is correct

I opened every range in doc §4 at both pins. All accurate, including the two
load-bearing ones: `inbound-admission.ts:21-28` shows `bind(key) => {connection,
admit}` and `:17-19` shows `AdmissionInput` carrying only `{connection, lane,
item, signal}` — there is no registration view, lineage or scope evidence
anywhere in the host-facing surface; and `reply-routing.ts:15` shows `authorize`
is **required** at pin B, so doc:18-20's correction of the parent design's
"optional" claim is right (and 27a32127 is an ancestor of 29edcf8b, so this is a
forward pin, not a divergent tree).

I checked the simpler no-AF-change options myself and agree none works. The host
cannot synchronously answer "is this channelId an accepted, currently-registered
destination on exactly this K, with this lineage" at admit time *and again after
awaits*: `getDescriptor` (`channel-registry.ts:2411-2413`) sits on a
TypeScript-`private` `framework.channelRegistry`, gives no generation identity
and no epoch binding; the `mcpl:channels-register` trace (`:1689`) is
asynchronous and unordered against admission; and initialize attestation
(`inbound-admission.ts:147-155`) is static at handshake so it cannot cover
post-startup joins. The proposed read-only `lookup`/`current` view plus a
host-owned projector keeps authority production in the host's configured
verifier and gives AF only storage + lifetime of an opaque sidecar — I found no
new authority hole in it, and the explicit "never read a verified sidecar from
persisted rows" (doc:253) and "read view carries no mutation/opening/
subscription authority" (doc:249-250) close the two I went looking for.

### Owner-decision classification (brief item 4)

I found no owner decision smuggled in as an engineering call, and no engineering
call inflated into an owner gate. The author-access wake conjunct is
pre-approved by `generic-recipient-fixtures.md:133`, not new. Conservative
invalidation of equal replacements is pre-approved by T03. The conditional
AF-scope slice is already provisioned at `generic-recipient-host.md:708`. The
one place where a decision may be *missing* is MEDIUM-1 below.

## Findings

**MEDIUM docs/design — `docs/design/generic-recipient-scope-contract.md:49-56`:
Mapping X is specified for the `read` selector only; the wake side is asserted to
be preserved but is not made expressible.** Doc:49-51 pins Mapping X as
`joinedReadable:true` + complete `dmUsers` + `subConversations:true` for *read*,
then doc:52-53 says "Wake and reply remain independently explicit; neither
inherits the read selector", and doc:44-45 says "Empty lists mean deny, not all."
But old wake was *also* non-enumerable: `host-recipient-protocol.ts:122` sets
`addressed` from `mentionIds.includes(ownUserId)` in **any** old-readable joined
channel, with no channel list, and `:117` derives DM wake from per-message
`dmAuthorizedPeer`, which has no config counterpart at all. So the same "an exact
list cannot faithfully cover this" argument the doc makes for read applies
verbatim to wake, and the doc never says it. Failure scenario: an operator
performs a faithful-looking migration populating `read` per Mapping X and leaving
`wake.channels` as the old `wakeChannels` list with `wake.joinedReadable:false`
and `wake.dmUsers:[]`; every mention in a newly joined channel and every
authorized-human DM silently stops waking the agent — contradicting this doc's own
claim at :133-134 that "authorized human DMs retain their existing addressed/wake
behavior". The fixture encodes exactly this shape (`wake.joinedReadable:false`,
`wake.channels:["mailbox/active"]`) and `dynamic-addressed-join-read-only` asserts
an *addressed* post-startup join does not wake. That case is correct as a
selector-separation demo, but there is no counterpart case or clause showing what
wake must contain to preserve old behavior. Per
`generic-recipient-host.md:413-418`, narrowing permissions is an escalation
trigger, so this is either a missing Mapping X clause for `wake`/`reply` or a
missing owner question — the doc currently does neither.
*Fix:* add a Mapping X clause for the wake selector (`joinedReadable:true` plus
the authorized-human `dmUsers` set, with addressedness still doing the gating),
and a positive fixture case: addressed mention in a post-startup join under a
wake-side Mapping X → `store:true, wake:true`. If the intended answer is instead
"wake narrows", raise it as an owner question.

**MEDIUM docs/design — `docs/design/generic-recipient-scope-contract.md:230-241`
vs `:86-88`: the projector's only named evidence input is the detached
descriptor, which both this doc and the parent design declare non-authoritative.**
Doc:86-88 says a claimed parent "in message metadata, **address**, label,
channel-id grammar or stored history cannot create/replace this record", and
`generic-recipient-host.md:387,399-401` says descriptor `.address`/`.metadata`
"neither creates authorization". But the proposed projector signature is
`(key, candidateRef, detachedDescriptor, signal) => verifiedScope | deny` — the
descriptor (whose `address`/`metadata` members are `types.ts:1092,1095`) is the
only server-supplied evidence AF hands it, and doc:64-66 explicitly declines to
invent any capability, initialize offer or metadata member that would carry the
authenticated scope facts separately. Failure scenario: a T2/T3 implementer reads
"only a configured verifier consuming authenticated adapter evidence may produce
`verifiedScope`" (:235-236), has no other channel available, and resolves it by
reading the claimed parent out of `descriptor.address` — precisely the
claimed-parent widening the acceptance criteria forbid, while still passing every
fixture, since the fixtures model `parentVerified` as an already-settled boolean
and never model where it came from. The contract is non-constructive at exactly
the point where the widening risk lives.
*Fix:* name the evidence path in one paragraph. Either (a) state that descriptor
contents from the *attested protected* binding are admissible by provenance
because the adapter executable and account are operator-authenticated, and that
the claimed parent is only ever consumed as a key into `lookup()` over
already-accepted registrations (never as the relationship proof); or (b) name the
separate authenticated carrier. Then add a fixture field distinguishing
"parent relationship asserted in descriptor data" from "parent relationship
verified against native facts", with a negative where only the former holds.

**MEDIUM test — `test/fixtures/generic-recipient/check-scope-contract.mjs:1`:
the checker is not wired into any runner, against project convention.**
`package.json` `test` is `bun test`, which discovers `test/*.test.ts`; a
`.mjs` under `test/fixtures/` is never collected, and
`grep -rn 'generic-recipient'` over all `*.json|yml|ts|js|mjs|toml` outside the
fixture directory returns nothing. Every other standalone checker in this repo is
explicitly wired: `scripts/host-starts.mjs` and `scripts/pack-installs-alone.mjs`
and `scripts/lockfiles-agree.mjs` are invoked from `.github/workflows/ci.yml:61,123,150`
or npm lifecycle hooks. Failure scenario: a later commit edits `scope-cases.json`
or the doc; CI stays green, the SHA-256 the doc pins in prose at :265 silently
goes stale, and the doc/fixture coherence this whole slice rests on is unverified.
Mitigating: the dispatch-gate wrapper ran it at this commit, and the parent
commit's `policy-shapes.json` is likewise unconsumed, so fixture-before-harness is
an established pattern here.
*Fix:* a three-line `test/scope-contract.test.ts` that imports the module (it
self-validates on import at `:257`) and calls the two flag paths, or a `ci.yml`
step next to the existing `node scripts/*.mjs` ones.

**MEDIUM docs — `docs/design/generic-recipient-scope-contract.md:43-45`: the
operator-facing configuration surface is left unmapped and explicitly disclaimed,
while the pinned fixture still encodes the superseded shape.** The doc defines
`read`/`wake`/`reply` selectors each carrying `channels`, `joinedReadable`,
`dmUsers`, `subConversations`, then says "This is documentation vocabulary, not
new recipe fields or an exported TypeScript type." Meanwhile
`generic-recipient-host.md:93-96` still declares `readChannels: string[]` /
`wakeChannels` / `replyChannels`, and the unchanged pinned
`test/fixtures/generic-recipient/policy-shapes.json` still asserts that exact
shape. `readChannels: string[]` cannot express `joinedReadable`, `dmUsers` or
`subConversations` at all, so the provisional recipe surface *must* change — but
the doc neither says so nor notes that the pinned fixture's policy block is now
superseded. Failure scenario: T2 ("add dynamic scope compile/bind coverage") has
no defined configuration shape to compile and invents one, and two public
fixtures in the same directory describe two incompatible policy vocabularies with
no cross-reference. Note the semantics also *narrow* the provisional field: the
new `joined()` conjunct (doc:105-106, oracle `:146`) requires membership even for
an explicitly listed channel, so a listed-but-not-joined readable channel is now
denied. That is parity-preserving against the old pair (which required
`access.joined !== true` → refuse, `host-recipient-protocol.ts:120`) and so needs
no escalation, but it is a silent change to the provisional surface and should be
stated.
*Fix:* add a short mapping subsection: provisional field → selector member, an
explicit note that the exact-list-only shape is superseded and why, and a
sentence that `policy-shapes.json`'s policy block predates this contract.

**MEDIUM hygiene — `test/fixtures/generic-recipient/check-scope-contract.mjs:194`:
the new public file hardcodes five non-neutral internal names, and the
neutral-naming scan exempts itself.** `const forbiddenNames = ['orgasmic',
'soma', 'tarn', 'alnold', 'alder']` is now committed to the tracked tree;
`grep -rniE 'orgasmic|tarn|alnold|alder|\bsoma\b'` over the whole repo excluding
`.git` returns **this file and nothing else**. So the artifact whose purpose is to
keep internal names out of public text is the sole tracked carrier of them.
Compounding it, `docsContract()` (`:247-256`) scans only `documentText` and
`fixtureText` — it prints "2 public files" while the commit added three — so a
machine-local path or internal id accidentally committed in the `.mjs` would not
be caught by its own gate. The doc is honest about the scope (:303 says "the new
document/fixture"), so this is hygiene, not a misrepresentation.
*Fix:* move the denylist out of the tracked tree (env var, or a gate-side list
passed via argv), then include the `.mjs` in the `neutral()` scan. If the list
must stay inline, encode it so the literals are not greppable and still scan the
file.

**LOW correctness/docs — `docs/design/generic-recipient-scope-contract.md:99-101`
vs `check-scope-contract.mjs:156-161`: the normative `Live(K,R)` and the "tiny
pure documentation oracle" disagree about lane grants.** Doc:99-100 folds
"configured lane permission and effective lane grant" into `Live`, which
`Read` and therefore both the automatic and explicit paths depend on
(`:121,:149`). The oracle instead puts `incomingGrant` only on the incoming
`store` (`:159`) and never consults `lane` at all, so an explicit reply on a
connection with no incoming grant is permitted by the oracle and refused by the
doc. The oracle's reading is the sensible one — an explicit tool should not need
`channels.incoming` — but the doc is the normative artifact, and the gate that
claims to validate "explicit expected outcomes against a tiny pure documentation
oracle" is validating a slightly different predicate. Relatedly, `publishGrant`
is never varied in explicit mode (all five T01 cases leave it `true`), so whether
the explicit path requires a publish grant is unspecified and untested.
*Fix:* move the lane-grant conjunct out of `Live` into the `Store` line, and
state the explicit path's grant requirement with one fixture varying it.

**LOW test — `test/fixtures/generic-recipient/scope-cases.json`: `joined` is
`true` in all 46 cases, and the `S.channels` exact-list branch is never the
reason a case is admitted.** `joined()` (`check-scope-contract.mjs:146`) has two
disjuncts; across all cases `joinedReadable` is the only one that ever fires
positively — `read.channels` holds `mailbox/reference`, which is only ever used
by `dynamic-unpermitted-dm-user` as a *DM* channel id. So doc:50-51's "explicit
channels can supplement an operator-approved ceiling" is unexercised, as is the
joined-false denial (a readable channel the account is not a member of, listed
explicitly → must deny per the narrowing noted above).
*Fix:* two cases — listed channel with `joinedReadable:false, joined:true` →
store; same with `joined:false` → deny.

**LOW test — `scope-cases.json`: `policy.automaticReplies` is `true` in every
case, so the feature-off branch of the oracle is untested.**
`check-scope-contract.mjs:161,169` gate `newBinding` and `privateNotice` on it,
and `automatic-reply opt-in` is one of the owner questions doc:321-322 lists as
still open. A case with `automaticReplies:false` on an otherwise-waking input
(expect `store:true, wake:true, newBinding:false, reply:false, privateNotice:false`)
would pin the opt-in-off behavior and distinguish it from a reply denial.

**LOW test — `check-scope-contract.mjs:149-152`: sub-conversation lineage is
modelled one level deep only.** `parentKind` is constrained to `['joined','dm']`
(`:30`), so the doc's "recursive `Select(S,parent,item)`", "all ancestors must be
readable, current and permitted" and "reject cycles" (:111-116) have no fixture
witness. Acceptable for a snapshot oracle, but the multi-level and cycle rules
are currently prose-only and will need a T4 witness.

**LOW test — `check-scope-contract.mjs:154-167`: no fixture models the post-await
recheck *inside* admission.** Doc:184 requires "check current registration and
scope generations again after every await" and doc:101 requires "same
registration/mapping generation before and after asynchronous admission" — AF
really does this at `channel-registry.ts:1946` (re-reads the entry and compares
`entry.descriptor !== target` after `await admission.admit`). Every `send*` field
models the *send* await; nothing models a generation change landing between
`admit` entry and verdict. A `admissionRegistrationCurrent` input with a
`store:false` expectation would cover it.

**LOW test — `check-scope-contract.mjs:82`: the identity/channel invariant is
one-directional.** `dmUsers` entries must start with `sample-`, but nothing stops
a `channels` list from containing a `sample-`-prefixed user-shaped id, so the
inverse confusion (user identity smuggled into a channel list) is unguarded by
the schema. One extra assertion closes it.

**LOW test — `check-scope-contract.mjs:203-206`: `rejectsMutation` accepts any
throw.** `assert.throws(() => checker(changed), 'mutation must be rejected')`
passes if the checker throws for *any* reason, so a mutation that trips an
unrelated `TypeError` instead of the intended assertion is indistinguishable from
real coverage. None of the 39 currently mis-fires — I read all of them — but the
suite will not notice if a future edit makes one vacuous. Asserting on the
expected message substring would fix it.

**LOW docs — `docs/design/generic-recipient-scope-contract.md:211-213`: the
rejected-alternatives list omits the trace tap.** It names "exposing the whole
mutable registry" and "interpreting connector-specific parent fields", and the
report additionally names private-registry lookup. The remaining non-AF option a
reviewer or implementer will reach for is subscribing to the
`mcpl:channels-register` / `mcpl:channel-descriptor-rejected` trace stream
(`channel-registry.ts:1689,1777`) to shadow-build a host-side registry. It does
not work — asynchronous and unordered against admission, no generation identity,
no synchronous `current(ref)` after awaits — and saying so in one sentence would
make the "yes, narrowly" conclusion self-contained.

## Verification Gates

| Gate / command | Outcome | Evidence |
| --- | --- | --- |
| `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | **PASS**, re-run by me: `46 cases`, `39 rejected mutations`, exit 0 | reusable receipt `gate-scope-fixtures-01M67EY1SPRN4ZC2RKTWSF4BSF.log`; independently re-run in this review |
| `node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | **PASS**, re-run by me: `2 public files; 13 rejected naming/path/id/credential controls`, exit 0 | reusable receipt `gate-docs-contract-01M67EY1YQW0322XZ30VAY0B96.log`; independently re-run in this review |
| `scope-boundary` (frozen parent, clean tree, allowed paths, fixture digests, commit identity) | PASS — receipt reused, not re-run | `gate-scope-boundary-01M67EY2438NAQY8WAX9H8M003.log` |
| Node runtime | `v22.23.3`, matches the implementer's reported runtime | — |
| Checker cwd-independence | PASS — runs from `/tmp` via `import.meta.url`; doc:293's "run from repository root" is advice, not a requirement | probe |
| AF API-gap spot-checks (brief asked ≥5; I did 12) | **all 12 ranges accurate** | `git show` at A `869bb3ad` / B `29edcf8b`, read-only, no fetch, no writes to the shared clone |
| B pin ancestry | `27a32127` (parent design's inspected B) **is an ancestor of** `29edcf8b` — forward pin, not a divergent tree | `git merge-base --is-ancestor` |
| Old-oracle citations | doc:26-28's four line attributions all match `host-recipient-protocol.ts:104-111,113-118,120-123,125-127`; `writeChannels` shape-only at `:82-83` | read `/srv/ci-fixtures/pr361-v1-8153e7b84442cc11` |
| Fixture field coverage | computed per-field value sets over all 46 cases; `joined`, `connection`, `automaticReplies` monovalued (see LOW findings) | probe script |
| Worktree state | clean before and after; no commits, no edits, no new tracked files | `git status --porcelain` empty |

Ranges I opened and confirmed: A `inbound-admission.ts:9-28,101-127,109-145,147-155`;
A `server-connection.ts:591-600,1146-1167,1550-1559,1618`;
A `channel-registry.ts:1568-1572,1653-1687,1703-1784,1887-1951,2411-2413`;
A `framework.ts:34974-34999`; A `types.ts:1078-1105`;
B `reply-routing.ts:6-20,72-78,106-140`; B `reply-admission.ts:28-44`;
B `framework.ts:34943-34953`; B `channel-registry.ts:5962-5986,6043-6045`.

## Unmet criteria / test gaps

No acceptance criterion is unmet. All four are satisfied: connector-neutral
contract doc with the predicate, ordering, fail-closed defaults and separated
permissions; a concrete API-gap list with exact file:line at both pins; public
synthetic fixtures covering A04, T01–T03 and new dynamic cases with per-case
store/wake/newBinding/reply/destination/privateNotice, including all three
required negative controls (`a03-bad-static-mapping`,
`dynamic-use-before-registration` + `dynamic-push-before-registration`,
`dynamic-claimed-parent-widening` + `dynamic-claimed-parent-overrides-actual`);
and no production code, live data, threaded typing or new infrastructure.

Test gaps, in rough priority for T2: wake-side Mapping X positive case
(MEDIUM-1); runner wiring (MEDIUM-3); explicit-channel-list positive and
joined-false negative (LOW); `automaticReplies:false` feature-off control (LOW);
mid-admission post-await recheck (LOW); multi-level and cyclic lineage (LOW);
explicit-path grant requirement (LOW).

## Residual risk

- The fixtures are snapshot assertions over *already-settled* booleans. They
  prove the predicate's shape, not that any adapter actually establishes
  `parentVerified`/`scopeVerified`/`nativeMappingValid`. The whole contract
  reduces to trusting an operator-authenticated adapter's native validation, and
  that is where MEDIUM-2's ambiguity bites — T3/T4 must qualify it against a fake
  native API.
- No old/new runtime storage-parity execution. `oldRead`
  (`check-scope-contract.mjs:174-179`) is source-derived from the frozen oracle
  and hardcodes `readJoinedChannels:true` (it never reads the boolean), which is
  correct for the Mapping X baseline and documented, but it is not an execution
  receipt against that pair.
- The proposed AF interface is a proposal. Its spelling, the projector's
  bounding, and the sidecar's lifetime all still need implementation and testing
  in the authorized AF fork plus a newly pinned composed integration artifact
  before any runtime claim.
- Every owner question in the parent design's §4 (transition/H1/H2,
  ACK/recovery, store/cutover, automatic-reply opt-in, external notice routing)
  remains open, correctly, and nothing here authorizes live activation.

## Fix directions

Ordered for a T2 follow-up; none requires reopening this commit's boundary.

1. Add the Mapping X clause for `wake`/`reply` plus a positive addressed-join
   wake case, or raise wake narrowing as an owner question (MEDIUM-1).
2. Name the projector's authenticated evidence path in one paragraph and split
   "asserted in descriptor data" from "verified against native facts" in the
   fixture schema (MEDIUM-2).
3. Wire the checker into `bun test` or `ci.yml` alongside the existing
   `node scripts/*.mjs` steps (MEDIUM-3).
4. Add the provisional-field → selector mapping subsection and note that
   `policy-shapes.json`'s policy block is superseded (MEDIUM-4).
5. Move the name denylist out of the tracked tree and scan the `.mjs` with its
   own gate (MEDIUM-5).
6. Move the lane-grant conjunct from `Live` to `Store`, and close the LOW
   coverage gaps (explicit channel list, `joined:false`,
   `automaticReplies:false`, mid-admission recheck, assert-throws specificity).
