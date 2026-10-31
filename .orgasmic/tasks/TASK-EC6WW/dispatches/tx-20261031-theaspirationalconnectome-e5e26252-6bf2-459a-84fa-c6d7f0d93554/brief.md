# Implement fix round — Dispatch Brief
```brief
TASK-EC6WW fix round 3 (task operator Tarn, qualifier Soma; not owner). LOCAL ONLY. From 8d211982eaaf59b83713ebe236e5c5c91715e00e. Review: /srv/review-drop/TASK-EC6WW-REVIEW2.md (sha256 ae9e909e1c3b7b94d1ec986ca02b64615deac1d76efcf26ed7ccdb0675d99562) — read it in full. Do only:
1) MEDIUM collision: describe it as a CLASS in the comment (checker ~:279) and the report — "any 5-character token after task-/conv- without i/l/o/u"; add named collision controls task-state and task-graph (rejected=true). No exclusions.
2) MEDIUM limitation: fix the comment and the report — the source-minted families not covered by the shape: ART-, CMT-, SEC-, dec_, term_, gotcha-, MEET- (cite shipped/... :ID_PREFIX: read-only, as in the review); AGENT/PROJECT are not minted families, only legacy rules. Don't add new detectors (that's not this round's scope) — name them as REMAINING LIMITATION. Optionally one control per family showing the current MISS as a 'remaining limitation:' (synthetic values).
3) MEDIUM lookahead: add ['non-shape node all-digit stem task', ['x_task','01234_x'], false]; mutant m10 = removal of the lookahead → a named kill.
4) MEDIUM forbidden names: in the benign identifier controls inside docsContract, assert only internalIdentifier(sample)===false (not the whole neutral()), so SCOPE_FORBIDDEN_NAMES doesn't break them; a control: SCOPE_FORBIDDEN_NAMES=model → docs-contract exit 0 (and a real injected name in a synthetic sample is still rejected — keep that existing check). Record both runs.
5) LOW %2F: name it a limitation in the comment + a control ['%2Ftask','zzzzz'] pinning the current MISS as 'remaining limitation: percent-encoded adjacency'.
Synthetic values only; check the diff and messages for reconstructed literals. Gates (bounded 16G/swap 0/600s/32MiB, PIPESTATUS, no retries, node /opt/node-v22.23.3-linux-x64/bin/node): --self-test, --docs-contract (with 0 names, and with SCOPE_FORBIDDEN_NAMES=model), m1-m10 named kills, diff --check c353e1c8 HEAD. Labelled by head+checker sha. Vasyl noreply + Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>. History isn't cleaned (a5017576 known positive); PASS = only the predicate/inputs. Short report.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-fd057305-fee9-4db5-ad7e-07a5563b714e, run run-01M689RQ8R8ZRCF0GFC588MZEC, in your own native session and the same worktree.
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