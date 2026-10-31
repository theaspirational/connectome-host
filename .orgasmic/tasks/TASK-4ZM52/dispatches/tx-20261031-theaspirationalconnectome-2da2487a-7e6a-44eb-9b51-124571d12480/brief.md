# Implement fix round — Dispatch Brief
```brief
TASK-4ZM52 LOW round after review APPROVE (report /srv/review-drop/TASK-4ZM52-REVIEW1.md, sha256 8f7c7341ff4d5bdac5cb89e75790f4fd4950688c139d24424da9835878c6b1e0 — read it in full). Task operator Tarn, qualifier Soma; not owner decisions. The v4.3 brief (2fafa3d4…2e84) and the engineering STOP disposition stay in force; LOCAL ONLY; production recipient_policy_integration_pending is not lifted; recipe.ts/index.ts/recipient-policy.ts unchanged; no new RefusalCode/error string/wire field/grant.
Work (only this):
LOW-1: assert the exact key set of replyAuthority (['timeoutMs','resolve','authorize']) next to test/recipient-reply.test.ts:48-50 (and the exact keys of admit's success result where they are not already exact toEqual); add a fifth case to check-return-controls.mjs: an excess nested member, bound directly to the annotated type (a typed local, not via Object.freeze) → tsc must fail. If the annotation can be honestly tightened so that tsc catches an excess member in replyAuthority without a cast — you may, but don't break the four existing negatives; otherwise rely on the runtime key-set assertion and write that down.
LOW-2: replyChannel (src/recipient-admission.ts:117) must use the same detached lanes set (:156), not the live policy.lanes; a test cell "mutating policy.lanes after compile does not widen the resolver" + a mutant (live read) with a named kill.
LOW-3: do NOT add a new error string; document automaticReplies.timeoutMs range as a named I6 precondition in docs/design/recipient-reply-compiler.md (with the citation v2/static-policy.ts:21,37-38).
LOW-4: a docs note for I6: the unboundProse demonstration is tautological; at I6 drive it through the real B consumer.
Gates (bounded as in v4.3 §4, existing root/closure, restage the same root with the before/after visible scans and the closed inventory; the root witness at 05:25Z is old — if there are unreadable/root pids, STOP and ask): types v0/v1/v2/root, all controls incl. the new return negative, focused, both mutant runners (with the new mutant), feature-off byte check against 5af23aaa…, self-test, docs-contract (expected FAILED, the declared FP), the full suite ONCE at the final head, diff-check, bindings. No retries; failures are kept. Disk ≥20 GiB before the suite. Progress at most every 4 min. Commit on top of e276024a, author/committer Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>, Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>. Report: the delta e276024a..HEAD, the gate table, what was not done.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-39920f3b-5e51-48cf-9356-8addbec8a5df, run run-01M686AMRJEZ0YCY8ZTSHYA17Q, in your own native session and the same worktree.
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