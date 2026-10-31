# Implement fix round — Dispatch Brief
```brief
A821A LOW-only round, from 8dd238b5cb5b18c89d64512c1cac8d439ffdc2c4. Task operator: Tarn; constraints: Soma. Review: /srv/review-drop/TASK-A821A-FIX1-REVIEW.md (sha256 5dec2e351f86575764af753172949bf67024d638176af743ea6f8ce27b8a5747).
1. brief:270: rewrite the garbled sentence (e.g. "- Exact composed A+B+D integration release: SHA, tarball hash/manifest and supported entries.").
2. check-scope-contract.mjs: move the import.meta.url and .github/workflows/ci.yml reads into the same guarded loop in docsContract() (:94-108): missing → --docs-contract exit 1 with the file name; default/--self-test don't depend on them.
3. Notes only (no logic changes): in the report/brief, say there are 2 independent detection rules plus a message discriminator (toolingEnvironment ⊂ toolingCli); in the brief, document that the mktemp -d fallback of bounded-gate.sh leaves a log directory and that cleanup is the caller's responsibility.
Nothing else. Gates (bounded as before): --self-test, --docs-contract (PARTIAL), the missing-file control for all 7 inputs (5 stubs + ci.yml + the checker source if it applies) in a temp mirror: docs-contract exit 1 with a name, self-test/default exit 0; git diff --check. The "token" in the controls = a synthetic lexical marker only, no real credentials/secrets. F1 PENDING. LOCAL ONLY, no push. Commit(s): Vasyl noreply author/committer, trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-e5398517-47db-4543-96e9-252a720547e2, run run-01M67W4JC6WTSCP6Q62QW7KBY1, in your own native session and the same worktree.
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