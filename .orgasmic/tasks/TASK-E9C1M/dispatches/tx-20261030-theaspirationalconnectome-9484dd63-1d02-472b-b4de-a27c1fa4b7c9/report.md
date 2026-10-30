# TASK-E9C1M re-review 4 — F-host design, fix round 3

Reviewed `4326620b2c34c0101960c7bdd5e480ae20e422d6` (parent
`90fb9ef6b3a23341d143d99b7f716be1d847bf19`, base `2c39662289be4cd5a150222e8456ff5577ce7622`).
Diff vs parent: 3 files, +169/−79 (`docs/design/generic-recipient-host.md`,
`docs/design/generic-recipient-fixtures.md`,
`test/fixtures/generic-recipient/policy-shapes.json`).
Read-only: `git status --short` empty at start and end; no edits, commits or new
files in the worktree. Prior review `TASK-E9C1M-REVIEW3.md` (mine), worker report
`TASK-E9C1M-FIX3.md` (both in the private drop; read in full).

## Verdict

**APPROVE.** All three round-3 LOWs are closed. Both re-pins are true as stated
and I reproduced them. The thread-gap reclassification is honest under Soma's
owner rule. Every number in the T table and the six combined totals recomputes
exactly, with no H/T double-charge. A04 and the T01–T03 negatives are correct
against the frozen predicate. The privacy grep is clean across all five
committed files. Three new LOWs remain; no HIGH, no MEDIUM.

## Findings

### LOW

**LOW `test/fixtures/generic-recipient/policy-shapes.json:1` (test-coverage):
the A04 authorized-human-DM wake control that the round's own rationale depends
on has no case in the public fixture, and no case anywhere in the file asserts a
positive wake.** `fixtures.md:131` requires "keep an authorized-human-DM wake
control to prove this is not a blanket DM wake veto", and `FIX3.md`'s disposition
names that control as the mitigation. But the fixture's 17 cases carry only
`mayWake:false` or no wake key at all — including the new `first-contact-permitted-dm`
and `joined-after-startup` (`policy-shapes.json:238-262`) and the pre-existing
`reference-no-wake` (`addressed:true`, `mayWake:false`). A conformant-looking
implementation that never wakes satisfies every `mayWake` assertion derivable
from this data. The old predicate does distinguish the case:
`host-recipient-protocol.ts:117-118` sets `addressed = human && metadata.dmAuthorizedPeer === access.dmPeer`
and then `:125-126` returns `triggerInference: addressed`. The normative text in
`fixtures.md` carries the requirement, so this is coverage, not error. Fix: add
an `authorized-human-dm` case (`senderKind:"human"`, `dmAuthorizedPeer` matching,
`addressed:true`, `expected:{store:true, mayWake:true}`) and assert it in
`check-doc-contract.py` alongside the two new ones.

**LOW `docs/design/generic-recipient-host.md:93` and `:209` (docs/contract
drift): `RecipientPolicyV1`'s `readChannels: string[] // exact opaque ids` and
§3's "For channel-bearing work require an exact read-channel entry" are stated
without any marker that T1 will replace or augment them, even though §4 now says
plainly they cannot express an approved requirement.** `:393-396` states the
exact-list surface "does not by itself cover newly discovered threads, joined
readable channels or first-contact `dmUsers` conversations", and `:700` schedules
T1 to "agree generic admission scope/predicate … for dynamic registrations, joins
and first-contact DMs". The deliverable's stated purpose is a frozen API "that
F-host and F-Slack … build against in parallel"; the one block an implementer
copies (`:86-101`, labelled `version: 1`) is the part known to be incomplete, and
nothing at that point of definition says so. Impact: parallel F-Slack work built
to the v1 exact-list read surface needs rework once T1 lands. Fix: one clause on
the `readChannels` comment and at `:209` marking the exact-list read surface as
provisional pending the §6 T1 scope contract, cross-referencing `:393-396`.

**LOW `docs/design/generic-recipient-fixtures.md:136` (docs/consistency): T02's
ingress negative "triggering author without native access → no storage/wake/new
binding" is unqualified and, read literally, narrows storage below the old read
ceiling that A01/A04 require parity with.** For the DM lane the old pair does gate
on the author (`host-recipient-protocol.ts:115-116`: `dmUsers.has(access.dmPeer)`
and `authorId === access.dmPeer`), so the negative is grounded there. For the
channel lane it is not: `:119-124` checks only `readJoined`, `access.joined`,
`access.kind`, plus author provenance/self (`:109-111`) — there is no per-author
platform-access test, so an otherwise valid ambient item stores. A new pair that
refuses storage on author access in the channel lane produces old `store:true` /
new `store:false`, which is exactly the shape A03 labels a "preservation
mismatch" (`fixtures.md:130`) — but T02 presents it as a required pass. Fix:
scope the author-access negative to establishing the reply binding/wake
("no new binding, no wake"), or state explicitly that it must not narrow ingress
storage below the Mapping X read ceiling, naming the DM-lane author check as the
one case where old refusal is already the behaviour.

### Observation (not a finding)

Old owner decision 3 also carried the clause "Any other changed status/conversation
behavior needs explicit disposition" (deleted at `90fb9ef6..HEAD`, host.md −169).
Its substance survives — `:316-318` retains disabled typing and requires separate
slice-C evidence before claiming enabled-typing equivalence, and `:767-768`
repeats it — and the §4 escalation rule (`:443-449`) subsumes it generically. But
that rule's examples are thread-output and lost-context only; enabling typing or
changing status behaviour is an observable-contract change and would now be read
off `:767` as an engineering call. One word ("status/conversation behaviour")
added to the escalation examples at `:444` would close the ambiguity.

### Release constraint carried forward (unchanged, not a defect)

`git grep -lE '/srv|review-drop|3574d888' 9f153044 -- docs test` still hits both
design docs; `af8e4da7` and HEAD hit only the eight pre-existing upstream
`test/*.test.ts` files. History was not rewritten, by explicit user direction.
**The range `2c396622..4326620b` must not be pushed.** Publication only as a
fresh commit of this final tree on the public base, re-scanning the full range.

## Verification Gates

**Reused receipts (read, not re-run)** — both at this commit, clean tree; both
scripts read in full:

- `docs-contract` exit 0 — `gate-docs-contract-01M67B1F9YP4MEJWW0FKZTW9D6.log`.
  `check-doc-contract.py:18` now **forbids** `orgasmic|soma|tarn|alnold|alder`
  case-insensitively across both docs and both JSON inputs (the round-3 whitelist
  at the old `:33` is gone), `:42` forbids private markers plus platform names in
  the policy JSON, `:69-71` asserts one owner list `1..7` with §7 adding none,
  `:92-93` asserts `owner decision 3` and `authorize/fund` are absent, `:95-96`
  asserts the four authorized repo names and the `C/E/F` engineering label,
  `:98` asserts both old pins are gone, `:99-100` asserts the eight thread
  negatives verbatim, and `:103-126` recomputes T, the two allocations, AF and
  all six combined totals in both fence columns from the tables themselves. It
  remains a static specification check, not a behavioural receipt.
- `source-pins` exit 0 — `gate-source-pins-01M67B1FF2QBYD8FZC1VH72AA3.log`.
  `:85-87` asserts the A-pin tree facts, `:60-67` the frozen storage guards and
  the wake-only host hook, `:69-71` the old predicate tokens.

**Scoped read-only probes I ran** (no gate wrapper, no reusable receipt):

- **Brief 1 — the three LOWs.**
  - LOW-1 closed: `host.md:429` now reads "Telegram/GitHub/outcome-sidecar-style
    baseline peers"; the checker's former allowance is replaced by a forbid at
    `check-doc-contract.py:18`.
  - LOW-2 closed: `host.md:366-371` names "**two non-enumerable-as-startup-channel-sets
    old read sources**", `readJoinedChannels:true` and `dmUsers` as **user
    identities** "not the channel ids of their future first-contact DMs";
    `:378-381` gates storage parity on the engineering scope integration "not
    owner authorization or static list authoring"; A04 added at `fixtures.md:131`
    with both sub-cases and two new fixture scenarios.
  - LOW-3 closed: `host.md:383` (carriage table) and `:390-391` (gap text) name
    `ChannelDescriptor.address` alongside `.metadata`, both as opaque, neither as
    authorization. Verified against the pin: `27a32127:src/mcpl/types.ts:1094-1098`
    is `address?: Record<string, unknown>` / `metadata?: Record<string, unknown>`,
    with no parent/scope/thread member anywhere in `ChannelDescriptor` (`:1081-1105`).
- **Brief 2 — the re-pins, reproduced independently in the AF ledger.**
  - `git rev-parse 1d990e41:src 869bb3ad:src` → both `3d21d6a67815d27b194366919fd824947977e2a2`.
    Whole-commit trees differ (`c9089ee4…` vs `40f63100…`); `git diff --stat
    1d990e41 869bb3ad` → `docs/required-inbound-admission.md | 7 ++++++-`,
    1 file, 6 insertions / 1 deletion. The doc's "runtime/source tree is identical
    … the only whole-tree delta is its admission documentation" (`host.md:46-48`)
    is exact, and correctly stops short of claiming tree equality.
  - `git merge-base --is-ancestor 869bb3ad 27a32127` → exit 0. "already on
    published A" (`host.md:44-45`) holds.
  - **B API claims at `27a32127` are correct.** `src/mcpl/reply-routing.ts:6-16`:
    `ReplyDestination = Readonly<ChannelRef>`, `resolve` returns
    `{readonly channelId: string} | null`. `:122-126` rejects any resolve result
    whose own keys are not exactly `['channelId']`; `:127` freezes
    `{serverId: input.connection.connectionId, channelId}`. `ChannelRef` is
    `{serverId, channelId}` (`channel-registry.ts:673-676`). So B carries **no**
    thread field — `host.md:385` ("B needs no outbound thread field") is right.
    `:128-131` re-evaluates `valid(connection, destination) && authorize(...) === true`
    and **never re-calls `resolve`**, and failure returns `privateWork()` with
    `privateReplyNotice` (`:27`) — confirming T03's "no re-resolve/base-channel
    fallback" and §4's reconnect rule. The binding key includes `transportEpoch`
    (`:90`), grounding T03's "previous epoch with equal visible ids".
- **Brief 3 — reclassification honesty.** Owner list is one numbered `1..7`
  (`host.md:450-477`); `regex ^\d+\. ` over §7 yields nothing and `:741` points
  at "decisions 1–7 in §4"; no stale "decision 3"/"ninth"/"eighth" anywhere in
  either doc. Each surviving item 1–7 is a contract/permission/recovery/scope
  call (1 removes H1 cross-pair support; 2 changes no-field effects; 3 changes
  loss/retry/operator recovery; 4 changes cross-epoch completion and external
  posting; 5 removes rollback; 6 chooses which connections may post externally;
  7 authorizes a new external publication route) — **none hides an engineering
  call.** In the other direction, the engineering plan does not hide a contract
  change: `:390-392` and `:400-406` keep the gap and its classification explicit;
  `:371-372` and `:398-399` forbid widening ("must preserve the same read
  ceiling, not widen it by accepting every registration or trusting a claimed
  parent"); `:407-412` states the escalation triggers; the AF-scope conditional
  stays inside the four named forks (`:706`). The force of the old item is not
  softened: the preservation gate survives as the G retirement condition
  (`:482-491`) and `fixtures.md:229-231` now also requires A04 and "resolved
  dynamic-thread scope coverage" for G, and the "retain the old pair" fallback
  survives as owner decision 1 (`:452-455`). Two residues: LOW 3 above, and the
  deleted status/conversation catch-all (Observation).
- **Brief 4 — arithmetic, recomputed with my own extraction, not the worker's
  checker.** H rows excluding F-host.1 (6–8) and F-host.6 (10–16): 10+10+8+8+8+8
  = **52**, 16+16+12+12+12+12 = **80** ✓, and the published shorthand
  `10+10+4×8` / `16+16+4×12` is right (4 rows at 8–12). T: 6+12+16+10+4 = **48**,
  10+20+24+16+8 = **78** ✓. Allocations: T1+T2+T5 = **22–38** ✓, T3+T4 = **26–40**
  ✓, and 22+26 = 48 / 38+40 = 78 ✓. All six combined rows match in both columns:
  (52,80)/(62,96); (100,158)/(110,174); (108,174)/(118,190); (58,88)/(68,104);
  (106,166)/(116,182); (114,182)/(124,198) — every fence cell is exactly base
  +10/+16, AF is added once at 8–16, the design row once at 6–8. Critical path
  30–48 h sits inside T's 48–78 ✓, and 30+8 / 48+16 = **38–64** ✓.
  **No H/T double-count.** Each shared-plumbing row carves out its H baseline in
  the row text: T2 "beyond H's static policy/binder; do not charge that baseline
  compile again" (`:700`), T4 "Extend H's existing transport harness … not a
  second full harness build" (`:702`), T5 "without repeating H's ordinary D
  persistence work" (`:703`); AF-scope says "do not count this twice in T"
  (`:706`). T3 is F-Slack connector work with no H counterpart (H is host-fork
  PRs only), and T1 is new. G stays at 8–12 h in H even though `fixtures.md:229`
  widened its evidence set, with the incremental qualification charged in T4/T5
  — the correct direction. Caveats, both disclosed in the doc: T4 prices an
  "extension" at the same 10–16 h as building the original harness (F-host.2a),
  and T2 (12–20) exceeds its own base F-host.3 (8–12); and "H + T" mixes a
  host-only H with a host+connector T (`:689-690` says so).
- **Brief 5 — A04 and the T negatives against
  `/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/src/host-recipient-protocol.ts`,
  read in full (`:94-128`).** A04 channel sub-case is correct: `:120-121` gates on
  `readJoined` **and** `access.joined === true` with no startup channel list, so a
  channel joined after startup stores; `:122-123` makes `addressed` mention-based,
  `:125-126` returns `store:true, triggerInference:false` for `context-only`.
  A04 DM sub-case is correct: `:113-116` keys on `dmUsers.has(access.dmPeer)` and
  `authorId === access.dmPeer` — a **user** list, with the `D…` channel id never
  pre-known; `:107-108` makes `human` false for `senderKind:'bot'|'unknown'` with
  `isBot:true`, so `:117` forces `addressed = false` and `:118` requires
  `routingReason === 'context-only'` → `store:true, triggerInference:false`. The
  **no-wake DM variant is therefore necessary and correctly specified**, and the
  **authorized-human wake control is correctly demanded** because `:117` does wake
  on `human && dmAuthorizedPeer === access.dmPeer` — without it the row would read
  as a blanket DM wake veto. The fixture cases match the predicate
  (`policy-shapes.json:238-262`: `senderKind:"bot"`, `addressed:false`,
  `{store:true, mayWake:false}`), but the human control is prose-only (LOW 1).
  T01 is a connector-side specification for `reply_message`, not a claim about the
  host predicate, and is consistent with it. T02's "unreadable parent" ↔ `:105`
  `access.readable !== true` and "parent outside read scope" ↔ `:120` are exact
  parity; its third negative is the ambiguity in LOW 3. T03 is correct against B
  (above) and against `:57-63` (frozen `recipientPublishParams`/`threadId`).
- **Brief 6 — privacy grep over all five committed files at HEAD**
  (`docs/design/generic-recipient-{host,fixtures}.md`,
  `test/fixtures/generic-recipient/{policy-shapes,old-reader-payloads}.json`,
  `docs/README.md`), expanded list
  `/srv|review-drop|ci-fixtures|(^|[^a-z])/home/|TASK-[A-Z0-9]{4}|task[-_][a-z0-9]|orgasmic|soma|tarn|alnold|arnold|alder|lynx|\bopus\b|3574d888|9000000|U9000|dc099069|bf6e8e0|1d990e41|vasyl|dovhanchyn|theaspirational|ledgers|worktrees`
  → **zero hits in all five files.** The two round-3 prose `/home/` hits are
  excluded correctly by the lookbehind, not by whitelisting ("timer/home/module"
  `:121`, "locus/home/latest" `:233` — both prose, not paths), and the third is
  gone. The `docs/README.md` diff adds only two links; its pre-existing "Claude"
  lines are upstream text untouched by this branch. Both obsolete pins
  (`1d990e41`, `bf6e8e0e`) are gone from the doc as claimed.
- **Fixture integrity.** `sha256sum test/fixtures/generic-recipient/policy-shapes.json`
  = `d633f090fea0a1a1d2783b1f7d68afb32ade8ffe3c255316d810c8e945c74413`, matching
  `host.md:106` and `fixtures.md:58`; `old-reader-payloads.json` unchanged at
  `8f70b5cd…`. The two new cases use invented ids (`mailbox/newly-joined`,
  `sample-bot-peer`) consistent with the existing public vocabulary; no private
  value, key or structure.

**Not run, by design:** no build, install, test suite, AF composition, connector
execution, store open, compaction/restart or live ordering. The deliverable is
documentation plus two data fixtures; A01–A04/U01–U02/T01–T03 and H01–H12/S01–S06
are specifications and the doc claims no passes for them.

## Unmet Criteria / test gaps

- **Acceptance: met**, unchanged from round 3 and not regressed by this diff.
  The manager's round-3 additions are all present: the three LOWs, both re-pins
  with D's rebase marked pending, the reclassification to engineering work inside
  C/E/F and the four named forks, the T slices with a single-counted totals table,
  and the explicit T01–T03 negatives.
- **Opus review** remains a manager-owned gate; this review is not it, and the
  worker claims no receipt.
- Test-spec gaps: no positive-wake case anywhere in the public fixture (LOW 1);
  T02's author-access negative is unreconciled with A01/A04 parity (LOW 3).

## Residual Risk

1. The exact-thread / dynamic-scope integration is still unbuilt. It is now
   scoped and priced (T1–T5, 48–78 h) and gates G, but nothing is qualified.
2. D `a5b02a07` is still unrebased onto A `869bb3ad`; the composed A+B+D
   integration commit and the AF 0.31 fork tarball have no identity, so §2's
   `file:vendor/` instruction remains unexecuted. The A/B facts I verified are
   static tree/ancestry/API facts, not evidence that the composition builds.
3. The frozen v1 config schema is known-incomplete at its point of definition
   (LOW 2); parallel F-Slack work may be built against it.
4. If T1 turns out to need AF plumbing, the 8–16 h AF slice plus a tarball
   re-pin serializes ahead of F-host.2a/.3/.4 — the doc says so (`:704-708`),
   but that path is the main schedule risk and is still a conditional.
5. Branch history remains unpublishable (release constraint above); the private
   drop stays mutable and host-local.
6. The no-fence path still depends on the unexecuted 5.1/5.2 probe.

## Fix Directions (ordered)

1. Add an `authorized-human-dm` positive-wake case to `policy-shapes.json` and
   assert it in `check-doc-contract.py`, so the fixture can fail a never-wake
   implementation.
2. Mark `readChannels` provisional at `host.md:93` and `:209`, cross-referencing
   the §4 gap text and the §6 T1 scope contract.
3. Qualify T02's author-access negative to binding/wake, or state that it must
   not narrow ingress storage below the Mapping X read ceiling.
4. Add "status/conversation behaviour" to the §4 escalation examples (`:444`).
5. Re-pin D to its rebase onto `869bb3ad`, then record the one composed
   integration SHA and tarball hash.
6. Publication: fresh commit of this tree onto the public base; never push
   `2c396622..4326620b`.
