# Implement fix round — Dispatch Brief
```brief
F-host design fix round 3 (gen:exec; docs plus fixtures; local; no push; no history rewrite). Re-review 3: /srv/review-drop/TASK-E9C1M-REVIEW3.md (APPROVE, 3 LOW, plus an estimate for the thread gap). Read it in full.
1) LOW-1: `orgasmic-style` at host.md:403 → a neutral descriptor ("outcome-sidecar-style"); remove `orgasmic` from the checker's allowance and FORBID it, together with soma|tarn|alnold|alder.
2) LOW-2: in Mapping X, name `readJoinedChannels:true` and `dmUsers` as non-enumerable old read sources; add an A04 row (a channel joined after startup; a first-contact DM from a permitted dmUsers peer: old stores without wake, new must too, under the agreed scope mapping).
3) LOW-3: mention `ChannelDescriptor.address` alongside `.metadata` in the carriage table.
4) Re-pin B to `27a321276c8c03efe3fbc810029d25d53ec04572` (on A 869bb3ad) wherever B is pinned, and A to `869bb3add7b48893d48b2ce50ea423e4b33fdfbc` (published #906 head; tree identical to 1d990e41 plus a docs commit; check this). D stays a5b02a07 for now with a note "rebase onto 869bb3ad pending".
5) RECLASSIFY the thread gap (Soma's rule, accepted): exact thread identity is ALREADY an approved requirement. The missing plumbing (dynamic admission scope / registered sub-conversation + native thread delivery) inside the existing slices C/E/F and the 4 authorised repos (agent-framework, connectome-host, slack-mcpl, telegram-bot-mcpl forks) is ENGINEERING work, not an owner decision. Remove "authorize/fund … or retain the old pair" from owner decision 3. Instead, add a new F-host/F-Slack slice table ("dynamic scope + exact thread") with the hours from REVIEW3 (48–78 h, critical path 30–48 h, plus a conditional AF slice of 8–16 h and a tarball re-pin if the scope mechanism needs AF), and recompute the totals: the old ones without it and new ones with it, single-counted. Owner decision 3 stays ONLY for cases where the observable contract, permissions, recovery guarantees or repo scope change (state precisely when that would be). If nothing like that remains, remove the decision and renumber 1–7 once.
6) Make the dynamic-admission/native-thread boundary explicit in T01–T03: parent/read/access negatives (thread in an unreadable parent; parent not allowed; an author without access) and stale-route negatives (a thread deleted/moved; a binding from a previous epoch).
Commit on top of 90fb9ef6; same identity plus the Sol trailer. Gates: docs-contract (updated: forbidden names, A04, new slice table and sums, owner numbering) and source-pins. Report: head, disposition, the new hour table and the final owner list.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261030-theaspirationalconnectome-d1018f42-b51e-469c-a0ba-e484713d7856, run run-01M6797GCPGMBCQHR82W9DM5HG, in your own native session and the same worktree.
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