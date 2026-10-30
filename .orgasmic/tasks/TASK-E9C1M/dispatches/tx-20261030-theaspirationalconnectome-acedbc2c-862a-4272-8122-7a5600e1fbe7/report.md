# TASK-E9C1M re-review 3 — F-host design, fix round 2

Reviewed `90fb9ef6b3a23341d143d99b7f716be1d847bf19` (parent
`af8e4da79e8b728904fc0a34fbd09091419c6967`, base
`2c39662289be4cd5a150222e8456ff5577ce7622`). Diff vs parent: 4 files, +268/−56
(`docs/design/generic-recipient-host.md`,
`docs/design/generic-recipient-fixtures.md`,
`test/fixtures/generic-recipient/{policy-shapes,old-reader-payloads}.json`).
Read-only: `git status --short` empty at start and end, no edits, commits or new
files in the worktree. Prior review `TASK-E9C1M-REVIEW2.md` (mine), worker report
`TASK-E9C1M-FIX2.md` (both in the private drop).

## Verdict

**APPROVE.**

**My own REVIEW2 MEDIUM 1 was factually wrong, and I confirm the correction
against the sources myself.** The patched frozen AF does refuse storage before
its handlers:

- `.../agent-framework/src/framework.ts:20588-20596` —
  `const decision = bridge.accept('push/event', origin);` then
  `if (!decision.store || origin.mcplChannelId !== \`slack:${...}\`) { responder?.respond({accepted:false,reason:'recipient envelope refused'}); return; }`
  **before** `await this.pushHandler?.handlePushEvent(...)` at `:20598`.
- `src/framework.ts:20726-20744` — itemwise
  `bridge.accept('channels/incoming', metadata)` with the same `!decision.store`
  refusal **before** `await this.channelRegistry?.handleIncoming(...)` at `:20741`.
- Compiled `dist/src/framework.js:18423-18433` and `:18534-18560` carry the same
  two guards.
- Source SHA-256 `c7c7e53ed9359e6a3834f9c6c1a6b09350559d16fc1c28ebcdb3ccdb636a31c8`
  (`sha256sum` on the oracle file) matches the in-repo manifest and the pinned
  claim; compiled `ab380ca5…` likewise.
- The host's own hook (`host/src/support-recipient.ts:24-25`) is indeed
  `shouldTriggerInference` → `.triggerInference`, i.e. wake-only. My round-2
  error was reading only that hook and the downstream AF queue handlers and
  concluding the *pair* had no storage guard.

So the frozen pair **has** a storage guard, the matrix cell at
`generic-recipient-host.md:400` is now correct, and the "loss of ambient
context" owner decision I asked for would have been an invented difference.
`host.md:457-458` correctly refuses to add it as a ninth decision.

Round-2 findings: **M1 corrected (not "closed" — refuted, with my error named
in the doc's favour), M2/M3/M4/M5 closed and independently re-verified, L1/L2
closed. M6 (branch history) is unfixed by explicit user direction** and is
restated below as a release constraint, not a new defect. Three LOW findings
remain. No HIGH, no MEDIUM.

## Findings

### LOW

**LOW `docs/design/generic-recipient-host.md:403` (docs/privacy, brief item 6):
the internal harness name survives in a PR-destined doc.** The matrix row label
is "Telegram/GitHub/**orgasmic**-style baseline peers". This is the only hit for
`/srv|review-drop|/home/|TASK-|internal handles` across all five committed files
(the other two `/home/` hits, `host.md:117` "timer/home/module" and `:229`
"locus/home/latest", are prose, not paths). It is not accidental: the worker's
own checker whitelists it — `check-doc-contract.py:33` asserts
`re.search('slack|telegram|github|orgasmic', line, re.I)` only appears in
sections 4 and 6 rather than forbidding it — so the green `docs-contract`
receipt does not cover this. Soma's history guard named "orgasmic-style wording"
explicitly as scrub-before-publication material; L1 removed the task ids and
review bookkeeping but left this. Impact: an internal deployment nickname in an
upstream `anima-research/connectome-host` PR. Fix: use a neutral descriptor
("outcome-sidecar-style baseline peers") and drop `orgasmic` from the checker's
allowance.

**LOW `docs/design/generic-recipient-host.md:338-341` +
`docs/design/generic-recipient-fixtures.md:124-126` (docs/test-coverage): the two
old readable sources are identity- and boolean-keyed, which an exact
`readChannels` list cannot express; the storage-parity rows only cover the static
case.** `host-recipient-protocol.ts:19-22` defines
`HostAccessPolicy = {readJoinedChannels: boolean, writeChannels: string[], dmUsers: string[]}`,
and `:116-126` stores (a) *any* joined readable channel when
`readJoinedChannels` is true and (b) any DM whose `access.dmPeer` is in the
*user* list `dmUsers` with `authorId === access.dmPeer`. Neither is a channel
set known at configuration time. §3 requires "an exact read-channel entry"
(`host.md:206`), so for a `readJoinedChannels:true` deployment Mapping X is not
completable by list authoring alone — it depends on the same unresolved dynamic
admission-scope integration as threads. The doc does say this in substance
(`:338-340` "Dynamic membership/sub-conversation changes must preserve coverage
too", `:393-395` "exact-list based and snapshotted; it does not by itself cover
newly discovered thread identities or newly joined readable channels … before
claiming thread/storage preservation"), so this is completeness, not error. But
A01–A03 are all static-list witnesses; only T02 carries a dynamic case, and
nothing names `dmUsers`. Fix: in Mapping X, name `readJoinedChannels:true` and
`dmUsers` as the two non-enumerable old sources and state plainly that storage
parity is gated on the owner-decision-3 integration; add an A04 row for a
channel joined after startup and a first-contact DM from a permitted `dmUsers`
peer (both: old stores/no wake, new must too under the agreed scope mapping).

**LOW `docs/design/generic-recipient-host.md:379-387` (docs): the thread-carriage
table names only `ChannelDescriptor.metadata` as the existing place for adapter-
local data.** `ChannelDescriptor` (AF `27a32127:src/mcpl/types.ts:1081-1099`) also
has `address?: Record<string, unknown>` ("Platform-specific address"), which is
the more natural home for an immutable native `(account, conversation, root)`
tuple, and `sameChannelTarget` (`channel-registry.ts:426`) already compares
descriptors. Harmless as written — the doc's point (no authoritative parent
field, no channel-id parsing, nothing frozen here) is correct — but naming
`address` would save an implementer a lookup.

### Release constraint carried from REVIEW2 M6 (not a new defect)

`9f153044` still carries the private drop path, the private projection SHA-256
and projection-derived cardinalities: `git grep -lE '/srv|review-drop|3574d888|U900000' 9f153044`
hits both design docs (the other hits are pre-existing upstream `test/*` files).
HEAD is clean. The worker did **not** rewrite history, under explicit user
direction, and documents the exact private contents in its report plus a
"do not push this history" restriction. That matches Soma's guard (publication
only as a fresh commit from the reviewed final tree on the public base). I
record it so the constraint is not lost: **the range `2c396622..90fb9ef6` must
not be pushed.** Verify any promotion with
`git grep -nE '/srv|review-drop|3574d888|9000000|orgasmic|TASK-' <range>`.

## Verification Gates

**Reused receipts (read, not re-run)** — both at this commit on a clean tree,
scripts read in full:

- `docs-contract` exit 0 — `gate-docs-contract-01M679WW1C3YDYTRTHYGE9MDSH.log`.
  `check-doc-contract.py` now path-checks **both** JSON fixtures as well as both
  docs (`:14-17`, closing the round-2 gap), forbids `task[-_]` and `opus`
  (`:19-20`), asserts all 18 original plus 8 new matrix rows (`:25-26`), the
  corrected policy oracles (`:45-56`), example provenance against the real
  fixture tokens (`:60-62`), the byte-sort manifest wording (`:63`), one owner
  list `1..8` with §7 adding no numbered item (`:68-71`), and the hour sums from
  the table itself (`:72-76`). It is a static specification check, not a
  behavioural receipt — and it deliberately permits `orgasmic` (LOW 1).
- `source-pins` exit 0 — `gate-source-pins-01M679WW6FV17AQW6EXW6BC07N.log`.
  `check-source-pins.py:26` now sorts by UTF-8 path-string bytes **and** `:30-33`
  recomputes every root independently via
  `find "$1" -type f -print0 | LC_ALL=C sort -z | … sha256sum`, asserting both
  agree with the manifest. The round-2 self-consistency defect is gone.

**Scoped read-only probes I ran** (no gate wrapper, no reusable receipt):

- **Brief item 1 (M1).** `sed`/`sha256sum` over the oracle AF
  `src/framework.ts`, `dist/src/framework.js`, host `src/support-recipient.ts`
  and `src/host-recipient-protocol.ts` — evidence quoted in the verdict above.
- **Brief item 3 (digests), reproduced independently.** A fresh shell
  implementation of the published algorithm text,
  `find <root> -type f -print0 | LC_ALL=C sort -z` → `sha256sum` per entry →
  `<sha>␠␠<rel-path>\n` → `sha256sum`:
  - AF `src`: 109 files → `6678e5f0043bec7304ea7a3c17ff08e3d049954b91b2dc5ab631207bc88204c1` ✓
  - AF `dist`: 437 files → `5cfd5889ae6742ba5cc1f892e974f6233be5293f3d1cf6e8e17d59eb50dcc4b3` ✓
  - AF `package.json` `19589d2d…`, CM `src` 53 → `8ccfe280…`, CM `dist` 893 →
    `d3eb9c3f…`, and all seven Chronicle single-file roots ✓
  Both recorded AF digests are exactly the values I computed in round 2 under
  plain lexicographic ordering, so M4 is closed in the direction I named: the
  manifest was recomputed, the text now says "sorted by raw byte value
  (LC_ALL=C), NOT by Path components", and doc `host.md:503-508` repeats it.
  Also re-verified: every `fileSha256` entry for AF/CM/Chronicle entry points and
  natives, both lock hashes (`0235927…`/`d03991a…`), the artifact manifest
  `8153e7b8…`, and the stale-lock claim (`package-lock.json` resolves AF 0.21.0 /
  CM 0.13.0 against installed 0.22.2 / 0.14.1).
- **Brief item 2 (read/storage/wake, Mapping X, A01–A03).** Full read of
  `host-recipient-protocol.ts:78-131`. Old predicate confirmed line by line:
  envelope/version/stamp validation, `access.readable === true`, non-self
  consistent provenance, then joined `channel|private_channel` with
  `readJoined` **or** `access.kind==='dm'` with `dmPeer ∈ dmUsers` and matching
  author; `wakeAllowed !== addressed` refuses; return is
  `{store:true, triggerInference: addressed}`. So: valid ambient → store, no
  wake (**A01** correct); unreadable/invalid → `refuse` = `{store:false,
  triggerInference:false}` (**A02** correct); omitting an otherwise-readable
  channel from the new exact `readChannels` (`host.md:206`) gives new
  `store:false` vs old `store:true` (**A03** correct, and correctly labelled a
  bad-mapping negative control). `writeChannels` is shape-validated (`:82-83`)
  and never consulted by `accept`, so `host.md:335-337`'s "the host validates
  `writeChannels`' shape but does not use that list to narrow this storage
  predicate" is right, and Mapping X keys on read coverage, not the write/wake
  list (`:342-347`). No ambient-loss owner decision is invented (`:457-458`
  states it is an implementation requirement, not a ninth decision). The only
  real difference I can still identify is the non-enumerability of the two old
  read sources — already stated in substance by the doc; see LOW 2.
- **Brief item 4 (exact Slack threads).** B at `27a32127`:
  `src/mcpl/reply-routing.ts:6-17` — `ReplyDestination = Readonly<ChannelRef>`,
  `resolve` returns `{channelId} | null`; `ChannelRef = {serverId, channelId}`
  (`channel-registry.ts:673-676`); `docs/reply-routing.md:27-30` — "Channels may
  represent registered thread channels; there is no new thread field, transport
  profile, capability negotiation or initialize extension". `ChannelDescriptor`
  (`types.ts:1081-1099`) has only `address?`/`metadata?` free-form members — no
  parent/scope field. D `a5b02a07:docs/context-only-delivery.md:96-109` matches
  the two profile-independent deltas verbatim. Sealed connector:
  `dist/src/server.js:538-545` is `reply_message` taking `channelId` + `messageId`
  → `sendMessage(channelId, content, {threadTs})` (exactly the cited `:539-545`),
  and `:1029-1038` is the automatic path — `frozenTarget = params.threadId`,
  refuse unless `/^\d+\.\d+$/`, re-fetch the parent and refuse if it is itself
  threaded, then send with that exact `threadTs` on the base channel. The frozen
  pair's carriage is confirmed end-to-end: `host-recipient-protocol.ts:62` →
  `{supportRecipientReplyRoot}` → AF `framework.ts:14085` →
  `channel-registry.ts:3114` injects `threadId` into the publish params.
  **Conclusion: the section is correct against B `27a32127` and D, invents no AF
  parent/scope API, and the gap is real** — the narrowed B destination has no
  thread field, the old threadId publish channel is gone, and no connector
  implements registered sub-conversations. Owner decision 3 (`host.md:428-435`)
  states it exactly: exact thread identity is mandatory, the owner
  authorizes/funds the missing integration or retains the old pair, engineers
  choose the mechanism — no engineering escalation. **It is correct that the
  host hours exclude it:** `host.md:650-654` says so in the table's preamble
  ("The unresolved native/dynamic-thread preservation integration in owner
  decision 3 is not silently priced as existing host work; agree its scope and
  revise estimates before adding it"), and the eight rows sum to the stated
  totals without it (recomputed: 10+10+8+8+8+8 = **52**, 16+16+12+12+12+12 =
  **80**; +fence 10–16 → **62–96**; +design 6–8 → **58–88** / **68–104**). My own
  estimate for the excluded work is below.
- **Brief item 5 (owner list).** `host.md:415-455`: one numbered list, regex
  `^\d+\. ` yields exactly `1..8`, no duplicates, no renumbering in §7
  (`:683-688` points at "decisions 1–8 in §4" and adds none). Each of 1–8 is a
  policy/funding call; §7's "Engineering calls" block keeps API wiring, error
  mapping, hashing, packaging, identity-response shape, runtime selection and
  slice execution out of owner scope. Nothing over-escalated, nothing
  under-escalated that I can evidence.
- **Brief item 6 (private material).**
  `grep -nE '/srv|review-drop|/home/|TASK-[A-Z0-9]{5}|orgasmic|soma|tarn|3574d888|9000000|task-e9c1m|task-hjv3j'`
  over all five committed files → only the three prose `/home/` hits and the one
  `orgasmic-style` label (LOW 1). No `Opus`/review-bookkeeping text remains.
  Commit identity `Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>`
  with the `GPT-6.1-Sol` trailer, no internal branch handle.
- **Fixture integrity.** `sha256sum` → `policy-shapes.json` =
  `fbef1ce744da35ec19082a78ddda0e1b2fd7d9f9a8692f0d477db83678d9214a` and
  `old-reader-payloads.json` =
  `8f70b5cdab01eacdfb207698179c154ce171412ed417ef630fa8f4c18a06aeb5`, both as
  cited (`fixtures.md:58`, `host.md:102`, `host.md:502`). The fixture was
  re-authored this round (new SHA); I re-read it in full — invented ids
  (`fixture-edge`, `sample-receiver`, `mailbox/*`, `inspect_entry`), invented
  sizes (700/900 ms, 3 channels, 15 cases), organized around grant/mask/
  deny-wins/admission-selection/no-leaf/overlay relations, with no value, key or
  structure from the private projection and none of its markers. M5 is closed
  properly: `host.md:107-113` now quotes the fixture's actual values
  (`fixture-edge`, `sample-receiver`, the three `mailbox/*` channels, 700 ms,
  900 ms, `wakeWithoutChannel:false`) and says explicitly that
  `endpoint-a`/`acct-example-a`/`room/one` are the harness, "not the source of
  the policy fixture". M3 is closed: `overlay-cannot-widen` now expects
  `{grantWidening:false, operatorNote:true}` with a separate
  `protected-overlay-replacement` → `configurationRejected:true`, which matches
  `src/mcpl-config.ts:296` (`OPERATOR_ONLY_OVERLAY_FIELDS` includes
  `enabledCapabilities`) and `:390-391` (push a note, continue), and
  `host.md:160-163` states the two stimuli apart. M2 is closed: `host.md:403`
  names both D deltas with the exact error shapes, cross-references §2, and
  `fixtures.md:127-128` adds U01/U02 with before-policy and no-field controls.
- **Private-citation honesty.** The four §4 report SHA-256s (`5c1b7aec…`,
  `842e508c…`, `cb13d22a…`, `3511e1c6…`) each resolve to a real file in the
  private drop, and the labels match their content types. No fabricated citation.

**Not run, by design:** no build, no test suite, no AF install, no connector
execution, no store open, no compaction/restart, no live ordering. The
deliverable is documentation plus two data fixtures; A01–A03/U01–U02/T01–T03 and
H01–H12/S01–S06 are specifications, and the doc claims no passes for them.

## Unmet Criteria / test gaps

- **Acceptance: met.** Design doc on the correct base with the host config
  surface, reply-authority hook, context-only profile, identity/attestation, the
  legacy-translator boundary, the conditional managed-launch fence with an
  explicit supported boundary preceded by the §5.1 synthetic old-reader/new-store
  plan, and the shared stub contract covering the O4 list. The manager's
  round-2 additions are all present: three separated matrix columns, the
  "no translator does not resolve the pr361 transition" statement
  (`host.md:330-336`), the purpose-built public fixture, the AF 0.31 exact-tarball
  requirement, and the read/storage/wake separation with A01–A03.
- **Opus review** remains a manager-owned gate; this review is not it, and the
  worker claims no receipt.
- Remaining test-spec gaps: no storage-parity row for a dynamically joined
  channel or a first-contact `dmUsers` DM (LOW 2); T02's dynamic-thread case is
  specified but explicitly unqualifiable until the scope mapping exists.
- No fixture or doc text is now unreproducible from the repo plus the named
  pins — the round-2 `inventoryAlgorithm` defect is gone.

## Residual Risk

1. **The exact-thread integration is unbuilt and unestimated in these hours.**
   It gates any preservation claim for the deployed pair, and owner decision 3
   is the only lever. My estimate: see Fix Directions.
2. Still validated against three unmerged AF pins. The doc pins B at
   `bf6e8e0e`; the current B is `27a32127` (a large rebase). I checked the
   exact-thread section against `27a32127` and the contract is unchanged in the
   relevant surface, but the composed A+B+D integration commit and the 0.31 fork
   tarball still have no identity, so §2's mapping table and the `file:vendor/…`
   instruction remain unexecuted. Re-pin B before implementation.
3. Branch history is unpublishable (see the release constraint above); the
   private drop remains mutable and host-local.
4. `orgasmic-style` ships in the doc and is whitelisted by the project's own
   doc gate (LOW 1).
5. The no-fence path still depends on an unexecuted probe — but its input
   manifest is now reproducible, which was the round-2 blocker.

## Fix Directions (ordered)

1. Replace `orgasmic-style` at `host.md:403` with a neutral descriptor and drop
   `orgasmic` from `check-doc-contract.py:33`.
2. Name `readJoinedChannels:true` and `dmUsers` in Mapping X as non-enumerable
   old read sources, state that storage parity is gated on the owner-decision-3
   scope mapping, and add an A04 row (channel joined after startup; first-contact
   DM from a permitted peer).
3. Mention `ChannelDescriptor.address` alongside `.metadata` in the carriage
   table.
4. Re-pin B to `27a32127` (or the composed integration SHA) when it lands.
5. Publication: fresh commit of this tree onto the public base; never push
   `2c396622..90fb9ef6`.

### My estimate for the owner-decision-3 gap (brief item 4)

Engineer-hours, focused tests included, excluding owner waits, AF ratification
waits and live qualification. This is **additional to** the 52–80 h F-host
figure, which correctly excludes it.

| Work | Hours |
| --- | --- |
| Agree the generic admission-scope contract (host↔AF↔connector): scope/predicate form replacing or augmenting exact-list `readChannels`/`replyChannels`, fail-closed defaults, registration-before-use ordering | 6–10 |
| Host: implement the scope mapping in config compile + A `bind`, with snapshot-free coverage of newly registered sub-conversations and DM counterparts; feature-off controls | 12–20 |
| Connector (F-Slack): immutable native `ThreadRef` capture, neutral sub-conversation registration/lifecycle (create, validate, expire, recreate, opaque connection-qualified ids), exact-thread publish, stale-mapping refusal | 16–24 |
| Qualification harness for T01–T03 against a fake native API, incl. the newly-discovered-thread case and transport rotation | 10–16 |
| Storage-parity re-qualification under dynamic membership (A01/A03/A04) | 4–8 |
| **Total** | **48–78 h** |

Critical path ≈ **30–48 h** (scope contract → host scope mapping → adapter
registration → T-row qualification), split roughly 22–38 h host-side and
26–40 h connector-side. **Schedule risk the doc should carry:** if the agreed
scope mechanism needs an AF-side change rather than host config plus connector
registration, add an AF slice (≈ 8–16 h) **and** a re-pin/rebuild of the
integration tarball, which serializes ahead of F-host.2a/.3/.4 rather than
running beside them.
