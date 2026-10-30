# Implement fix round — Dispatch Brief
```brief
T1 LOW round 3 (TASK-KY73N), on eba367392801c25e52b7793214fca80d2f817345. Read /srv/review-drop/TASK-KY73N-FIX2-DELTA-REVIEW.md in full. Fix ONLY its 3 LOWs, no semantic changes to cases/expectations/permissions:
L1 doc :198-203: add to the path-1 citations the call-time refusal A src/framework.ts:35811 (dispatchMcplToolCall from :35792, `if (!isToolAllowed(...))` → denied by tool policy); say that tool-allow policy is enforced both when listing (:35473) and at dispatch (:35811); keep "without a call-time CapabilityGrant check". Verify it via `git show 869bb3add7b48893d48b2ce50ea423e4b33fdfbc:src/framework.ts`.
L2 check-scope-contract.mjs:305: the mutant loop collects ALL mismatching case ids and asserts the set equals exactly [named id] (deepEqual); the self-test must stay green; add a negative control that a mutant with 2 mismatches is rejected.
L3 scope-cases.json: rename parentId "mailbox/denied" in dynamic-parent-claim-native-disagreement to a neutral one (e.g. "mailbox/other-joined"), only in that case and its note; the expected outcome is unchanged. Update the fixture sha in the doc.
Gates: --self-test, --docs-contract (SCOPE_FORBIDDEN_NAMES via the gate env), scope-runner (the CI block without the list → PARTIAL exit 0), scope-boundary (parent eba36739). Vasyl noreply + trailer Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>. Calls on asks belong to the task operator Tarn. No push.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-4f6e6427-cc67-4c7e-91a6-2093e39791fa, run run-01M67GTE9D3GPPXT5MATK20Q6P, in your own native session and the same worktree.
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