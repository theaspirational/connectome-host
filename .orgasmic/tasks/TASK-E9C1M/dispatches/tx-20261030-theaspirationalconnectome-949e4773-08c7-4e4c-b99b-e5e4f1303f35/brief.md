# Implement fix round — Dispatch Brief
```brief
F-host design fix round 4 (small; docs plus fixture; local; no push/rewrite). Re-review 4: /srv/review-drop/TASK-E9C1M-REVIEW4.md (APPROVE, 3 LOW + 1 observation). Read it in full.
1) LOW-1: policy-shapes.json: add an `authorized-human-dm` case (senderKind human, dmAuthorizedPeer matching, addressed:true, expected {store:true, mayWake:true}); the checker asserts it alongside the two A04 cases. New fixture sha in the docs.
2) LOW-2: host.md:93 (`readChannels` comment) and :209: mark the exact-list read surface as PROVISIONAL pending the T1 scope contract (§6), with a reference to :393-396.
3) LOW-3: fixtures.md:136 T02: scope the author-access negative to binding/wake ("no new binding, no wake"); state explicitly that ingress storage must not be narrowed below the Mapping X read ceiling. The DM lane's author check (host-recipient-protocol.ts:115-116) is the only case where the old refusal already exists.
4) Observation: add "status/conversation behaviour (incl. enabling typing)" to the escalation examples at host.md:444.
One commit on top of 4326620b; same identity plus the Sol trailer. Gates: docs-contract (updated) and source-pins. Short report: head, new fixture sha, gates.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-34daab84-e1c7-4a3c-8432-d0f038130546, run run-01M67AGJSD09ZWPPQ4GNXJ0F7E, in your own native session and the same worktree.
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