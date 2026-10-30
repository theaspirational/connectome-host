# TASK-E9C1M — design fix round 1

## Changed

**HEAD:** `af8e4da79e8b728904fc0a34fbd09091419c6967` on `task-e9c1m-impl`.
Parent: `9f153044ce63b8da719a0132c0817901f1031216`; upstream base remains
`2c39662289be4cd5a150222e8456ff5577ce7622` (`v0.20.0-17`). Local commit only;
no push, merge, production code, dependency change, live recipe/environment/store
access, or live ordering. Requested Vasyl Dovhanchyn author/committer and
`Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>` trailer verified.

Files:
- `docs/design/generic-recipient-host.md`
- `docs/design/generic-recipient-fixtures.md`
- `docs/README.md`
- `test/fixtures/generic-recipient/policy-shapes.json` — independent public synthetic data
- `test/fixtures/generic-recipient/old-reader-payloads.json` — code hashes only, for the required old-reader probe

Read REVIEW1 in full; its SHA-256 is
`0364248b00f0c6f8747327d7b4592be5a2aa8bf280b545e8b71280e44d96d5fd`.

### Disposition table

| Finding / requirement | Disposition at HEAD |
| --- | --- |
| M1 hour arithmetic | Corrected and recalculated after splitting .2: excluding design/fence **52–80 h**; with fence **62–96 h**. Including design: **58–88 / 68–104 h**. Original unsplit table would have been 42–64 / 52–80, not the prior claims. Gate parses and sums all eight rows. |
| M2 modern family | §2 now requires resolved legacy family, names proposed startup error `recipient_policy_requires_legacy_family` with AF's exact explanation, and refuses before connecting. H02 includes modern HTTP(S) and modern stdio zero-connect controls. Rechecked exact A source. |
| M3 notifications | Outbox-tracked delivery MUST be id-bearing request form. Explicit notification negative row never settles dedup/cursor from silence or host-only traces; possible delivery stays uncertain without automatic replay. |
| M4 old-reader ambiguity | §5.1 identifies stale npm AF 0.21.0/CM 0.13.0 versus installed/Bun AF 0.22.2/CM 0.14.1/Chronicle 0.4.0. Added exact source/compiled entry hashes, runtime-root inventory hashes and native payload hashes. Installed resolved versions/payloads must be asserted before **every** attempt, including controls. No npm-lock reconstruction of a different reader. |
| M5 no-fence requalification | No-fence decision is a release-set manifest artifact with exact hashes and receipts. Any storage-layer payload hash change invalidates it and requires §5.1 re-run; no-native-barrier result requires the scoped fence. Inconclusive gates block enablement. |
| M6 paths, as corrected by manager | Both docs contain no machine-local input paths or drop handles. Private code/report inputs are SHA-identified. **Did not commit the private policy file:** final data are independently designed public relationships, with a new identity. Projection→fixture traceability appears only in this report. |
| M7 split .2 | F-host.2a = harness (10–16 h); F-host.2b = independent store probe (10–16 h), can start immediately with input/direct-storage preparation; final AF matrix still consumes the single integration tarball. Dependencies/totals updated. |
| L1 unknown fields | Strictness is limited to the new policy/subobject and new scalar type; unrelated unknown keys retain current tolerant handling. |
| L2 upstream/late connector | States directly that stock upstream has no recipient protocol. This does not resolve pr361 transition/recovery. If the connector is late, new-pair activation for that deployment is not proposed; old pair remains separate, never a supported D-store reader. |
| L3 CM patch premise | Rechecked exact CM 0.24.0 tarball/source open/config/registration path for absent application-reader floor. Not inferred from D's 0.24.1 note; not claimed as a native-barrier result. |
| L4 whole-result attestation | H02 now covers valid nested identity inside a non-data initialize result. Labeled in-process A boundary test because getters/prototypes/symbols/non-finite values cannot be a genuine JSON transcript. |
| L5 index | Added docs/README.md Design section linking both documents. |
| Soma condition + corrected matrix | §4 explicitly calls no-translator/connector-first a **proposal, not accepted preservation equivalence**. Three distinct columns cover stock upstream, frozen pr361 deployment pair, and proposed generic pair. Legacy S0/S1, future neutral, baseline-style peers and D-store readers have startup/admission/access/partial-transition boundaries. Cross-pair protected-binding upgrades are unsupported. Six H1/recovery/cutover differences are owner decisions; other implementation details are engineering calls. No live order. |
| Retained contract | H01–H12/S01–S06, same-frozen-destination-or-private reconnect behavior, request/ACK uncertainty, explicit managed-launch boundary, A/B/D pins and exact AF 0.31 fork-tarball requirement remain explicit. `^0.30.0` does not cover 0.31. |

### Required correction confirmations and private traceability (report only)

1. The committed public fixture is purpose-built, **not a copy or sanitized serialization** of POLICY-SHAPES. Its SHA-256 is
   **`399a883765023a3fbd529d75e166734b82a2196304554006868041daae3d9162`**.
   It has invented identifiers/profile sizes, no deployment pins, private paths,
   approval/task evidence, inherited ID lists or incidental live cardinalities.
   A transient untracked copy made under the earlier brief was replaced before
   staging/commit after the correction; no private JSON payload was committed.
2. The private projection supplied at
   `/srv/review-drop/soma-rtaln-policy-shape-20261030/POLICY-SHAPES.json` remains
   identified by SHA-256
   **`3574d8885580636a4269cadeb82bfb116695bccc62434209b62d853ecaa02911`**.
   The only conceptual traceability is: read/write/wake permission distinctions →
   invented mailbox ceilings; feature/tool allow/deny relationships → invented
   sample feature/tool masks; per-connection admission selection and profile/grant
   separation → independent off/admission-only/full/deny/no-leaf cases. Neither
   the projection nor the public fixture proves observed grants or runtime authority.
   Transport pins, timeouts, account/channel/user values, deployment instance/tool/
   repository counts and approval/deployment evidence were not transferred.
3. **Confirmed matrix separation:** absence of a stock-upstream recipient protocol
   removes nothing from upstream but does **not** settle preservation, H1 continuity,
   rollback or recovery for the deployed pr361 pair. The document separates those
   sets and identifies exact cross-pair failures and D-store boundaries. No live
   ordering, activation or owner acceptance is inferred.
4. Old-reader code manifest SHA-256:
   `b8cf27f96daa73f6a130f7346e84b35d9caf05b478c43fd5f8b3c0e519ad87d9`.
   This separate required payload-hash fixture contains no policy projection.

## Verification Gates

All new receipts are at clean HEAD `af8e4da79e8b728904fc0a34fbd09091419c6967`.
Evidence directory for **this round**:
`/home/orgasmic/.orgasmic/runs/run-01M678567E7DC6YEWX7XZHETYW/evidence`.

| Gate / command | Result / log |
| --- | --- |
| `orgasmic dispatch gate docs-contract -- python3 "$ORGASMIC_EVIDENCE_DIR/check-doc-contract.py"` | Final **PASS, exit 0**, reusable receipt. `gate-docs-contract-01M678MFF9QZN16GV5Y68HHN22.log`. Checks docs/data-only scope, parent/author/trailer, whitespace, new links/fences, full pins/AF0.31 wording, retained 18 row IDs, no private paths, independent fixture shape/hash, new boundary wording and arithmetic. No private input is needed for this docs gate. |
| `orgasmic dispatch gate source-pins -- python3 "$ORGASMIC_EVIDENCE_DIR/check-source-pins.py"` | **PASS, exit 0**, reusable receipt. `gate-source-pins-01M678M9745VTM07N9XD33467V.log`. Read-only A family/snapshot source, installed old versions/exports/file hashes and **1502** runtime inventory entries, stale locks, exact CM0.24.0 open/config/registration premise, stock absence vs legacy v1 offer/check. No package programs or stores executed/opened. |
| First docs-contract attempt | **FAIL, checker false positive**, not a document regression: `gate-docs-contract-01M678M921QXQPBW7MNVSZDB22.log`. Overbroad `/home/` substring matched prose `timer/home/module` and `locus/home/latest`. Checker now recognizes an absolute path boundary, retains strict no-`/srv`/drop-handle checks, and was re-run completely on the same unchanged clean HEAD. Failed receipt retained. |

Scripts are adjacent to this report and outside the worktree. The source gate
reads only the assigned pinned source/code inputs. An exploratory archive lookup
for a nonexistent config type member was corrected to the actual declarations
in `src/context-manager.ts`; the final source gate uses the actual member.

**Inherited evidence:** prior source reading of A `1d990e41`, B `bf6e8e0e`, D
`a5b02a07`, upstream base `2c396622`, legacy host oracle and the supplied O4/mapping/
static/I5 reports remains bounded to those unchanged immutable pins/content
identities. Earlier documentation at `9f153044` and its review are historical
context, not proof of the new files or a re-used gate pass. No prior gate receipt
was reused. New source checks re-verify the findings material to this fix round.

**Not run:** builds, behavioral connector tests, old/new store opens, outbox crash
matrix, compaction/restart or native compatibility probes. Those remain planned
qualification, not requirements to execute in this docs-only fix round. Source
absence of an API floor is not a native barrier or no-fence receipt.

## Unmet Criteria

None for this documentation fix round. No new peer/Opus review receipt is claimed.
The supplied REVIEW1 was read and all seven MEDIUM/five LOW items plus the corrected
public-fixture and compatibility-matrix requirements are addressed. Production
implementation, owner acceptance, integration-tarball identity and runtime gates
remain later work, not implied achievements.

## Residual Risk / owner decisions

The proposed target pair is not yet implemented or preservation-qualified. Owner
choices in §4/§7 remain unresolved and must not be converted to live ordering:

1. Approve unavailable legacy cross-pairs/no-translator target support, retain the
   frozen pair separately, or scope an H1-capable adapter.
2. Preserve a separately qualified H1 window or explicitly disposition the H1→H2
   effect delta; D is not a transparent H1 implementation.
3. Disposition any conversation/thread/status capability loss or representation
   change; B alone does not provide enabled typing/root ownership.
4. Disposition changed ACK/dedup/outbox, notification tracking, loss/retry and
   uncertain-content/operator recovery behavior; no exactly-once or retro-replay.
5. Resolve dependencies on cross-epoch completion, hinted/old-epoch/coalesced
   traffic and unbound automatic output before migration; otherwise separate design.
6. Choose the one-way D store/rollback and qualified native-barrier or managed-
   launch boundary, supported entry points and uncertainty handling. No protection
   of arbitrary SDK readers and no stale-history restore.
7. Decide which connections may enable automatic replies and any independently
   approved external operator notice route. B's internal notice does not create one.

Everything else is an engineering call within that policy boundary: exact
integration/versioned AF0.31 tarball, optional-authorize typing reconciliation,
neutral identity/channel representation, validators, error mapping, tests,
packaging/reproducibility and execution of the slices. Requalification of any
changed storage payload is mandatory; the source-only CM check cannot waive it.
