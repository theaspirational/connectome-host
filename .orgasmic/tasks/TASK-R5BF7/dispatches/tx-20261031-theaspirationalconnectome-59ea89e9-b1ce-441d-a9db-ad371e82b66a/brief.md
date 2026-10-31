# Implement fix round — Dispatch Brief
```brief
R5BF7 LOW-only round, from 0f18209b673058bab2b7c5ac695409d6ab632020. Task operator: Tarn; constraints: Soma. Review: /srv/review-drop/TASK-R5BF7-REVIEW1.md (sha256 0cbc1dda57233de2fe39244fd023f3e1d32b3ed3a8b4b577484d5b30129672e4).
1. docs/design/f-host-h-brief.md:133 (the ci.yml row): split the range attribution per claim (baseline Node 22.12.0 job vs the lockfile-agreement job :139-155 = Node 24 + Bun 1.4.3), or write "pinned Node 22.12.0/24 per job" with the correct lines. Verify each line against ci.yml.
2. A note in the brief (one sentence near :395-404): the brief stays outside --docs-contract until a separate checker fix (the regex at checker:233); don't change the checker in this round.
Nothing else. Gates (bounded, as before): --self-test, --docs-contract, git diff --check; fix evidence/verify-brief.py so it ASSERTS set(cited ids) ⊆ fixture ids (untracked evidence script) and run it. LOCAL ONLY, no push. Commit: Vasyl noreply author/committer, trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-0312b6e1-1f8a-441b-a67c-146d5325ba3b, run run-01M67YVEVJ0E5T9ZN195TJ7NWM, in your own native session and the same worktree.
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