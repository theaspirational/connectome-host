# Implement fix round — Dispatch Brief
```brief
Docs-only fix round on top of 8d068b4badcc9eebe65aabba6c1f960c8494eb03 (amend into the single commit so the result stays one commit on 8f2bb103; same author Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>). Review approved; fix exactly these, nothing else:
1. MEDIUM changelog.d/470-turn-retry.changed.md: state that a recipe that already sets agent.retry moves turns onto the recipe-derived policy, so 529 sends drop from 13 to 7 and stated-wait ceilings drop from >=10 min to max(60 s, maxRetryDelayMs); give the migration (set overloaded.maxRetries / maxRetryDelayMs explicitly, or omit agent.retry to keep the framework default). Keep it .changed (maintainer may reclassify).
2. LOW src/membrane-retry.ts header: one sentence pointing to src/framework-error-policy.ts for the turn half, so it no longer implies agent.retry does not reach turns.
3. LOW README.md ~302-305: drop the 'same framework instance' fleet-children clause; say fleet children each load their own recipe.
4. Replace the trailer with `Co-Authored-By: GPT-6-Astra <noreply@openai.com>`.
Gates: bun install --frozen-lockfile, bun test, bun x tsc --noEmit (Bun 1.4.3 as before). No push. Report new sha and diff vs 8d068b4b.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261028-theaspirationalconnectome-b17ceb53-9c25-4da0-8d7c-5dd29615bdc0, run run-01M628Q85HN1HVBHP7H2WZ5VT6, in your own native session and the same worktree.
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
Blocking decision: `orgasmic dispatch ask "<question>"`; keep it alive for the stdout answer. A timeout is not permission. With `--no-wait`, answers/messages arrive at turn boundaries, not between tool calls. If finalize refuses for pending messages, handle their full text and retry. Successful finalize is terminal; no work follows it. Never disclose credentials or the run token.