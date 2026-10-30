# F-host design — fix round 2

## Changed

**HEAD:** `90fb9ef6b3a23341d143d99b7f716be1d847bf19`.
Parent: `af8e4da79e8b728904fc0a34fbd09091419c6967`; branch `task-e9c1m-impl`.
Upstream base remains `2c39662289be4cd5a150222e8456ff5577ce7622`.
Local new commit only, with the requested Vasyl Dovhanchyn author/committer and
GPT-6.1-Sol co-author trailer. No push, history rewrite, production code,
dependency change, store open, connector execution or live ordering.

Changed files (four; documentation/data fixtures only):
- `docs/design/generic-recipient-host.md`
- `docs/design/generic-recipient-fixtures.md`
- `test/fixtures/generic-recipient/policy-shapes.json`
- `test/fixtures/generic-recipient/old-reader-payloads.json`

Read REVIEW2 in full. SHA-256:
`625db0c2b8c96fe4466bf8e62ec6f6d2372190eb4ccf83d48b6fdc6cb9f9b25a`.

### Disposition table

| Finding / requirement | Disposition |
| --- | --- |
| M1 frozen storage guard | **REVIEW2 M1 is factually wrong about the full frozen pair having no storage guard.** The host `shouldTriggerInference` callback is wake-only, but patched AF separately checks `bridge.accept(...).store` before both handlers. Manager and Soma confirmed the correction. The matrix and new read/storage/wake subsection state both facts with source/compiled evidence below. |
| M1 ambient consequence, as corrected | No invented ambient-loss owner decision. Mapping X must cover all old-readable joined channels/allowed counterparts, not the write/wake whitelist. A01 requires valid ambient joined context stored/no-wake on **both** pairs; A02 requires unreadable/invalid context not stored on either. A03 deliberately omits read coverage and exposes a bad-mapping preservation mismatch; that is not the supported mapping. |
| M2 unconfigured D wire deltas | Baseline-peer × generic-pair cell now names both deltas and cross-references §2: push `delivery` → `-32602 unsupported_combination`; post-policy ungranted selected incoming batch → ordered whole-batch `capability_denied`. U01/U02 and public JSON cases cover profile/custom-admission off, with baseline controls. |
| M3 overlay oracle | `overlay-cannot-widen` is explicitly unprotected and expects `grantWidening:false` plus `operatorNote:true`, not rejection. Separate protected-policy replacement case expects configuration rejection. §2 distinguishes the stimuli. Rechecked upstream drop/notes source. |
| M4 inventory sorting | Recomputed every runtime-root digest using UTF-8 package-relative POSIX **path strings sorted bytewise, LC_ALL=C**, not pathlib component ordering. AF src/dist digests changed; underlying file hashes and CM/native inventories did not. Manifest wording and source-pins agree. Independently reproduced **every root** via `find -print0 | LC_ALL=C sort -z` and `sha256sum`. |
| M5 example provenance | §2 uses the actual public fixture values: fixture-edge/sample-receiver/mailbox channels, 700 ms admission and 900 ms reply timeout. Separate endpoint-a/acct-example-a/room-one identifiers are explicitly the harness, not that fixture's provenance. |
| M6 branch history | Followed the user's override: **did not rewrite the branch**. Exact private history inventory is below. HEAD is cleaned, but `9f153044` remains reachable and this branch must not be published as-is. Publication is manager-owned later from a fresh commit of the final tree. |
| L1 public documentation bookkeeping | Removed the review-bookkeeping sentence and all internal report filenames/task handles, including the old branch handle. Citations now use “privately supplied report” plus SHA-256. |
| L2 owner numbering | One authoritative owner list, numbered **1–8 in §4**. §7 references it without adding or renumbering decisions. This report uses the same eight numbers. |
| Exact Slack threads (new requirement) | Exact thread identity is mandatory, not waived by channel-only B. New subsection specifies immutable native ThreadRef → genuine registered neutral sub-conversation → B frozen destination → exact native publish; explicit reply parameters retain the chosen root. T01 explicit, T02 triggering-thread automatic, T03 stale/private cases added. The actual dynamic-thread/admission-scope integration gap is named under owner decision 3; no existing AF parent/scope API is invented. |
| Retained boundaries | H01–H12/S01–S06 retained, plus A01–A03/U01–U02/T01–T03. Conditional managed-launch boundary still excludes arbitrary SDK readers. No-fence is manifest-specific and requalified on any storage-layer hash change. AF0.31 exact integration-tarball requirement and corrected hour sums remain. |

### Corrected source evidence for M1

The frozen source SHA-256 remains
`c7c7e53ed9359e6a3834f9c6c1a6b09350559d16fc1c28ebcdb3ccdb636a31c8`,
matching the pinned oracle manifest and the in-repo code-hash manifest.
Relative to the frozen AF package:
- `src/framework.ts:20589-20597`: `bridge.accept('push/event', origin)`;
  `if (!decision.store ...)` answers refusal and returns **before** `pushHandler`.
- `src/framework.ts:20726-20744`: itemwise `bridge.accept('channels/incoming', metadata)`;
  `if (!decision.store ...)` refuses **before** `channelRegistry.handleIncoming`.
- `dist/src/framework.js:18423-18433,18534-18560` has the corresponding guards;
  compiled file SHA-256 is
  `ab380ca55e5a319b828b66aaea8f47ccdce4ec5339c4b9dd58cf04c6f715cf24`.
- Host `src/support-recipient.ts:24-25` separately installs the wake-only hook.
  Reading only that hook/downstream queue handlers omitted the outer AF guard.

Host `src/host-recipient-protocol.ts:94-129` defines the old storage predicate:
correct receiving connection/account/version and channel/message/root evidence;
matching readable access; non-self consistent provenance; joined readable channel
with `readJoinedChannels:true`, or exact allowed DM counterpart/author. Invalid
items refuse storage; valid ambient items store without wake. The old host's
write-list shape is validated but is not its readable/storage set. New §3 exact
`readChannels` rejects omitted channels; **Mapping X must cover all old-readable
sources** and retain equivalent adapter access/provenance checks. With that mapping,
there is no inherent ambient storage difference. Incomplete dynamic-channel/thread
coverage blocks qualification and is explicitly part of the integration gap,
not permission to silently narrow reads or an inferred deployed-data loss.

### Fixture identities

- Independent public policy fixture SHA-256:
  `fbef1ce744da35ec19082a78ddda0e1b2fd7d9f9a8692f0d477db83678d9214a`.
- Old-reader payload manifest SHA-256:
  `8f70b5cdab01eacdfb207698179c154ce171412ed417ef630fa8f4c18a06aeb5`.
- Correct bytewise AF src inventory:
  `6678e5f0043bec7304ea7a3c17ff08e3d049954b91b2dc5ab631207bc88204c1`.
- Correct bytewise AF dist inventory:
  `5cfd5889ae6742ba5cc1f892e974f6233be5293f3d1cf6e8e17d59eb50dcc4b3`.

The public policy fixture remains purpose-built, with invented values and no
private projection, deployment pins, incidental live cardinalities or task/approval
evidence. Both JSON files and both docs pass the private-path check.

## Verification Gates

Both gates ran newly at clean HEAD
`90fb9ef6b3a23341d143d99b7f716be1d847bf19`; neither is an inherited receipt.
This round's evidence directory:
`/home/orgasmic/.orgasmic/runs/run-01M6797GCPGMBCQHR82W9DM5HG/evidence`.

| Command | Result / log |
| --- | --- |
| `orgasmic dispatch gate docs-contract -- python3 "$ORGASMIC_EVIDENCE_DIR/check-doc-contract.py"` | **PASS, exit 0**, reusable receipt. `gate-docs-contract-01M679WW1C3YDYTRTHYGE9MDSH.log`. Scope/parent/identity/trailer, whitespace/links/fences, private-path check over **both docs and both JSON files**, no internal task IDs/review bookkeeping, pins/AF0.31 wording, all 18 original + 8 additional matrix rows, corrected policy oracles/provenance, public hashes, one owner list 1–8 and exact hour totals. |
| `orgasmic dispatch gate source-pins -- python3 "$ORGASMIC_EVIDENCE_DIR/check-source-pins.py"` | **PASS, exit 0**, reusable receipt. `gate-source-pins-01M679WW6FV17AQW6EXW6BC07N.log`. 1502 runtime inventory entries validated with Python bytewise string sorting **and independent find/C-sort for every root**; installed versions/exports/payloads/locks, exact CM0.24.0 API premise, A family/attestation, full frozen AF source/compiled storage guards versus wake-only host hook, old read predicate, upstream overlay notes, both D wire deltas, native explicit-thread parameter. |

Checker scripts are adjacent to this report, outside the worktree. Both are
static specification/source/hash checks, not behavioral or preservation receipts.
No gates failed in this round. An ordinary pre-commit whitespace check found an
extra blank line at EOF after removing bookkeeping; it was removed before commit.

**Inherited evidence and scope:** exact A `1d990e41`, B `bf6e8e0e`, D `a5b02a07`,
base `2c396622`, frozen oracle file identities and the privately supplied source
reports remain content/pin bounded. Earlier review/receipts are historical context
only; no earlier gate pass was reused. Material disputed findings were re-read
against the unchanged actual source and checked in the new gate. History contents
were inspected with `git show 9f153044:...`, not rewritten.

**Not run:** builds, runtime connector/transport tests, native store compatibility,
compaction/restart, outbox crashes, managed-launch or live probes. Required new
fixture rows are specifications, not already implemented/passed tests.

## History / publication restriction — exact private contents in 9f153044

**Do not push this history.** At the user's direction it is intact. Clean HEAD
content does not remove the following from reachable parent `9f153044`.
The earlier statement that no raw private JSON file was committed was true but
incomplete: its **projection-derived statements were committed in Markdown**.
Publication must later use a fresh commit of the final tree, not this ancestry.
No rewrite, squash, cherry-pick, new publication branch or push was performed here.

Exact items in `9f153044`:

### Machine-local input paths / internal handles

`docs/design/generic-recipient-host.md`:
- Lines 101–102: `/srv/review-drop/soma-rtaln-policy-shape-20261030/POLICY-SHAPES.json`
  and the private projection SHA-256
  `3574d8885580636a4269cadeb82bfb116695bccc62434209b62d853ecaa02911`.
- Line 238: `/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/src/{index.ts,framework-agent-config.ts,runtime-account-identity.ts,support-recipient.ts,host-recipient-protocol.ts}`.
- Lines 293–294: `/srv/review-drop/tarn-rtaln-20261030/TASK-KPY3G-SLACK-STATIC.md`
  and internal basename `TASK-Q2TVQ-MAPPING.md` “in the same directory.”
- Line 348: `/srv/review-drop/tarn-rtaln-20261030/TASK-EM8Q2-I3-I5.md`.
- Line 19: internal branch handle `task-hjv3j-r2`; lines 470–471: internal
  Opus/manager review bookkeeping sentence. These are internal workflow details,
  separate from the projection-derived data below.

`docs/design/generic-recipient-fixtures.md`:
- Lines 6–8: `/srv/review-drop/tarn-rtaln-20261030/TASK-EM8Q2-O4.md` and internal
  basenames `TASK-KPY3G-SLACK-STATIC.md`, `TASK-Q2TVQ-MAPPING.md`.
- Lines 56–57: the same private POLICY-SHAPES path and SHA-256 above.
- Line 155: `/srv/review-drop/soma-rtaln-slack-code-20261030` sealed-input location.

### Projection-derived policy statements/cardinalities (fixtures doc lines 63–75)

- Slack: receiving `U900000001`, write/fallback channel `C900000011`, DM peer
  `U900000012`; joined reads true; subscribe/unsubscribe tools denied; restricted
  synthetic uploads; stdio/reconnect false; public-channel/write-list fallback
  restriction. These were masked projection values, not public-fixture inventions.
- Telegram: allowed chat/user `900000001`; “exact 17-tool list and seven feature
  sets from the file”; stdio/reconnect false; **120000 ms** request timeout.
- GitHub: events/read only; **six** mutation feature sets denied; failure-only
  checks; **114** synthetic repositories; stdio/reconnect true; reference to the
  separate **eight** read-tool allowlist from the private mapping.
- Outcome sidecar: WebSocket/reconnect true and **660000 ms** request timeout;
  the mutable-deployment-attribution qualification sentence. The doc did not
  spell out a deployment commit in that sentence.

These are the private input locations, handles, masked identities and inferred
policy shape/cardinality statements at issue; no real credentials, actual private
store/history, real routing identifiers or raw POLICY-SHAPES JSON payload was
committed. Source-code pins/native hashes are code identities, not a claim that
this policy material was public. Current HEAD removes those private projection
markers and paths; it cannot sanitize history without the later publication step.

## Unmet Criteria

None for this docs/fixtures fix round under the manager's explicit source-based
correction of M1 and the user's no-history-rewrite instruction. All six MEDIUM,
two LOW and the exact-thread specification requirement are addressed within that
scope. The permitted alternative of naming the exact thread/dynamic-scope gap is
used explicitly; no implementation/preservation completion is claimed.

## Residual Risk / final numbered owner list

The single numbered source of truth is §4, decisions **1–8**; §7 only references
it. The matching final owner list is:

1. **No translator / H1 cross-pair:** approve matched-pair-only target support,
   retain the old pair separately, or scope an H1-capable adapter. Stock upstream's
   absence of v1 does not approve deployed pr361 transition/recovery.
2. **H1→H2 effects:** preserve a separately implemented/qualified H1 window or
   explicitly disposition the no-field/open-repair delta; native D is not H1.
3. **Required thread mechanism / other status changes:** authorize/fund the
   missing preservation integration for dynamic sub-conversation admission scope
   and native thread delivery, or retain the old pair until qualified. Exact
   thread identity cannot be waived by narrowed B; engineers choose the concrete
   mechanism. Other status changes need disposition; typing requires C evidence.
4. **Recovery/ACK policy:** disposition changed pre/post-acceptance dedup,
   request-only tracking, uncertain quarantine and operator recovery. No exactly-
   once claim or retro-replay of old committed receipts.
5. **Reconnect/unbound output:** resolve actual dependencies on cross-epoch
   completion/unbound automatic posts; otherwise migration requires separate
   design. Stale authority is private, never re-resolved or repeated effects.
6. **One-way store/cutover boundary:** choose qualified native-barrier or scoped
   managed-launch protection, supported entry points and uncertainty handling;
   no arbitrary SDK protection, old D-store reader or stale-history rollback.
7. **Automatic-reply opt-in:** choose eligible connections after dependency
   disposition. Execution authorization is not live-data approval.
8. **External operator notice (optional):** approve a route separately if wanted;
   the internal B notice never permits guessed external publication.

Mapping X's read/storage/wake parity is a required mapping/test, **not an invented
ambient-loss owner decision**. Channel-only B plus exact static lists does not
currently implement the native/dynamic-thread integration; all listed host hours
remain the listed scope, not an estimate for that unresolved extra work. Native
compatibility, outbox and exact-thread runtime qualification remain unexecuted.
The branch history remains intentionally unsuitable for direct public publication.
