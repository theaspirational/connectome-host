# TASK-E9C1M — F-host design and shared stub contract

## Changed

- Design: `docs/design/generic-recipient-host.md`.
- Shared fixture specification: `docs/design/generic-recipient-fixtures.md`.
- Local HEAD: `9f153044ce63b8da719a0132c0817901f1031216`, branch `task-e9c1m-impl`.
- Parent/base: `2c39662289be4cd5a150222e8456ff5577ce7622`, verified `v0.20.0-17-g2c396622` (package 0.20.0). The explicit brief overrides the older assignment-summary base.
- Author/committer: Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>; requested GPT-6.1-Sol co-author trailer present.
- Documentation only: two added Markdown files, 670 lines. No production code, runtime fixtures, dependencies, lockfiles, framework/legacy inputs, recipes, environment, private stores or main checkout changed. No push, PR, merge or live operation.

The design specifies neutral per-connection admission/reply policy, operator-only configuration/overlay boundaries, identity and attestation, global D profile plus explicit per-connection grants, defaults-off compatibility, no legacy translator with connector-first release gating, a synthetic old-reader/new-store probe preceding any conditional managed-launch fence, and host PR slices with hour estimates. The fixture contract defines H01–H12 and S01–S06, controls/observations, old negative controls, and pending/ACK/outbox crash states.

### Confirmations of manager additions

1. A is pinned at `1d990e4195e265f58be865719df2bb7e51f5c456`; B at `bf6e8e0eeaa38b086bb8d05bd4efe3a3d2f54cd9` to be rebased onto A; D at `a5b02a07fd4c380ded751e01ada2ce3e6b242cca`. Implementation is gated on ONE named AF integration commit and a versioned local tarball, not moving heads.
2. **AF 0.31 requirement taken into account:** host `^0.30.0` does not cover 0.31. A later release manifest and both locks must resolve exactly the versioned fork tarball with integrity, source/build/export provenance and reproducibility; no vanilla 0.30 substitution or private-machine artifact paths in public PRs. No dependency change is made in this design-only step.
3. Stale-epoch loss gates both workstreams. Proposed connector pending/outbox handling commits dedup only after final host acceptance, never just A's verdict. Known pre-storage refusal may receive bounded fresh re-admission; uncertain ACK is quarantined, not blindly replayed. Old committed receipts are never retro-replayed; retries are not exactly-once. Epoch/admission checks remain strict.
4. Reconnect never re-resolves. Fresh authorize/current-access checks can only approve the same frozen destination; stale binding keeps prose private with B's internal readable agent notice. An external operator notice route is only an owner-decision proposal. Uncertain tool effects are never repeated automatically.
5. Verified policy file `/srv/review-drop/soma-rtaln-policy-shape-20261030/POLICY-SHAPES.json` SHA-256: **`3574d8885580636a4269cadeb82bfb116695bccc62434209b62d853ecaa02911`**. Used only for synthetic config/stub examples, explicitly not observed grants or runtime authority.

## Verification Gates

- PASS, exit 0, at clean HEAD `9f153044ce63b8da719a0132c0817901f1031216`:
  `orgasmic dispatch gate docs-contract -- python3 "$ORGASMIC_EVIDENCE_DIR/check-doc-contract.py"`.
- Reusable receipt recorded. Log:
  `/home/orgasmic/.orgasmic/runs/run-01M676Y8DFF5Q14C9VNXBEX5AP/evidence/gate-docs-contract-01M677M156AFHCX44AX5KRZWZB.log`.
- The script checks exact changed paths/base/author/trailer, `git diff --check HEAD^ HEAD`, local Markdown links/code fences, full A/B/D pins, presence of all 18 matrix row IDs, connector-name section boundaries, the supplied policy SHA, and AF 0.31 artifact wording. This is a static documentation/scope check, not proof of behavior or semantic completeness. Script is adjacent to this report, outside the worktree.
- Read-only source inspection: supplied baseline and orgasmic skill, `CONTRIBUTING.md`, host merge/startup/config paths, exact A/B/D docs/API sources, programme design, the five legacy host files, and the four supplied task reports. No applicable AGENTS.md found; supplied conventions/gotchas absent. Frozen fixture package versions independently read as AF 0.22.2 / CM 0.14.1 / Chronicle 0.4.0. Policy file hash independently computed with `sha256sum` and rechecked in the gate.
- No builds, behavioral tests, connector executions or store probes run: deliverable is design/specification only. All proposed runtime/old-reader/outbox gates are explicitly unexecuted. No production-path behavior is claimed.

## Unmet Criteria

None for this design-only worker delivery. Opus review has **not** occurred: manager confirmed it is a manager-owned post-report gate, not worker acceptance (20:11:46 UTC response). No review receipt or integration/compatibility PASS is claimed.

## Residual Risk

The composed AF integration commit/tarball does not yet have a supplied identity. B's inspected working type still requires `authorize`, whereas the dispatch contract makes it optional; host design always supplies it. New neutral identity format and registered sub-conversation representation need agreement and implementation. The no-translator recommendation requires owner sequencing approval; it is not current legacy compatibility. Native compatibility and the conditional fence boundary remain empirical/operational gates. Source-level metadata retention is not compaction+restart or old-reader safety. The outbox proposal exposes uncertainty, not exactly-once delivery. Enabled typing requires separate slice-C/connector evidence. No live migration approval follows.

### Open questions — owner decisions

- Approve connector-first ordering/no host translator, or scope a separate adapter design.
- Preserve separately qualified H1/H2 opt-ins and choose connection automatic-reply enablement; close O4 and unbound-post dependency inventories before live migration.
- After the synthetic store results, approve the exact native-barrier or managed-launch enrollment/rollback boundary and supported operational entry points.
- Decide whether an independently authorized external operator notice route is wanted; internal B notices already apply.

### Open questions — engineering calls

- Produce the single reproducible AF 0.31 integration tarball/manifest and reconcile optional `authorize` typing.
- Agree neutral identity response and genuine registered sub-conversation destinations without thread/initialize-offer API invention.
- Preserve upstream grant ownership; broader server-file controls require separate scope.
- Pin isolated runtime/native inputs and instrumentation; execute old-reader mutation, D compaction+restart and outbox crash matrices.
- Inventory every managed storage-open path if the fence is needed; qualify enabled typing separately.
