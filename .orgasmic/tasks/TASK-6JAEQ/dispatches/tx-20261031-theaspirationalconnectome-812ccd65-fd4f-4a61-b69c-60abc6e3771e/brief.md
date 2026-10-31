# Implement fix round — Dispatch Brief
```brief
F-host.3 fix round 1 (TASK-6JAEQ), from 9c6f6ea1de60f2ac749f9f3184adcc71f128253f. LOCAL ONLY, no push. Task operator: Tarn; constraints: Soma (programme qualifier); not decisions by the owner Vasyl. Review: /srv/review-drop/TASK-6JAEQ-REVIEW1.md (sha256 25bea49aeecea0f21b2ed504936e9d5818dbe0c9699ba306f67826c780e44740) — in full, including the reviewer-*.ts/mjs probes in its evidence.
MEDIUM (src/recipient-admission.ts:112, inference/request ignores ceilings): FIRST check whether the H brief (docs/design/f-host-h-brief.md), generic-recipient-host.md or the T1 contract defines a separate rule for inference/request. (i) If NOT — apply the more restrictive one: the lane goes through `wakeWithoutChannel && addressed` (store/mayWake symmetric with channel-less push/event); (ii) if it IS defined — implement exactly that rule, state it explicitly in the interface doc comment CompiledRecipientAdmissionPolicy (:16-24) with a source citation. In either case — a mutant that widens the lane (back to unconditional true) must be killed; the test :211 updated. If the documents contradict each other or the choice changes the observable contract outside this dead code — STOP and ask.
LOW-1: in readOverlayEntry (mcpl-config.ts:344-345) the recipientPolicy check BEFORE the `disabled===true` return — a tombstone with a policy refuses loudly; a test.
LOW-2: readAgentOverlayFile (:265) via parseRecipientConfigJson — a hidden policy behind a duplicate map/id refuses; tests; policy-free overlays unchanged.
LOW-3: a compiler-control for test/stubs/f-host-3/v1 analogous to v0 (removing each @ts-expect-error → pinned TS2353/TS2339/TS2741).
LOW-4: add loadRecipeDetailed/loadSavedRecipe (plain, BOM, invalid, unknown-keys) to the in-tree feature-off probe; base/head byte-identical.
Record without changes: the protected-overlay guards are only via unit (they must be re-proven at runtime-binding), bug-report.ts:346 is blocked by a configured policy — in the docs Unmet/Residual.
GATES: on the EXISTING isolated roots tmp-recipient/build/{base,head} (no npm ci/new install — the disk is nearly full; if a root needs updating, only copy the changed tracked src files and verify by hash). Matched base (reused with an identical bindings table if allowed) / fresh head: tsc, v0/v1 types, compiler controls, the suite via test-on-tmpfs, focused, mutants (old 21 + new), feature-off. Bounded: 16G scope, `timeout --signal=TERM --kill-after=60s 3000s`, 64 MiB cap, PIPESTATUS, INCONCLUSIVE classes, no retries; progress ≤4 min; reconcile helpers. Commits: Vasyl noreply author/committer, trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-e2f6e868-eb76-43ca-81b1-8c1e3a1709db, run run-01M680D8DBYQXRDKQ7C4C9C6Q8, in your own native session and the same worktree.
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