# F-host design — fix round 3

## Changed

**HEAD:** `4326620b2c34c0101960c7bdd5e480ae20e422d6`.
Parent: `90fb9ef6b3a23341d143d99b7f716be1d847bf19`.
Branch: `task-e9c1m-impl`; upstream base remains
`2c39662289be4cd5a150222e8456ff5577ce7622`.
Local new commit only; no push, history rewrite, production implementation,
dependency change, store open, connector execution or live ordering.
Requested Vasyl Dovhanchyn author/committer and GPT-6.1-Sol trailer verified.

Changed three files:
- `docs/design/generic-recipient-host.md`
- `docs/design/generic-recipient-fixtures.md`
- `test/fixtures/generic-recipient/policy-shapes.json`

Read REVIEW3 in full, SHA-256:
`1e99def4fd3fc837e3a11eb7a406a6a7c7494c09aaad70c3ee9f05d27ae63d59`.

### Disposition

| Requirement | Disposition |
| --- | --- |
| LOW-1 neutral label/name guard | Replaced the internal baseline-peer label with `outcome-sidecar-style`. Removed its allowance and now forbid case-insensitive `orgasmic|soma|tarn|alnold|alder` across both public docs and both JSON inputs. Path/task-id/review-bookkeeping checks retained. |
| LOW-2 non-enumerable read sources | Mapping X explicitly names `readJoinedChannels:true` and identity-keyed `dmUsers` as not enumerable into startup channel-id lists. A04 plus two invented public scenarios require parity for a later join and first-contact permitted DM. The no-wake DM variant is a valid bot/unknown-peer case; an authorized-human wake control prevents an accidental blanket DM veto. Storage parity depends on required engineering scope mapping, not an owner funding gate. |
| LOW-3 descriptor carriage | Carriage table and boundary text now name `ChannelDescriptor.address` alongside `.metadata`, treating both as opaque data, not parent/scope authority. Rechecked actual B type. |
| A re-pin | Active A is `869bb3add7b48893d48b2ce50ea423e4b33fdfbc`. Verified its `src` tree equals old A `1d990e41`; the only whole-tree diff is `docs/required-inbound-admission.md` (6 insertions/1 deletion). No claim that the full Git trees are identical. |
| B re-pin | Active B is `27a321276c8c03efe3fbc810029d25d53ec04572`; ancestry check proves it is on A869. Relevant channel-only resolver, current-key/grant/descriptor, timeout/cap/authorize surface rechecked. Old active A/B pins removed from docs. |
| D pending composition | D remains `a5b02a07fd4c380ded751e01ada2ce3e6b242cca`, explicitly marked **rebase onto 869bb3ad pending**. One exact integration commit/versioned AF0.31 tarball is still mandatory; no assembled release or runtime compatibility is claimed. |
| Thread-gap classification | Exact thread identity/read-storage parity are already-approved requirements. Dynamic scope, registered sub-conversations and native delivery within C/E/F and the four authorized forks are engineering work. Removed the obsolete owner decision and its funding/retain-old-pair formulation. Seven remaining owner choices are numbered once in §4; §7 references them. |
| Scope escalation boundary | Only a concrete observable-contract change, permission expansion/narrowing, recovery-guarantee change, or repository outside the four authorized forks triggers owner escalation. No such change is proposed by the engineering plan. |
| Engineering slices/estimates | Added five incremental T slices totaling 48–78 h, critical path approximately 30–48 h, plus conditional AF-scope 8–16 h and a serial integration-tarball re-pin/rebuild. All old/new/fence/design combinations are recomputed once; baseline compile/harness/persistence work is not recharged in T. |
| Explicit negatives | T01/T02 require unreadable-parent, parent-not-permitted, and caller/trigger-author-without-access refusals. T03 explicitly includes deleted/moved threads and previous-epoch bindings with equal visible ids; no replacement-route, re-resolution or root fallback. Ingress storage refusal is distinguished from already-admitted private output. |
| Retained release constraint | History is intact as instructed. Parent `9f153044` still contains private input locations/projection-derived statements: this branch must not be pushed as-is. Publication remains a later fresh commit of the reviewed final tree. |

Public policy fixture SHA-256:
`d633f090fea0a1a1d2783b1f7d68afb32ade8ffe3c255316d810c8e945c74413`.
The two added scenarios are purpose-built, not copied private policy data.
Old-reader hash manifest unchanged:
`8f70b5cdab01eacdfb207698179c154ce171412ed417ef630fa8f4c18a06aeb5`.

### New engineering hour table

All ranges are engineer-hours with focused tests, not commitments or live
qualification. The original host-only H work remains **52–80 h** without design
or conditional launch fence.

| Additional dynamic scope + exact thread work (T) | Hours |
| --- | --- |
| T1 generic scope/predicate/registration contract | 6–10 |
| T2 incremental host dynamic scope mapping/bind controls | 12–20 |
| T3 F-Slack native ThreadRef and registered-sub-conversation lifecycle/delivery | 16–24 |
| T4 additional native fake-API/thread/epoch qualification | 10–16 |
| T5 dynamic join/DM storage-parity requalification | 4–8 |
| **T total** | **48–78** |
| Conditional AF-scope implementation/testing + integration tarball re-pin/rebuild | **8–16 extra** |

T allocation: approximately **22–38 h host-side**, **26–40 h connector-side**.
Estimated critical path **30–48 h** assumes staged/overlapping implementation;
it is not the sum of all serial row durations. Fully blocking conditional AF
work adds roughly 8–16 h, making **38–64 h**, and serializes before dependent
F-host.2a/.3/.4 final integration gates. Independent F-host.2b preparation can
still start immediately.

| Included work | Without 10–16 h fence | With fence |
| --- | --- | --- |
| H only, excluding design | 52–80 h | 62–96 h |
| H + T, excluding design | **100–158 h** | **110–174 h** |
| H + T + conditional AF, excluding design | **108–174 h** | **118–190 h** |
| H + design, without T | 58–88 h | 68–104 h |
| H + T + design | **106–166 h** | **116–182 h** |
| H + T + conditional AF + design | **114–182 h** | **124–198 h** |

Design adds 6–8 h; fence and AF-scope are independent conditionals added once.
These cover the specified host/F-Slack work, not a full future connector port.
No new CM/Chronicle implementation or work outside the four authorized forks
is included. Exact native delivery and dynamic scope are not silently omitted.

## Verification Gates

Both ran newly at clean HEAD `4326620b2c34c0101960c7bdd5e480ae20e422d6`.
Current round evidence directory:
`/home/orgasmic/.orgasmic/runs/run-01M67AGJSD09ZWPPQ4GNXJ0F7E/evidence`.

| Command | Result / log |
| --- | --- |
| `orgasmic dispatch gate docs-contract -- python3 "$ORGASMIC_EVIDENCE_DIR/check-doc-contract.py"` | **PASS, exit 0**, reusable receipt; `gate-docs-contract-01M67B1F9YP4MEJWW0FKZTW9D6.log`. Exact scope/parent/identity, whitespace/links, both docs and both JSON path/forbidden-name checks, new pins and D note, H01–H12/S01–S06 plus A01–A04/U01–U02/T01–T03, A04 scenarios and thread negatives, single owner list 1–7, five T rows and all combined/design/fence totals independently recomputed. |
| `orgasmic dispatch gate source-pins -- python3 "$ORGASMIC_EVIDENCE_DIR/check-source-pins.py"` | **PASS, exit 0**, reusable receipt; `gate-source-pins-01M67B1FF2QBYD8FZC1VH72AA3.log`. New A/B checks below, plus unchanged old-reader file/export/native/lock hashes and 1502 runtime inventory entries with independent Python bytewise and find/LC_ALL=C sorting; frozen AF storage guard versus wake hook, CM0.24.0 API premise, overlay notes, D deltas and native explicit thread parameter. |

Source-specific proof:
- `git diff --name-only 1d990e4195e265f58be865719df2bb7e51f5c456 869bb3add7b48893d48b2ce50ea423e4b33fdfbc`
  yields only `docs/required-inbound-admission.md`.
- Both `:src` trees are `3d21d6a67815d27b194366919fd824947977e2a2`.
- `git merge-base --is-ancestor 869bb3add7b48893d48b2ce50ea423e4b33fdfbc 27a321276c8c03efe3fbc810029d25d53ec04572`
  exits 0.
- B27 `src/mcpl/reply-routing.ts:6-17,93-151` and
  `src/framework.ts:34945-34952` retain channel-only/current-authority checks;
  `src/mcpl/types.ts:1081-1099` exposes `address` and `metadata`, no parent/scope API.
- Frozen host predicate still uses `readJoinedChannels`, permitted `dmUsers`,
  and `addressed = human && ...dmAuthorizedPeer...`, hence the explicit A04
  no-wake variant plus human wake control is necessary.

Scripts are adjacent to this report, outside the worktree. Both gates passed
on their first run this round. No build caches were created in evidence.

**Inherited evidence:** the frozen oracle/code identities, CM0.24.0 package,
D a5b02a07 and supplied source reports retain their prior content/pin scope.
Prior review/receipt history is context only, not a current pass. A's earlier
runtime conclusions remain valid because the source tree equality was checked;
B's relevant API claims were rechecked at the new pin rather than inherited
from bf6e8e0e. No earlier gate receipt was reused.

**Not run:** builds, installs, connector execution, stores, old/new native
compatibility, compaction/restart, outbox crashes or live qualification. Fixture
rows remain specifications, not implemented or passed runtime tests.

## Unmet Criteria

None for this documentation/data-fixture round. All three LOWs, pin updates,
reclassification, new engineering estimates and negative-case requirements are
addressed. No new peer/Opus review receipt, integrated AF build or preservation
qualification is claimed.

## Residual Risk / final owner list

The single list is §4 **1–7**; §7 adds none. The same final list is:

1. **No translator / unavailable H1 cross-pair:** disposition matched-pair-only
   target support, an H1-capable adapter or separate old-pair retention. Stock
   upstream's absence of v1 does not resolve the deployed transition.
2. **H1→H2 effects:** preserve/disposition the separate no-field/open-repair
   compatibility window; D is not a transparent H1 replacement.
3. **Recovery/ACK policy:** disposition actual changes to receipt timing,
   request-only tracking, uncertain quarantine and recovery—not exactly-once or
   retro-replay of already-committed old receipts.
4. **Reconnect/unbound output:** resolve any dependency on cross-epoch completion
   or unbound automatic posts; stale authority remains private, never re-resolved.
5. **One-way store/cutover boundary:** choose qualified native-barrier or scoped
   managed-launch protection, supported entry points and uncertainty handling;
   no arbitrary SDK protection or stale-history rollback.
6. **Automatic-reply opt-in:** choose eligible connections after dependency
   disposition; execution authorization is not live-data approval.
7. **External operator notice (optional):** approve a route separately if wanted;
   an internal readable notice does not authorize guessed external publication.

Exact threads, dynamic read sources and their existing C/E/F implementation
inside agent-framework/connectome-host/slack-mcpl/telegram-bot-mcpl forks are
**engineering work, not an eighth owner decision**. Escalation applies only to
concrete changes to observable contract, permissions, recovery guarantees or
repository scope. The work and qualification are still unbuilt, but now
explicitly estimated, including the conditional AF/tarball dependency.

D's rebase, the one composed AF0.31 tarball and optional-authorize typing
reconciliation remain engineering inputs. No-fence remains release-manifest-
specific and requalified on storage hash changes. Historical private material
in reachable `9f153044` remains an intentional publication constraint: no push
of this branch history; later promotion must be a fresh commit of the final tree.
