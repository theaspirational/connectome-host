# Implement fix round — Dispatch Brief
```brief
F-host design fix round 1 (gen:exec; docs only; local; no push). Review: /srv/review-drop/TASK-E9C1M-REVIEW1.md, APPROVE with 7 MEDIUM and 5 LOW. Read it in full and fix all of them:
- MEDIUM hour totals: recompute exactly.
- MEDIUM modern-family: inboundAdmission is legacy-family only. Add it to the §2 validation list with a named startup error, plus a negative fixture row.
- MEDIUM notifications: the outbox requires request-form (id-bearing) delivery for tracked items. Add a negative fixture: an item delivered as a notification is never settled.
- MEDIUM old-reader: the oracle's package-lock.json is stale (AF 0.21.0/CM 0.13.0) while bun.lock/node_modules are AF 0.22.2/CM 0.14.1. Pin the exact old payload hashes per entry point, and make "assert installed versions before each attempt" normative.
- MEDIUM fence requalification: the no-fence decision is a release-set manifest artifact. Re-run §5.1 whenever any storage-layer payload hash changes.
- MEDIUM paths: vendor the synthetic policy shapes into an in-repo fixture dir (copy the content of /srv/review-drop/soma-rtaln-policy-shape-20261030/POLICY-SHAPES.json and keep its SHA-256 as the identity; if you can't read it, say so in the report and use a placeholder plus the SHA). Remove every /srv/* and review-drop path from both docs: "supplied privately, identified by SHA-256".
- MEDIUM split F-host.2 into 2a (harness) and 2b (store probe, can start right away, ~10–16 h).
- LOWs: scope "unknown fields → fail" to the new policy objects only; state directly that upstream has no recipient protocol and what happens if F-Slack is late; recheck the CM no-floor at 0.24.0; an H02 negative control for a non-data initialize result; add docs/README.md Design section.
PLUS Soma's condition (required): no-translator/connector-first is a PROPOSAL, not accepted preservation equivalence. Add a compatibility matrix: {old host (pr361-v1 legacy), new host} × {legacy Slack recipient connector S0/S1, future connector with neutral admission, Telegram/GitHub/orgasmic-style peers}. For each cell: does it start or fail at initialize; where admission/access is enforced before storage/wake; what fails closed during a partially completed transition (including an old release reading a store written by D). Then explicitly list any difference that removes H1 compatibility or changes supported recovery/cutover; those are owner decisions. Everything else is an engineering call. Keep H01–H12/S01–S06 and the reader-fence boundary explicit. No live ordering.
New commit(s) on top of 9f153044, same identity and trailer. Re-run your docs-contract gate, updated for no /srv paths. Report: head, a disposition table, and the owner-decision list.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-63f25cf1-22a4-4cfb-abc8-3a0289abd71a, run run-01M676Y8DFF5Q14C9VNXBEX5AP, in your own native session and the same worktree.
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