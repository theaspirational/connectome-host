# Implement fix round — Dispatch Brief
```brief
TASK-EC6WW fix round 2 (task operator Tarn, qualifier Soma; not owner). LOCAL ONLY. From 698bb5870116d14e45ae5e78ff1bdf37fb95c144. Review: /srv/review-drop/TASK-EC6WW-REVIEW1.md (sha256 dba9dc4fa456e237972924516d600423c74810bda2f8fd1939329efae9208d2f) — read it in full.
Do:
1) MEDIUM-1: add CONV to both alternations in internalIdentifier() (check-scope-contract.mjs:231-232) + an 'uppercase prefix CONV' control (synthetic, e.g. CONV-EXAMPLE) and a lowercase-with-digit control for conv (synthetic). Mutant m5: remove CONV → a named kill.
2) MEDIUM-2: fix the comment at :227 to the actual semantics ("fully-uppercase tokens remain reserved; any other casing requires a digit in the suffix") and add a short comment: KNOWN FALSE NEGATIVE — non-uppercase tokens with an all-letter suffix (≈17-20% of real 5-character ids), and `_`-adjacent tokens (\b). Do NOT broaden the predicate in this round (that is a semantic choice for the qualifier); don't relabel the FN as acceptable.
3) Add controls that FIX the known FN as an explicit expectation (e.g. 'known false negative: lowercase all-letter suffix' → accepted, labelled as a known gap), so a future change of semantics is visible.
Only synthetic values; no real ids/ULIDs, including assembled from parts (search for reconstructions in the diff and the messages).
Gates (bounded: 16G, swap 0, timeout 600s, 32 MiB, PIPESTATUS, no retries, node /opt/node-v22.23.3-linux-x64/bin/node): head --self-test, --docs-contract, m1-m5 named kills, git diff --check c353e1c8 HEAD. Label by head + checker sha256. Report: the history isn't cleaned (a5017576 is a known positive finding); PASS = only the predicate/inputs. Commit: Vasyl noreply author/committer, Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>. Short report.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-84e254d4-9bbd-461c-ba0d-42889cde22b2, run run-01M6898ZKQ32H2DASBRH3B1C3A, in your own native session and the same worktree.
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