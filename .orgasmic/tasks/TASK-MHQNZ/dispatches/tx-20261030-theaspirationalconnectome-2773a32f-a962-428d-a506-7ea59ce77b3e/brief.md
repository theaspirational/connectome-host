# Implement fix round — Dispatch Brief
```brief
Phase P revision 4: a SMALL bounded prep slice, on e3f07c16a3983431b096d747477fda8d20bf3c38. DO NOT run Phase E, create/open stores, or import AF/CM/Chronicle/SDKs. Read the delta review IN FULL: /srv/review-drop/TASK-MHQNZ-R3-DELTA-REVIEW.md (sha 7d713403…). ONLY the following; no other harness growth:
M-2: check-prep.py takes a MANDATORY externally supplied approved_sha256 argument (not recomputed from the file under test); asserts it against the computed manifest hash and against EVERY receipt's manifestSha256; the normal and negative receipts are each bound to their explicitly expected manifest. The dispatch gate passes the same value as the launcher.
M-3: the strace smoke in the EXACT production FD/pipe shape (the same `3>&1 1>&5 | /bin/cat` script, stdio[4] API and stdio[5] pipes as in probe.mjs runChild; ideally the same code). Disjoint asserts: the trace (fd1 of bash) contains execve of /bin/true; stdout (fd5) carries a known marker from a tiny no-store control (e.g. /bin/echo with a marker, or a stdlib marker script without SDK imports), and that marker does NOT appear in the trace stream as tracee output; the API channel (fd4) is separate (a marker event); not just "non-empty". check-prep asserts transportArgv equals the string from probe.mjs.
M-1: a cheap cross-check of container-inspect.json Config.Cmd/Image (and the relevant HostConfig) against the recorded argv in stream-check; an honest README/report line: the launcher is trusted, the recorded argv is its self-attestation, not protection against a modified host/launcher. No attestation/auth service.
M-4: explicit conservative semantics in README/report: filter activation (≥192 MiB) with any drop = terminal INCOMPLETE for the run; the budget must not be approached; no new reserve architecture.
LOW-2: fix the retain regex so it catches newfstatat (and similar *at variants); one selftest case.
Other LOWs (1,3,4,5): only a "known limits" list in the report, no code.
Limits/privileges/envelope/data scope unchanged. Commit on top of e3f07c16, same identity plus the Sol trailer. Gates: check-prep (with approved_sha256), noop with the new smoke, kill, deadline-negative. Report: new harness/manifest sha, image (if changed), argv, receipts, a delta list per item.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-a2329c6b-eb50-457b-ad8d-3ee3b3ab8a02, run run-01M67DABP7KS1CNCSB0SPAW9BW, in your own native session and the same worktree.
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