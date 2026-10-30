# Implement fix round — Dispatch Brief
```brief
Phase P revision 3: a focused fix slice (prep only). DO NOT run Phase E, create/open any store, or import AF/CM/Chronicle. Base 1bb624822c16ea58971ee1fed031b55b67a81b97. Read the static review IN FULL: /srv/review-drop/TASK-MHQNZ-R2-STATIC-REVIEW.md (sha be6d96a9…). Scope is ONLY the following; no other changes:
1) MEDIUM deadline binding: host-receipt.mjs must take deadline/kill-after from the ACTUALLY launched argv (parse the recorded argv file that launch.sh passed to timeout, plus argvSha256), and the checker compares them to manifest.capture.deadlineSeconds/killAfterSeconds and to the approved manifest/execution intent, not to literals. runtime.mjs is the single source of the values. NEGATIVE CONTROL (gate): a launcher with a changed deadline (e.g. 600s/10s) on the mock stream → the checker REFUSES (not COMPLETE). Matching constants are not evidence.
2) Trace filter: count retained/dropped lines and bytes; emit trace-filter-stats per attempt and periodically; include them in results. If relevant access can't be correlated because of filtering, the case stays uncertain/inconclusive.
3) Before use, verify old-tree.json/new-tree.json (against the manifest's tree-assertion entries), /harness/* and /tools/strace (plus trace-libs) against the trusted manifest; on a mismatch, fail closed (inconclusive, no imports).
4) strace smoke in prep: `strace -o /dev/fd/3 /bin/true` (plus /dev/fd existence) inside the same no-op envelope; the result goes into the stream. In Phase E, a ptrace/ABI/loader failure stops the native run as INCOMPLETE/inconclusive. NO privilege, capability, seccomp or ptrace relaxation.
5) The --phase-e-approved flag: explicitly document it as NOT an authority boundary. A private exclusive execution intent (an evidence-only file: manifest sha, image id/digest, argv sha, a nonce; the launcher refuses a repeated nonce) may be added to prevent accidental replay, but it does NOT authenticate owner consent; owner authority and Phase E's operational go remain external. No signing scheme or approval service.
Do NOT grow the probe with other LOWs. Commit on top of 1bb62482, same identity plus the Sol trailer, no host paths in git. Gates: check-prep, the no-op (with the strace smoke), kill (137/INCOMPLETE), deadline-negative (refuse). Report: new harness sha, input-manifest sha, image (if changed), full argv, all receipts, a delta list per item.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-d3f0c50b-64fa-4974-8660-1014db1e2a50, run run-01M67BPB295V5D0XX7A2BKCTVQ, in your own native session and the same worktree.
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