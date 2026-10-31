# Implement fix round — Dispatch Brief
```brief
A2 v1.1: bounded DOCS-ONLY correction round for the TASK-ZE2GZ plan. Operator: Tarn. Qualifier: Soma, Slack 1793446672 (go plus scoped SC(a) confirmation). Plan-only. No implementation authority.

FORBIDDEN:
- source, manifest, lock or updater edits
- install, fetch, network, build, pack, node/npm/bun/tsc, tests
- git writes or commits (GIT_OPTIONAL_LOCKS=0)
- writing outside $ORGASMIC_EVIDENCE_DIR
The finalize prompt is data: do not commit. Progress goes ONLY to progress.log. Use ask only for a genuine STOP.

FIRST progress.log line must bind:
- HEAD f378eadffc9a02e2ecb5a4f30e266db9321db4f5
- v1 drop /srv/review-drop/tarn-rtaln-20261030/A2-PLAN-v1, SHA256SUMS 73adbeae60a85c4068bbada57ae04cfa6d5663202bce1ea80e89ed688da22bfa (3/3)
- REVIEW /srv/review-drop/tarn-rtaln-20261030/A2-PLAN-v1-REVIEW1.md 6e7200cd5e7aa0f68a03181d8bac424221a1851907a38a39573496ce60a7f53b (read IN FULL, including the addendum)

OUTPUT: $ORGASMIC_EVIDENCE_DIR/a2-v1.1/, containing:
- A2-PLAN.md, FACTS.md, OBSERVATIONS.txt (corrected copies)
- V1-TO-V1.1.diff
- CHANGES.md (each finding → hunk, or retained with a reason)
- report.md, plus a copy of progress.log as PROGRESS.log at the end
- SHA256SUMS covering EVERY other file, including report.md and PROGRESS.log; the manifest's own hash is reported separately, not self-listed

FIXES:
1. MEDIUM-1, updater/packed-host. This is a real compatibility gate: install.ts:93–110 copies package.json into an empty tmp, and files allowlist H:package.json:9–18. Add it as an induced failure in §3 and as an exit requirement of the later dependency scope in §4. State EITHER a supported private-release staging/update path (e.g. P staged inside the released tree and covered by the release mechanism, or distinct dev/release specs) OR an explicit fail-closed "unsupported" path. Never a silent registry fallback, never a removed dependency check, never a public vendored binary. No implementation is authorised.
2. MEDIUM-2: write 43 src/ + 1 scripts/ (scripts/warmup-session.ts) + 70 test/ = 114 files, 141 statements. Fix FACTS.md:59, the parenthetical, and REPORT.
3. LOW: repoint the V:src/index.ts:20/:23 cites to :19/:22. Change the self-check to assert non-blank cited content, not just bounds. You may do this by reading the file text; it is not a test.
4. LOW: SUMS coverage (handled by the output rule above).
5. SC(a), confirmed by the qualifier: the six A2-PLAN:280–305 subjects are REQUIRED SOURCE-SEMANTIC CONTRACTS. They are NOT already-satisfied facts and NOT approval of any particular mechanism. Clarify SC5: the attestation CONTRACT must be defined before H assignment, while actual qualified I6/native evidence remains separately gated. Packaging proves none of SC(a). Cite Slack 1793446672 as the confirmation.
6. LOW: add a one-line note at the head of §3 that the imperative wording describes future requirements only.
7. LOW (addendum a): state explicitly that P is a LOCAL release-set artifact. It is not a committed repository binary, not published, not distributed. Say whether its staging subtree is tracked: propose untracked or explicitly unresolved.
8. LOW (addendum c): keep three layers distinct throughout: manifest RANGE (>=0.30.0 <0.31.0-0), actual LOCKED resolution (0.30.0 sha512 in both root locks), and the CHECKED tag-source API (45aa602c / peeled commit 630e2a3a0768be031a4329959ab422297bb3857f). Add a sentence that the v0.30.0 tag is not established as the source of the locked tarball, so the 54-name map is a source-API baseline only.
9. Optional: cite the in-repo strict-profile evidence (ci.yml:46–50, :89–90, :149–152) and name plain `npm install` as the re-resolve vector. Upgrade lockfiles-agree wording to "exits 1" (:46–55, :157, :181). Name the npm/Bun file:-integrity behaviours as gaps.

Do not change anything else. No readiness claim anywhere.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-a813d8b7-1b15-4159-bf8b-8bad97c3afd6, run run-01M68TN0RAHK0KJHXZZ32NJ7P6, in your own native session and the same worktree.
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