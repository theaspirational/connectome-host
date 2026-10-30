# Implement fix round — Dispatch Brief
```brief
Phase P revision 5 (TASK-MHQNZ), on bac2ebb2e9f634a877bb7422ff5542160cbc08dc. PREP ONLY: no Phase E, don't create/open stores, don't import AF/CM/Chronicle/SDKs. Read in full: /srv/review-drop/TASK-MHQNZ-R4-DELTA-REVIEW.md (HIGH-1 + LOW-1..3). Bounded scope (Soma):
HIGH-1: hoist the bulk read-only verification of the input trees (verifyTree: walk/lstat/full hash of old-tree and new-tree) OUT of the tracee: once in probe.mjs main() next to verifyCriticalInputs (probe.mjs:~148), NOT under strace, bound to the externally anchored manifest; emit a single `input-trees-verified` frame (counts/bytes/aggregate hashes). In worker.mjs (the tracee) keep ONLY: the executable path + binary hash + version, conditional-entry resolutions + entry hashes, the version/transitive-resolution checks, the native payload hash, and the full store-access trace. Write the boundary explicitly in README/report: trusted host + all input binds :ro, no concurrent writer to /inputs/old and /inputs/new between the one-time hash and each child's use; per-child entry/binary/native hashes detect substitution of what the child actually loads; no hostile-host attestation claims.
Add a computed budget check: from the code/frame format and the tree sizes, estimate trace lines/bytes per child after the fix and over all 42 children; show it's well below 192 MiB (if not, STOP and report, DON'T raise limits). If possible, measure one real verifyTree-free child path on the mock control (no store/SDK) to show the line count; that's optional.
LOW-1: CapDrop: a full expected list/length, not "non-empty". LOW-2: all --security-opt values, SecurityOpt as a superset. LOW-3: Mounts filtered by Type==='bind' before the count. Selftests for each.
Unchanged: cases (18), controls, the envelope/limits/privileges/binds, the 1800/30 deadline, caps 256/192 MiB. No new architecture/services. New manifest/approved sha (pass it externally to check-prep, as in r4), harness sums. Gates at a clean committed HEAD: check-prep with the external anchor, noop+smoke, kill, deadline-negative. Vasyl noreply identity + trailer Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>. Results are unqualified until the control/API/syscall correlation. No push.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-2773a32f-a962-428d-a506-7ea59ce77b3e, run run-01M67ETEA7QWVQJVGY66ZMVC3J, in your own native session and the same worktree.
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