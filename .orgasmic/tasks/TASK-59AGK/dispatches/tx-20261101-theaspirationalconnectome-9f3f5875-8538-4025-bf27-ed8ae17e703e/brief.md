# Implement fix round — Dispatch Brief
```brief
Fix round 1 for TASK-59AGK (connectome-host #523 bot-review fixes). This round is packaging and wording only. Read /srv/review-drop/TASK-59AGK-REVIEW1/report.md in full.

You are on a NEW branch starting at the PR head 925e4f70758a8d6e738bc70f0f3c1eb773be0a27. Recreate the content of 7919c9dc84c2616c81416e76a757cea4c7fee82a as a single commit **without replies.md**. Cherry-pick it and drop replies.md from the commit. The tracked tree must not contain replies.md, and you must not add a .gitignore entry for it.

Apply these review LOWs in the same commit, or in a second commit:
(a) test/framework-error-policy.test.ts:124: in the above-ceiling case, separate a ceiling refusal from attempt exhaustion where that's feasible, and pin that statedWaitMs above the ceiling is dropped.
(b) test/capture-retry-create.preload.ts:19: assert the framework fallback policy's identity if that's feasible without brittle internals; otherwise leave a comment documenting the limitation.

Write the reply drafts only to $ORGASMIC_EVIDENCE_DIR/replies.md, never commit them. Key them by the three thread URLs and use the new sha(s). Use the wording fix: "this follow-up changes no runtime behavior", not "Runtime behavior is unchanged".

Gates at the final head: `bun test` (move the generated logs/ into evidence as before), the host typecheck, and the web typecheck (`--project web/tsconfig.json`). Use PATH=/srv/ci-fixtures/bun-1.4.4-linux-x64:/opt/node-v22.23.3-linux-x64/bin:$PATH. Confirm `git ls-files replies.md` is empty and that the diff from 925e4f70 to head matches 7919c9dc minus replies.md, plus any test changes from (a) and (b). No push, no GitHub writes.

Before any checks, write the first progress note to $ORGASMIC_EVIDENCE_DIR/progress.log with `date -u` and HEAD, then another every 5 minutes.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261101-theaspirationalconnectome-ef8cc478-3e39-42bb-9fdf-6b7450a7d265, run run-01M6B9B2A6PF2RRCTDSFA6HZ5R, in your own native session and the same worktree.
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