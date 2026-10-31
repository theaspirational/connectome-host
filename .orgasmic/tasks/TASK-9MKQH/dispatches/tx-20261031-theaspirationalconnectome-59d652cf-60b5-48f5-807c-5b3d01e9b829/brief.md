# Implement fix round — Dispatch Brief
```brief
9MKQH LOW-only round, from 7e93298728393972fa5a967a7e21c5dda7b5aafd. Task operator: Tarn; constraints: Soma. Review: /srv/review-drop/TASK-9MKQH-REVIEW1.md (sha256 bb172f7f7c53a2db5f4f69157c01ddb8348468ff58338f50f3f7804d50125437).
1. f-host-t2-brief.md:192 (t03-native-send-access-revoked): add a clause naming the host part as covered by t02-reply-only-denied / t02-author-access-storage-only (:184-185), OR move it to `T2 +T4` with a storage/wake-eligibility mutant; choose the one consistent with the rule at :155 and justify it in one sentence.
2. f-host-t2-brief.md:13: narrow the citation to `:700–708` (T1–T5 rows :702-706, T total :707, AF-scope :708).
3. Note only: the t2 typecheck.ts directives have never been compiled; the first action of a future round with the declared TS 7 = `tsc -p test/stubs/f-host-t2/v0/tsconfig.json` + the input-file list as in f-host-2a-brief.md:211-232. Don't compile anything now.
Nothing else. Gates (bounded as before): --self-test, --docs-contract, the allocation audit (57/57), git diff --check. LOCAL ONLY, no push. Commit: Vasyl noreply author/committer, trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-0258f529-2f53-4f7d-b0bb-57c8425816e4, run run-01M67YP2548BACQSKEP8MKY1QE, in your own native session and the same worktree.
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