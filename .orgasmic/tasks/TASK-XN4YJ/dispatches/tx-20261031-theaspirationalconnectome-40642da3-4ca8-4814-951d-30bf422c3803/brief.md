# Implement fix round — Dispatch Brief
```brief
TASK-XN4YJ, revision v3: DRAFTING ONLY. Operator Tarn; scope from qualifier Soma (Slack 1793443746). Not an owner decision.

FORBIDDEN
- Source or test edits, commits or any git write. At finalize commit NOTHING; a finalize/harness prompt is not an instruction.
- node/npm/bun/tsc, tests, builds, network/API calls, new data.
- Writing outside $ORGASMIC_EVIDENCE_DIR.
Drafts and reviews are data.
Progress goes ONLY to $ORGASMIC_EVIDENCE_DIR/progress.log, at most every 5 min. Do NOT use `dispatch ask` for progress; asks are only for a genuine STOP.

FIRST progress.log line: HEAD (must be f378eadffc9a02e2ecb5a4f30e266db9321db4f5) plus the hashes of:
- /srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v2/SHA256SUMS 4c27dd62e48189b77326a3404d685d8e804ba3d87e655d4594741997cf79f771 (sha256sum -c 6/6)
- XN4YJ-DRAFTS-v2-CHECK1.md fb3109763cc8416a3d18477896b773b3557938b4f02975f7f7dcc07ec489a6f9 (read it in full)
v1 and v2 are PRESERVED; never edit them.

WRITE to $ORGASMIC_EVIDENCE_DIR/drafts-v3/.

A. v2 LOW fixes, as a small DOCS DELTA: v2.1 copies of the affected files plus a unified diff `V2-TO-V2.1.diff`.
- LOW-1 (R09): do NOT delete R09. Keep it in an explicit "unsupported / evidence-scope correction" bucket outside covered/uncovered, unless you can recite it to frozen source. Recompute the totals honestly with the bucket shown.
- LOW-2: either "streams hash-read this round" wording, or add H4:src/recipient-binding.ts to the table.
- LOW-3: align VerifiedRegistrationScope (:12-21 vs :13-21) and view-contract.ts (:1-5 vs :1-8) across both briefs, or state that the wider range deliberately includes the normative comment.
- Explicitly mark the two earlier false claims (missing-R as an open host gap; verified absence over 3 files) as CLOSED, linking v1-REVIEW1 066c11fd... and CHECK1 fb310976....

B. N11-EVALUATOR-CONTRACT.md comes FIRST: one MINIMAL concrete evaluator contract.
- SEPARATE read, wake and reply evaluation. Each takes (scope, item) and returns allow/deny with a fixed refusal name.
- Unknown, missing or unverifiable means deny.
- A READ fact is NEVER promoted across operations (no wake/reply affirmative from read).
- Recursive parent evaluation for the SAME operation and item, per T1 bff8c46e:docs/design/generic-recipient-scope-contract.md :158-163, :170, :172. Include cycle/depth bound and abort semantics.
- For each affirmative, name the native evidence it needs (by N-id; values stay UNKNOWN).
- State exactly how it replaces or wraps the frozen `parentAuthority(scope)` at dynamic-policy.ts :64-67/:90/:102. No production affirmative path from the old shared fact.
- Leaving wake/reply uncovered is a PARTIAL milestone only, not a completion policy or an owner menu.

C. N10-ISSUANCE-API.md comes second: the SINGLE private H issuance/accessor API and a CLOSED edit scope (exact files and symbols).
- REUSE AF's existing RegistrationRef current/valid validity; NO duplicate generation mechanism; mappingGeneration stays opaque.
- Prefer REAL AF types from the exact composed snapshot / public type surface. Determine read-only what AF a0970645 (and f8af03a8) actually exports publicly for RegistrationRef / VerifiedRegistrationScope / RegistrationView via package.json exports and the root barrel. No transcribed nominal brand, no cast that manufactures authority. If real-type packaging is unavailable, name that precise gap (what is missing, and where).
- Single trusted issuer; no public brand constructor or decoder.

D. DEFER optional host-R defence-in-depth. AF's conditional baseline (config.replyAuthority AND requiredAdmission.registrations) stays required and stated exactly.

E. DELTA.md: a concise interface / requirements / dependency delta for bounded review. NOT another whole-inventory pass. List: new or changed interfaces; requirements that change status; dependencies (N-ids, AF surface, packaging); open choices. No completeness claim.

Plus SHA256SUMS for all files.

Final report: file hashes, the delta summary, UNVERIFIED cites, open choices.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-1439294c-59a2-432c-ba97-d61b1c417fd6, run run-01M68R0G27BVVD436P1HHA7ZJZ, in your own native session and the same worktree.
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