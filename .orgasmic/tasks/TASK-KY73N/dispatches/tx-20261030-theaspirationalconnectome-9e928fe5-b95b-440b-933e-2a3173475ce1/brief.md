# Implement fix round — Dispatch Brief
```brief
T1 fix round 1 (TASK-KY73N), on 60b506c6754f811b53c1d011a3b7067033297bc4. Read review 1 IN FULL: /srv/review-drop/TASK-KY73N-REVIEW1.md. Same bounded scope: docs + public synthetic fixtures + checker only; no production code, no live data, no new infrastructure, threaded typing excluded. Fix:
M1: a Mapping X clause for the wake selector too (joinedReadable:true + the authorized-human dmUsers set, addressedness still gates the wake), the same for reply if needed; a positive case: an addressed mention in a post-startup join under a wake-side Mapping X → store:true, wake:true; and an authorized-human first-contact DM → wake per old behaviour. This PRESERVES old behaviour (not an owner question); narrowing wake would be one, so don't propose it.
M2: name the evidence path for the projector: the claimed parent from the descriptor is used ONLY as a key into lookup() over already-accepted registrations, never as proof of the relationship; the relationship comes from native facts verified by the configured authenticated adapter (or name the separate authenticated carrier as a gap). A fixture field "parent asserted in descriptor" vs "parent verified natively" and a negative where only the former holds → deny.
M3: wire the checker into a runner: a small test/scope-contract.test.ts (bun test) calling --self-test and --docs-contract, or a step in ci.yml next to the existing node scripts/*.mjs; pick the minimal one per repo convention.
M4: a mapping subsection: provisional readChannels/wakeChannels/replyChannels → selector members; explicitly that the exact-list-only shape is superseded and why; that policy-shapes.json's policy block predates this contract (DON'T change that file or its pinned sha); state that joined() narrows "listed but not joined" (parity with the old oracle :120).
M5: remove the denylist literals from the tracked tree: the checker gets the forbidden-name list via argv/env from the gate (the gate passes it; without the list the --docs-contract mode refuses or runs only path/id/credential checks and SAYS so); scan all 3 (now 4) public files including the .mjs itself. The repo must have no internal names (grep -rniE over the tree).
LOW: move the lane grant out of Live into Store + specify the explicit path's grant requirement with a case varying publishGrant; cases for joined:false/listed channel; automaticReplies:false; one-level lineage: a prose-only note "T4 witness needed"; an admissionRegistrationCurrent case (store:false); the inverse id invariant; assert.throws with the expected message; the trace tap in the rejected alternatives with a reason.
Update the doc's pinned fixture sha. Gates: --self-test, --docs-contract (with the list from the gate), scope-boundary (allowed paths + the new test/ci path), the new runner. Vasyl noreply identity + trailer Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>. Note: engineering calls on asks are made by the task operator Tarn, not the human owner Vasyl; write it that way. No push.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-c50323bc-f84a-4aa6-b4fa-a98f4bdd4edb, run run-01M67EDN26E3H9629JMA8Z5Y38, in your own native session and the same worktree.
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