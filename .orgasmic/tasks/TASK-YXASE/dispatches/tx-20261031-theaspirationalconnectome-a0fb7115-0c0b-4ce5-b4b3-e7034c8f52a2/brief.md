# Implement fix round — Dispatch Brief
```brief
TASK-YXASE follow-up round, DOCS/REPORT ONLY (LOCAL ONLY). Operator Tarn, qualifier Soma, not owner. Continue in the same worktree from 8df256cc.
Input: /srv/review-drop/TASK-YXASE-REVIEW1.md (sha256 8d8ee5002e803b24c2cf3f07894a73c3ab415c7cf7898a48c628f5a935ca4a0d). Verify the sha; on a mismatch, STOP.
1. F1 MEDIUM: in docs/design/dynamic-selector-core.md, reclassify `a04-joined-after-startup` and `dynamic-unreadable-join` as `shared witness`, because their witnesses are shared with `dynamic-fresh-epoch-readmission` and `t03-root-access-revoked`. Restate the counts as 19 independent / 11 shared / 19 witness-only, and update the shared-family description. No new fixtures, no code change. Do not edit the first commit cd5edcd8 (it stays the historical allocation); make a new commit.
2. F3 LOW: in the report, state that docs-contract `neutral()` fail-fasts on input #1 (the checker's own source, `run-mutants` FP), so the gate never reached the 10 new files. Cite the actual neutrality evidence: checker-inputs.json, the manual read, and the reviewer's receipt gate-t2-review-neutrality-probe-01M68DP2VPENX5WZ591C5WNEMC.log (exit 0, all 10 new files clean). Don't change the checker.
3. F2 LOW: carry it as a known gap for the projector slice; no change.
4. Gates: only `git diff --check` and a reread or grep proving the counts in the doc and the report match (49 = 19+11+19). Run no suite or docs-contract (it stays FAILED, recorded). Bounded wrapper as before.
Report: a full report with the new head, the changed lines, and the corrected row-accounting table. A neutral commit message, author/trailer as before. Progress note every 5 minutes.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-e5cb1e5a-ce30-41ee-b6e8-6aa0f09c3353, run run-01M68BQNWKZMKDDVVHNTD2P4MQ, in your own native session and the same worktree.
Changed this round: the brief above narrows the assignment; use this run's evidence directory and report, not the earlier round's destination.
Original instructions are history, not missing context. Original scope, security and read-only-ledger boundaries still apply, narrowed by this brief. Do not replay startup reads unless changed facts or this brief require them. Do not merge or close the dispatch.

## Evidence and verification
Earlier reads/code/reports remain context, not new proof. Identify valid inherited evidence and its unchanged inputs/scope in this round's report. No earlier gate pass is implied by resuming. Verify findings against current source. Run assigned checks and scoped probes through `orgasmic dispatch gate <name> -- <cmd> [args...]`; missing receipts do not assign a full suite. Keep builds outside evidence.

## Completion for this round
Report Changed, Verification Gates (commands/results/logs and inherited evidence), Unmet Criteria, and Residual Risk in `$ORGASMIC_EVIDENCE_DIR/report.md`. If unset, substitute a scratch path outside the worktree below.
For done, satisfy the narrowed brief and make a local commit in this worktree; stage explicit paths and do not bypass hooks. Then run:
`orgasmic dispatch finalize --status done --summary-file "$ORGASMIC_EVIDENCE_DIR/report.md"`
If blocked, report the exact unmet criteria and run:
`orgasmic dispatch finalize --status blocked --reason "<why>" --summary-file "$ORGASMIC_EVIDENCE_DIR/report.md"`
Blocking decision: `orgasmic dispatch ask "<question>"`; keep it alive for the stdout answer. A timeout is not permission. With `--no-wait`, Codex app-server accepts answers/messages mid-turn; other multi-turn harnesses receive them at turn boundaries or finalize. If finalize refuses for pending messages, handle their full text and retry. Successful finalize is terminal; no work follows it. Never disclose credentials or the run token.