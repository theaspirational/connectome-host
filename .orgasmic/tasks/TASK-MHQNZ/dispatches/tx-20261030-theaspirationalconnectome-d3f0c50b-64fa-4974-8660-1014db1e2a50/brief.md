# Implement fix round — Dispatch Brief
```brief
Phase P revision 2 (prep only; Phase E is NOT run; no store is created). Problem: the real old reader is not Node/dist. In the frozen oracle, host/package.json has `start: bun src/index.ts`, artifact-manifest bunVersion 1.4.2 (bin/bun sha a83d2637…), and @animalabs/agent-framework exports `"bun": "./src/index.ts"` vs `"import": "./dist/src/index.js"`. Under Bun the old pair imports SOURCE, not dist. Node rows must not be reported as the result of the actual old Bun reader.
Do:
1) Add Bun routes alongside Node: old = oracle bin/bun 1.4.2 (mount :ro, verify sha against artifact-manifest) with resolution under the `bun` condition (src); new = Bun 1.4.4 from /srv/ci-fixtures/bun-1.4.4-linux-x64/bun (verify sha 10702245…c235 against the PROVENANCE file next to it; if it doesn't match, STOP). Matrix per runtime: old→old, new→new, old→new, with Node and Bun results kept separate. Log the exact executable + resolved entry file + sha for each control and attempt (the resolved/transitive-resolved events already exist; add the runtime and condition).
2) If Bun needs anything under the envelope (a writable home/cache → only inside /work; BUN_INSTALL_CACHE_DIR etc. = /work/…, no network, no install), declare it; don't relax seccomp/ptrace/privileges. If strace under Bun doesn't work, that's an inconclusive control, not a relaxation.
3) Update the operator input manifest (plus bun binaries), harness.SHA256SUMS, image (if the Containerfile changes), the argv and the deadline (recompute for the doubled matrix; justify). Run the no-op limits proof again with the new argv.
4) Keep: the inherited-environment disclosure for the first build and the clean-build provenance; no claims about exposure/rotation. (b) D stays pinned-null.
Commit on top of 75d1d4d5, same identity plus the Sol trailer, no host paths in git. Report: new hashes, the full argv, a runtime×route→executable/entry table, limits proof.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-0f798a1f-6502-4b31-910c-b471be55b856, run run-01M67ARH8PK8QF691QCJA440VW, in your own native session and the same worktree.
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