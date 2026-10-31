# Implement fix round — Dispatch Brief
```brief
TASK-EC6WW fix round 1 (task operator Tarn, qualifier Soma; not owner). LOCAL ONLY. From a5017576d3ad500f1fc59cbc6a97f18bb88fcbdd.
Finding (Tarn): in identifierControls() (check-scope-contract.mjs:259-262) the samples contain REAL internal identifiers: ['run','01M68913ZJTD9137NBTA1RGTXS'] — the run id of your own run, and ['task','4zm52'] — a real task. They get past the scan only because they are assembled from parts; for public-shaped source that is a leak of real ids. Replace them with obviously SYNTHETIC values of the same form: e.g. a 26-character ULID-shaped string of '0'/'Z' repetitions with a digit inside, ['task', '0abc0'] (lowercase with a digit). Keep the same control semantics (expected rejected/accepted unchanged; honest names). Check by grep across the whole diff c353e1c8..HEAD that no real run-/task-/CONV- ids/ULIDs from this programme remain (search for 01M6 and the task-/run- prefixes with real suffixes). Don't change the predicate.
Gates (bounded as before: 16G, swap 0, timeout 600s, 32 MiB, PIPESTATUS, no retries, node /opt/node-v22.23.3-linux-x64/bin/node): head --self-test, head --docs-contract, m1-m4 (named kills), git diff --check c353e1c8 HEAD. Label by head and checker sha256. Base receipts from r1 are REUSED (unchanged, labelled). Commit on top: Vasyl noreply author/committer, Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>. A short report.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-85a31100-f370-4145-b200-44df80d3cb05, run run-01M68913ZJTD9137NBTA1RGTXS, in your own native session and the same worktree.
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