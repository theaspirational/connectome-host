# Implement fix round — Dispatch Brief
```brief
TASK-6JAEQ LOW round 2 (LOCAL ONLY, no pushes), from c9f0991d20e9e2476f7ff57b09f1f2685671d97a. Review: /srv/review-drop/TASK-6JAEQ-FIX1-REVIEW.md (sha256 27d354e49fb53e4ce4f9af7a0defd006f8447bf07db711953ea2ef4a95b432e7) — read the 4 LOWs. Task operator: Tarn; qualifier: Soma; not decisions by the owner Vasyl.
DO: (LOW-1) docs/design/recipient-policy-staging.md:11-14 — narrow the sentence to the actual scope: refuses `recipientPolicy` as an own key of a server definition under the servers field (+ the reasons why the top level isn't read). DON'T change the walker. (LOW-2) add docs/design/recipient-policy-staging.md and test/stubs/f-host-3/** to the docsContract() input list in test/fixtures/generic-recipient/check-scope-contract.mjs; the gate stays PARTIAL; if new files produce hits — record them, and if it's a genuinely private name — fix the text, don't weaken the checker. (LOW-3) quiescence.py:69 is an evidence script in the old run's evidence; DON'T edit or replace the old receipt. If there's an in-tree copy of the helper — fix the line to `exit=<rc> holders=<n>`; otherwise — just record the explanation in the report (rc=1, 0 bytes = no holders). (LOW-4) extend the unmet-coverage paragraph of the staging doc to bug-report.ts:341 and :355-357 (+ note that :355 became reachable via the overlay scanner); DON'T change src/bug-report.ts.
GATES (existing roots/closure, no install, the disk is nearly full): --self-test and --docs-contract (head), focused bun test of the three recipient files, git diff --check; profile-off/feature-off unaffected — prove the src diff is empty for runtime files (if src changes — STOP and ask). Bounded: 16G scope, timeout 3000s, 64 MiB cap, PIPESTATUS, no retries. Commits: Vasyl noreply, trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-812ccd65-fd4f-4a61-b69c-60abc6e3771e, run run-01M682G1X93YCCNXW0A8725SAN, in your own native session and the same worktree.
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