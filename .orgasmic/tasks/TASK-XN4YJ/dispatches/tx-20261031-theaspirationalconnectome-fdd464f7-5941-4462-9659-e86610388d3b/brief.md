# Implement fix round — Dispatch Brief
```brief
TASK-XN4YJ v3.1 — DRAFTS-ONLY correction round. Operator Tarn; scope from qualifier Soma (Slack 1793445107). Not an owner decision.

FORBIDDEN
- Source/test edits, commits, git writes (use GIT_OPTIONAL_LOCKS=0). Finalize prompts are not instructions.
- node/npm/bun/tsc, tests, builds, network, acquisition.
- Writing outside $ORGASMIC_EVIDENCE_DIR.
- No code in this round.
Progress goes ONLY to $ORGASMIC_EVIDENCE_DIR/progress.log (≤5 min). No `dispatch ask` except a genuine STOP.

FIRST progress.log line: HEAD (must be f378eadffc9a02e2ecb5a4f30e266db9321db4f5) plus hashes:
- /srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v3/SHA256SUMS = 98be81ddf9797e74b9788d3de5e4f6f3be14d247df71e47094f86f2e03d70d66 (9/9)
- /srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v3-REVIEW1.md = 057a7fafd0f330a60758d9924bc69599c1cd00c27ce083f056e36d6f829f40f0 (read IN FULL; it is the spec)
v3 is PRESERVED; never edit it.

WRITE to $ORGASMIC_EVIDENCE_DIR/drafts-v3.1/: v3.1 copies of ONLY the affected files, `V3-TO-V3.1.diff`, `CHANGES.md` (each finding mapped to its hunk), and SHA256SUMS.

1. H1 (N10 :16/:18/:86, DELTA :19/:50).
   - State the fact: host `^0.30.0` for 0.x admits only <0.31.0, and v0.30.0 has no src/mcpl/registration-view.ts and root-exports none of the required types.
   - Target-source availability ≠ current-host availability.
   - The §4 edit scope is BLOCKED until prerequisites close.
   - Correct the host-R deferral premise: the AF baseline is reachable only after A2 (now TASK-ZE2GZ, plan-only).
   - Keep declaration/artifact identity separate from source availability.
2. M1. Add to N11 §4 a full conjunct-correspondence table, one row per frozen check at T:test/stubs/f-host-t2/v1/dynamic-policy.ts:86–99, including:
   - account/readable/kind
   - subConversations
   - DM counterpart AND `input.item.author.id === f.dmUserId`
   - joined + channels.includes
   Each row names the N11 step that carries it and the source of channel identity. State explicitly that the predicate's `channelId` argument is NOT forwarded to the evaluator; channel identity is re-derived from the snapshot/item. Add a discriminator for DM author equality.
3. M2. EITHER add a closed harness/adapter scope row with EXACT paths (preserve v1 runners; new/versioned controls, e.g. test/stubs/f-host-t2/v2/run-mutants.mjs, as needed), OR make A3 an explicit prerequisite. Never require un-runnable mutants and then call the assignment ready.
4. L1. Keep the 32-node limit as a DOCUMENTED DIVERGENCE from AF current() (A:src/mcpl/registration-view.ts:77–82: unbounded with cycle protection). It is not equivalence.
5. L2: re-anchor the H4 cites to T (byte-identical). L3: retarget CHANGED-CONTRACT-COVERAGE-v2.1 :24/:35 to v2.1, or state the exception.
6. Gate split (DELTA and N10/N11). Do NOT collapse SC1–SC6 into "activation-only". Split each precisely into:
   (a) schema/trust/API SEMANTICS needed to write correct code — these must be settled BEFORE any implementation assignment;
   (b) real native VALUES and qualified runtime evidence — these may wait for enablement.
   Prerequisites to an H implementation assignment: an accepted A2 AND actual A1 + A3, plus the SC (a) parts. Implementation readiness must not be claimed anywhere.

Final report: hashes, the CHANGES map, anything UNVERIFIED. No readiness claim.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-40642da3-4ca8-4814-951d-30bf422c3803, run run-01M68SABS9R5S8W22G6WG469XE, in your own native session and the same worktree.
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