# Implement fix round — Dispatch Brief
```brief
A821A fix round after Review 1 (APPROVE + 3 MEDIUM + LOW), from e5184b31a1dc077ac1ef0cfec6841866a4ec4f9a. Task operator: Tarn; constraints: Soma (programme qualifier); not decisions by the owner Vasyl. Read in full: /srv/review-drop/TASK-A821A-REVIEW1.md (sha256 d828893296ca5c45182c95124160f30d91aabc0a80de81552ccd003603d851a1).
DO:
F2: remove the harness vocabulary from tracked files: bounded-gate.sh:4,9 takes the log directory as $1 or BOUNDED_LOG_DIR with a neutral fallback (mktemp -d); the command block in the brief (:218-221) = the bare `bash test/stubs/f-host-2a/bounded-gate.sh ...`; replace `INT-1` (brief :74,75,243) with "the composed A+B+D integration release". Add to check-scope-contract.mjs (:230-236) STRUCTURAL rules for tooling/programme identifiers (at least: the harness CLI name, an ORGASMIC_-prefixed env, INT-<n>), with negative controls; PARTIAL semantics without SCOPE_FORBIDDEN_NAMES unchanged. Confirm with git grep that there are zero hits in tracked files.
F3: the .2a file reads in the checker are lazy, inside docsContract(); if a file is missing → a clear assert/notice, not a stack trace; --self-test and the default mode don't depend on the stub files (a control: a temp mirror without the file → --self-test exit 0).
F4: typecheck.ts:29-30: narrow to the object branch first so that the directive witnesses TS2542 (readonly index).
F1 (NO acquisition): record stub-types as PENDING/EXPLORATORY in the brief and the report (TS 5.9.3 borrowed; the host declares ^7.0.2); brief :202-204 requires the compiler major = the package.json declaration and recording version + declaration; carry the reviewer's 6-item TS7 risk inventory into the brief. DON'T install TypeScript, DON'T point at other worktrees, DON'T symlink, DON'T change the stubs to fit 5.9.3. A typecheck of the stubs is NOT a gate in this round (PENDING).
Also: one explicit line above the 57-row table: these are planned scope-behaviour cells, not 2a's own exit gate (2a = harness + startup/transport controls, host.md:683).
GATES (bounded, as before): --self-test, --docs-contract (PARTIAL), the F3 control, git diff --check, input-probes (hashes unchanged). PUBLICATION: LOCAL ONLY (descends from the private line); no push.
Commits per concern, author/committer `Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>`, trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
```

## Round context
Resume round contract: 2. Continues dispatch tx-20261031-theaspirationalconnectome-30b364ba-0482-43b6-b7fd-7919500c3838, run run-01M67V7D9Z9PVK724JFM32R2DK, in your own native session and the same worktree.
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