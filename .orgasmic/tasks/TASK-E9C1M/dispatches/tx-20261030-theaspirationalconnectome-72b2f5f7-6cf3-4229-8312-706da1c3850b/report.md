# TASK-E9C1M re-review — F-host design, fix round 1

Reviewed `af8e4da79e8b728904fc0a34fbd09091419c6967` (parent `9f153044`, base
`2c39662289be4cd5a150222e8456ff5577ce7622`). Diff vs parent = 5 files, 510 lines:
`docs/README.md`, `docs/design/generic-recipient-host.md`,
`docs/design/generic-recipient-fixtures.md`,
`test/fixtures/generic-recipient/{policy-shapes,old-reader-payloads}.json`.
Read-only: no edits, commits or files inside the worktree (`git status` clean at
start and end). Previous review `/srv/review-drop/TASK-E9C1M-REVIEW1.md`, worker
report `/srv/review-drop/TASK-E9C1M-FIX1.md`.

## Verdict

**APPROVE.** All 7 MEDIUM and all 5 LOW from REVIEW1 are genuinely closed — I
recomputed the arithmetic, re-derived every source claim at the named pins, and
re-verified every hash rather than reading the disposition table. The public
fixture passes the PUBLIC-FIXTURE GUARD: it is purpose-built, not a copy or
sanitized serialization of the private projection. Six new MEDIUM findings
remain, four of them contract-accuracy defects an implementer would code
against. No HIGH: nothing here is a shipped bug, a security defect or an unmet
acceptance criterion.

### Round-1 closure, independently verified

- **M1 hours.** Rows are .1=6–8, .2a=10–16, .2b=10–16, .3/.4/.5/G=8–12, .6=10–16.
  Recomputed: excl. design+fence **52–80**; with fence **62–96**; incl. design
  **58–88** / **68–104**. `host.md:462-465` states exactly these. Closed.
- **M2 modern family.** `host.md:143-150` is accurate against A `1d990e41`
  `src/mcpl/protocol-family.ts:700-703` (`binding.family !== 'legacy'` →
  `'inboundAdmission is unsupported by the modern protocol family'`, quoted
  verbatim) and `:604-605` (stdio → `config.protocol ?? 'legacy'`; `http` →
  modern; ws(s) → legacy). H02 (`fixtures.md:98`) and fixture case
  `modern-not-eligible` carry the zero-connect negative control. Closed; the new
  startup error `recipient_policy_requires_legacy_family` is consistent with A
  (brief item 5a).
- **M3 notifications.** A `1d990e41:docs/required-inbound-admission.md:108-109`
  ("Notifications have no wire receipt; no application status is sent for a
  refusal") and `:73` ("A verdict is not a delivery receipt").
  `host.md:289-291` and `fixtures.md:121-125,133` now require id-bearing
  request form for every outbox-tracked item and add the never-settles
  negative control. Closed; outbox rule consistent with A (brief item 5b).
- **M4 oracle lock divergence.** Verified myself: `package-lock.json` resolves AF
  0.21.0 / CM 0.13.0 while installed+`bun.lock` are AF 0.22.2 / CM 0.14.1 /
  Chronicle 0.4.0. `host.md:395-404` states it; "before every attempt, including
  positive controls" assertion is now normative (`:420-429`). Closed.
- **M5 no-fence requalification.** `host.md:486-497` makes it a release-set
  manifest artifact with the storage-payload-hash re-run trigger. Closed.
- **M6 private paths.** `grep -nE '/srv|review-drop|/home/|soma-rtaln'` over all
  five committed files: clean. The three prose hits (`host.md:114`
  "timer/home/module", `host.md:219` "locus/home/latest", `fixtures.md:168`
  "context/root/typing") are not paths. Private inputs are SHA-identified only.
  Closed at HEAD — but see MEDIUM 6 for the branch history.
- **M7 split.** `host.md:455-456` = F-host.2a harness / 2b probe, 10–16 h each,
  2b "can start immediately". Closed.
- **L1** `host.md:139` scopes strictness and states unknown keys elsewhere keep
  tolerant handling. **L2** `host.md:252` states stock upstream has no recipient
  protocol and that this does not resolve pr361 transition. **L3** I extracted
  the CM 0.24.0 tarball (sha `ee1e7682…` matches) and confirmed all three cited
  ranges at exactly 0.24.0: `:193-216` config type (no reader-version member),
  `:518-545` `JsStore.openOrCreate` open, `:550-580` `MessageStore.register` —
  no application-reader floor. **L4** `fixtures.md:98` adds the non-data
  initialize-result attestation control. **L5** `docs/README.md` has a Design
  section linking both docs. All closed.

### PUBLIC-FIXTURE GUARD (brief item 2) — passes

The private projection is readable (`/srv/review-drop/soma-rtaln-policy-shape-20261030/POLICY-SHAPES.json`,
sha256 `3574d888…` as documented, mode 0400). I read both files side by side.
`policy-shapes.json` (sha256 `399a8837…`) shares **no** value, key or structure
with it. Not transferred: `slackConfig`/`serverRecipeRestrictions`/
`telegramAdmissionShape` structure, the `C9…`/`U9…`/`9000000…` synthetic ids,
`/synthetic/*` paths, `recipient.sqlite`, the 17-tool and 7-feature-set
Telegram lists, the 6-denied-feature GitHub set, "114 synthetic fixtures", the
120000/660000 ms transport timeouts, reconnect/transport pins, the `dc099069`
deployment attribution, and the `classification`/approval evidence strings.
Spot-grep for each of those markers at HEAD: absent. The fixture's own
identifiers (`sample-receiver`, `mailbox/*`, `inspect_entry`, `fixture-edge`)
and sizes (700/900 ms, 3 channels, 9 cases) are invented, and the case set is
organized around policy *relations* (grant/mask/deny-wins/admission-selection/
no-leaf/overlay), not a configuration dump. `old-reader-payloads.json` carries
only code identities — lock hashes, versions, entry points, file and
runtime-root digests, native payloads — no store, environment or policy data; I
re-verified the AF 0.22.2 file hashes, the two lock hashes and the 109/437/893
file counts directly against the oracle tree and they match.

### Brief item 4 — matrix separation and boundaries

The three columns are genuinely separated (`host.md:317` header + the
"Columns are intentionally distinct" paragraph at `:308-315`), and `:252-258`
states plainly that upstream's absence of v1 removes nothing but does not
resolve pr361 transition/recovery. The supported-combination paragraph
(`:323-328`) and fail-closed cells are mostly right. Two cells are not — see
MEDIUM 1 and MEDIUM 2. Owner-decision list: §4 names 6 numbered
H1/recovery/cutover differences and §7 adds two more (which connections may
enable automatic replies; an external operator notice route), i.e. 8 distinct
items where the worker's report says "six" plus a merged seventh. Nothing is
over-escalated — each is a policy choice, not an engineering call, and §7's
"Engineering calls" block correctly keeps packaging, error mapping, hashing,
typing reconciliation and slice execution out of owner scope. One genuine
difference is missing from the list (MEDIUM 1's second half).

## Findings

### MEDIUM

**MEDIUM `docs/design/generic-recipient-host.md:318` (correctness, brief item 4): the frozen pr361 column claims host-side validation "precede[s] storage/wake in both lanes"; in the oracle the legacy host bridge gates wake only and cannot refuse storage.**
In the oracle the bridge is installed solely as `shouldTriggerInference`
(`/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/src/support-recipient.ts:24-25`:
`bridge.accept(...).triggerInference`). `RecipientDecision.store`
(`src/host-recipient-protocol.ts:66-68,91,126`) is computed and never consumed —
`grep -rn 'RecipientDecision\|triggerInference' src/*.ts` finds no reader. In AF
0.22.2 that callback only sets the event's `triggerInference` flag: the item is
still queued and answered `accepted:true`
(`node_modules/@animalabs/agent-framework/src/mcpl/channel-registry.ts:1005-1075`,
`push-handler.ts:210-242`). So in the frozen pair, storage admission rests
entirely on the connector ACL plus AF grants; only wake was host-gated.
By contrast A refuses before any effect ("No effect runs on a refusal";
"Pushes run preflight … before deduplication, notices or queueing",
`1d990e41:docs/required-inbound-admission.md:76-91`).
Impact: the matrix inverts the one boundary the manager asked it to establish,
and it hides a real owner-relevant difference — on the new pair any channel
absent from `readChannels` yields `store:false` (`host.md:215`), so content the
old pair **stored silently without waking** is now not stored at all. That is a
capability/recovery change not listed among §4's owner decisions.
Fix: restate the frozen-pair cell as "connector ACL/provenance precede storage;
the host bridge gates wake only (`shouldTriggerInference`), not storage", and add
an owner decision for the loss of stored-but-not-woken ambient context on
channels outside `readChannels`.

**MEDIUM `docs/design/generic-recipient-host.md:320` (design/docs, brief item 4): the baseline-peer × new-pair cell says unconfigured connections "retain base initialize/grants and connector access behavior", omitting D's two profile-independent wire deltas.**
D `a5b02a07:docs/context-only-delivery.md:96-108` makes deltas 2 and 3
explicitly "independent of profile and grant" / "independent of the storage
profile and required admission": any top-level push `delivery` field is rejected
`-32602 unsupported_combination`, and an ungranted `channels/incoming` batch
containing any item-level `delivery` field gets ordered `capability_denied`
results for the whole batch instead of the usual capability error. `host.md:128-133`
records this correctly in §2, but the matrix cell — whose job is exactly "what
fails closed during a partial transition" — does not, so a reader concludes an
existing Telegram/GitHub/outcome peer is unaffected by adopting the new release
set. Fix: name both deltas in that cell as unconditional on the new host, and
cross-reference §2; escalation is not required (they remove no H1 capability).

**MEDIUM `test/fixtures/generic-recipient/policy-shapes.json:144-151` + `docs/design/generic-recipient-fixtures.md:66` (correctness/test): the normative `overlay-cannot-widen` case expects `configurationRejected: true`, which contradicts the host base and would turn today's warn-and-continue into a startup failure.**
At this base an agent overlay that sets `enabledCapabilities` is dropped with an
operator note, and startup proceeds: `src/mcpl-config.ts:296`
(`OPERATOR_ONLY_OVERLAY_FIELDS`) and `:390-392` push
"its enabledCapabilities is left out, since only the operator's config grants
that…" into `notes`, which `:735,751` emit as `[mcpl]` log lines, not problems.
`host.md:136` agrees ("Do not silently add generic grant authority to file/agent
overlays") and L1's `:139` keeps unrelated keys tolerant. Rejection is specified
only for a *replacement of a protected connection* (`host.md:152-156`) — a
different stimulus. An implementer satisfying the fixture as written regresses
the operator surface for every existing agent overlay. Fix: expect
`grantWidening:false` plus an operator note for this case, and add a separate
case for overlay replacement of a `recipientPolicy`-carrying connection with
`configurationRejected:true`.

**MEDIUM `test/fixtures/generic-recipient/old-reader-payloads.json:5` (correctness/test): the stated `inventoryAlgorithm` does not reproduce the recorded digests — the hashes were computed with pathlib's part-wise path ordering, not the documented lexicographic path sort.**
I implemented the algorithm exactly as written ("Sort package-relative POSIX
paths lexicographically; SHA256 … `<sha>` + two spaces + `<path>` + LF") and got
AF `src` = `6678e5f0…` and `dist` = `5cfd5889…`, against the recorded
`d5a47161…` / `0729bd7a…`. The recorded values come from
`sorted(base.rglob('*'))` over `Path` objects (the worker's own
`check-source-pins.py:24-29`), which orders by path *parts*, so
`src/usage/index.ts` precedes `src/usage-accounting.ts` where a byte/lexicographic
sort of the path strings does the reverse. CM's two roots happen to agree, which
is why only AF diverges. Impact: §5.1 makes this manifest a mandatory
pre-attempt assertion for the probe that gates .5, .6 and the whole store
decision; an independent implementation (or `find | LC_ALL=C sort`) aborts the
probe with a spurious "old reader changed". Fix: either state the ordering
precisely (sort by path components, directories traversed as components) or
recompute the four AF digests under plain lexicographic ordering; the gate script
must then match the published text.

**MEDIUM `docs/design/generic-recipient-host.md:107-111` (docs/correctness): the §2 example asserts "These numbers are fixture values" but none of them are in the fixture it cites five lines earlier.**
`:100-106` binds config examples to `policy-shapes.json` at sha `399a8837…`;
`:107-110` then uses `endpoint-a`, account `acct-example-a`, channel `room/one`,
and 1000 ms for both timeouts. The fixture has `fixture-edge`,
`sample-receiver`, `mailbox/active|reference|blocked`, `admissionTimeoutMs:700`
and `automaticReplies.timeoutMs:900` (`policy-shapes.json:6,7,25,29`), while
`endpoint-a`/`acct-example-a`/`room/one` are the *harness* identifiers from
`fixtures.md:16,28,36`. Two synthetic naming schemes is fine; claiming they are
the same data is not — the sentence makes a false provenance claim about a
SHA-pinned normative artifact. Fix: either use the fixture's actual values in the
example or say the example uses harness identifiers from `fixtures.md`, not the
policy fixture.

**MEDIUM branch history (scope/privacy, brief items 2–3): HEAD is clean, but parent `9f153044` still carries the private drop path, the private file's SHA-256 and projection-derived cardinalities — the branch cannot be pushed to a public PR unsquashed.**
`git show 9f153044:docs/design/generic-recipient-fixtures.md` lines 54-75 contain
"Read only `/srv/review-drop/soma-rtaln-policy-shape-20261030/POLICY-SHAPES.json`",
sha `3574d888…`, the projection's `U900000001`/`C900000011`/`U900000012` ids, "its
exact 17-tool list and seven feature sets from the file", "six mutation feature
sets denied, failure-only checks, 114 synthetic repositories", and the
120000/660000 ms request timeouts — precisely the deployment pins and incidental
live cardinalities the manager's correction forbids. `git grep` confirms all of
it is gone at `af8e4da7`. FIX1's claim "no private JSON payload was committed" is
true of the JSON and silent about this. Fix: squash/rewrite the branch (or
re-author the two commits) before any push or PR, and verify with
`git grep -E '/srv|review-drop|3574d888|9000000' <range>`.

### LOW

**LOW `docs/design/generic-recipient-host.md:618-619` (docs/scope): the doc's last lines carry internal review bookkeeping ("Opus review is manager-owned after this local documentation delivery; no review receipt is claimed here") into a file destined for an upstream PR.** True and useful in the dispatch, meaningless to `anima-research/connectome-host` readers. Same class, lower weight: the private report filenames `TASK-EM8Q2-O4.md` etc. at `:379-385` and `fixtures.md:6-9`. The SHA-identified citation form is what the manager asked for, so this is a pre-PR polish item, not a violation.

**LOW `docs/design/generic-recipient-host.md:333-363` vs `:589-602` and FIX1 (docs): the owner-decision count is inconsistent across artifacts.** §4 numbers 6; §7 adds two (automatic-reply opt-in per connection; external notice route); FIX1's table says "Six" and its residual list says 7 by merging the last two. Nothing is missing or over-escalated, but a manager counting owner gates from the report gets a different number than from the doc. Fix: number the owner decisions once, in one place, and have §7 reference them.

## Verification Gates

- **Reused receipts (read, not re-run)** — both at the reviewed commit on a clean
  tree, scripts read in full:
  - `docs-contract` exit 0 — `gate-docs-contract-01M678MFF9QZN16GV5Y68HHN22.log`.
    The script (`check-doc-contract.py`) is a static scope/parent/author/trailer/
    whitespace/link/fence/pin check plus the hour-sum arithmetic (`:51-57`), the
    public-fixture SHA and relation asserts (`:36-44`) and a no-`/srv`/`/home`/
    `review-drop` check on the two docs (`:22`) and on the policy JSON (`:40`).
    Its `/home/` check uses an absolute-path boundary, which is why the earlier
    `gate-docs-contract-01M678M921QXQPBW7MNVSZDB22.log` false positive on
    "timer/home/module" is a checker fix and not a doc change — I confirmed the
    three prose hits are the only `/home/` substrings. Note it does **not**
    path-check `old-reader-payloads.json`; I grepped that file myself (clean).
  - `source-pins` exit 0 — `gate-source-pins-01M678M9745VTM07N9XD33467V.log`.
    This is the script whose line 24-29 ordering produced MEDIUM 4: the gate is
    self-consistent with the fixture but not with the fixture's published
    algorithm text, so a green receipt here does not establish reproducibility.
- **Scoped read-only probes I ran** (no gate wrapper, no reusable receipt):
  `git status --short` (clean before/after), `git diff --stat` vs parent and base,
  `git log -1 --format` for author/trailer, `git grep` for forbidden paths/markers
  at both `9f153044` and `af8e4da7`; `sha256sum` on both fixtures and on the
  private projection; full read of the private `POLICY-SHAPES.json` and of
  `policy-shapes.json` for the fixture guard; `sha256sum`/`find` on
  `/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host` locks, AF/CM/Chronicle
  `package.json` versions and the five AF entry files; a Python
  re-implementation of the documented inventory algorithm plus five ordering
  variants and the pathlib variant; `tarfile` extraction of
  `@animalabs__context-manager@0.24.0.tgz` and line-range reads at `:193-216`,
  `:518-545`, `:550-580`; `git show` at AF `1d990e41` (`protocol-family.ts`,
  `docs/required-inbound-admission.md`) and `a5b02a07`
  (`docs/context-only-delivery.md`); reads of oracle
  `src/{support-recipient.ts,host-recipient-protocol.ts}` and oracle AF
  `src/mcpl/{channel-registry.ts,push-handler.ts}`; reads of host
  `src/mcpl-config.ts:285-400,730-760`; `python3` on all four hour sums.
- **Not run, by design:** no build, no test suite, no AF install, no connector
  execution, no store open, no live ordering. The deliverable is documentation
  plus two data fixtures; the design's own gates (old-reader probe, outbox crash
  matrix, compaction+restart, fence tests) are explicitly unexecuted and are not
  claimed as passed anywhere.

## Unmet Criteria / test gaps

- **Acceptance: met**, as at round 1, with the manager's two corrections now
  satisfied: the policy fixture is vendored in-repo as purpose-built public data
  (not the projection), and the matrix separates the three sets with explicit
  fail-closed boundaries and an explicit "no-translator does not resolve the
  pr361 transition" statement.
- **Opus review** remains a manager-owned gate; the worker claims no receipt and
  this review is not that gate.
- Fixture-contract gaps, in priority order: the overlay case expectation
  (MEDIUM 3) is currently a wrong oracle rather than a missing one; there is
  still no case covering D's two profile-independent wire deltas against an
  unconfigured peer (MEDIUM 2) — H11 covers the selector matrix on opted-in
  connections only; and no fixture asserts the `store:false`-for-unlisted-channel
  consequence that MEDIUM 1 identifies as an owner-visible difference.
- The `inventoryAlgorithm` text is the only part of either fixture that a third
  party cannot currently reproduce from the repo alone.

## Residual Risk

1. Still validated against three unmerged, under-review AF pins; the A+B+D
   integration commit and the AF 0.31 fork tarball have no identity yet, so §2's
   mapping table and the `file:vendor/...` instruction remain unexecuted.
2. MEDIUM 1 means the one *source-checked* comparison between the deployed pair
   and the proposal is currently wrong in the artifact an owner would read to
   make the transition decision.
3. The no-fence path depends entirely on a probe (§5.1) whose input manifest has
   the reproducibility defect in MEDIUM 4.
4. The private drop remains mutable and host-local; the repo now depends on it
   only for the privately supplied reports and the oracle tree, not for any
   fixture data — a real improvement over round 1.
5. Internal task IDs and the Opus-review line are still in PR-destined files
   (LOW 1); harmless technically, visible publicly.

## Fix Directions (ordered)

1. Correct the frozen-pair matrix cell (`host.md:318`) to wake-only host gating,
   and add the stored-but-not-woken ambient-context difference to §4's owner
   decisions.
2. Add D's two profile-independent wire deltas to the baseline-peer × new-pair
   cell (`host.md:320`).
3. Change `overlay-cannot-widen` to expect drop-with-note, and add a separate
   protected-connection-replacement case expecting configuration rejection.
4. Make `inventoryAlgorithm` and the four AF digests agree — state component-wise
   ordering or recompute lexicographically — and align the checker text.
5. Fix the "These numbers are fixture values" provenance claim at `host.md:110`.
6. Squash or re-author the branch so `9f153044`'s private path, private SHA and
   projection-derived cardinalities never reach a public PR; verify by grep over
   the pushed range.
7. Drop the Opus-review line before the PR and unify the owner-decision
   numbering between §4, §7 and the report.
