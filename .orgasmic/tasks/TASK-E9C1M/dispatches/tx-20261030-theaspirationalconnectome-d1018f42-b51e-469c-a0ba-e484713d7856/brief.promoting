# Implement fix round — Dispatch Brief
```brief
F-host design fix round 2 (gen:exec; docs and fixtures only; local; no push). Review: /srv/review-drop/TASK-E9C1M-REVIEW2.md (APPROVE; 6 MEDIUM, 2 LOW). Read it in full and close all of them, following its "Fix Directions":
1) host.md:318: in the frozen pair, the host bridge gates WAKE only (shouldTriggerInference); storage rests on connector ACL plus AF grants. Fix the cell. Add an OWNER decision: the new pair doesn't store what the old one stored silently without waking (channels outside readChannels → store:false), so ambient context is lost. Plus a fixture row asserting that consequence.
2) host.md:320: D's two profile-independent wire deltas (top-level push `delivery` → -32602; ungranted incoming batch with item `delivery` → capability_denied for the batch) go into the baseline-peer × new-pair cell, cross-referenced to §2, plus a fixture case for an unconfigured peer.
3) overlay-cannot-widen: drop-with-note (grantWidening:false plus an operator note), not rejection. Add a separate case: replacing a protected recipientPolicy connection → configurationRejected.
4) old-reader-payloads inventoryAlgorithm: recompute the AF digests under plain lexicographic (bytewise, LC_ALL=C) sorting of path strings, update the text and the gate script, and verify independently with `find | LC_ALL=C sort`.
5) host.md:107-111: fix the false provenance (use the fixture's values, or say they're harness identifiers).
6) LOW: remove the Opus-review line and the internal report file names (TASK-…md) from the docs: "privately supplied report, SHA-256 …" without a name. Number the owner decisions ONCE (§4) and have §7 reference them.
7) NEW REQUIREMENT (Soma, preservation contract): exact Slack THREAD replies are part of the preservation contract (the existing Slack workflow replies into exact threads). Narrowed B (channel-only, no thread transport) does NOT approve losing that. Add a section: F-host/F-Slack and the explicit reply tools must preserve thread identity through native thread-capable adapters alongside generic channel-only B (how the thread reference travels: a registered destination/sub-conversation with a neutral identity, explicit tool parameters, automatic reply into the thread of the triggering message under B's frozen-destination rule). Or name the exact real gap as an owner decision. Add fixture rows (an explicit reply into a thread; an automatic reply into the triggering thread; a stale thread binding → private).
8) History: DON'T rewrite the branch now. In the report, give the exact list of what's private in 9f153044 (review finding 6). Publication will happen later from a fresh commit of the final tree.
New commit(s) on top of af8e4da7; same identity plus the GPT-6.1-Sol trailer. Re-run docs-contract (+ path check over BOTH JSON files, + no TASK-ids in the docs) and source-pins with the new sorting. Report: head, a disposition table, and the final numbered owner list.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-9c6c57f5-6c54-4830-9a71-3c837f73db99, run run-01M678567E7DC6YEWX7XZHETYW, in your own native session and the same worktree.
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