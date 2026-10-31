# Changed

Local-only commit `c353e1c8d19a11697b5515bf3b03110de5dd85de` (two files; required author/committer and coauthor trailer). No push.

- `docs/design/recipient-policy-staging.md`: precisely limits the guard to an own `recipientPolicy` on a server definition directly under the servers field (`mcplServers.<id>.recipientPolicy`), including tombstones and duplicate ids/roots; documents inactive misplaced keys. Documents all three diagnostic catch paths at `src/bug-report.ts:341,346,355–357`, including newly reachable hidden-policy refusal via the overlay scanner. No runtime changes.
- `test/fixtures/generic-recipient/check-scope-contract.mjs`: adds the staging doc and all seven current `test/stubs/f-host-3/**` files to the supplemental input list (17 total). Predicates, denial controls, frozen oracle and fixtures unchanged.
- Operator-approved doc-only `INT-1` → “Qualified composed release” retains every PENDING requirement. Original newly scanned content hits: staging doc :14 `agent-overlay` (internal identifier) and :116 `INT-1` (programme integration identifier); see `new-input-preedit-hits.txt`. Prose now accurately describes overlay files.
- LOW-3: no tracked quiescence helper exists. Old private helper/receipt were not edited or replaced. Old `Path-limited lsof: 1 holders output bytes 0` means **exit=1, stdout=0 bytes, no holders**, not one holder. Original helper SHA256 `bac711f3b1088c0597c1f71209e8af1f3552471eda9127fb67bf635b86c9c819`.

# Verification Gates

All commands ran once, through `orgasmic dispatch gate NAME -- bash "$ORGASMIC_EVIDENCE_DIR/bounded.sh" COMMAND...`: 16G scope, swap 0, `timeout --signal=TERM --kill-after=60s 3000s`, 64 MiB output cap, both PIPESTATUS retained. No timeout/cap/137/141 INCONCLUSIVE result, no retry. Gate logs below include exact commands/status and raw-log paths. Gates after commit are bound to `c353e1c8d19a11697b5515bf3b03110de5dd85de`; initial bindings gate was precommit/dirty and not treated as a reusable final receipt.

| Gate | Command after wrapper | Result |
| --- | --- | --- |
| low2-head-self-test | `bash cell.sh head /opt/node/bin/node <absolute-worktree-checker> --self-test` | PASS: 57 cases, 45 rejection controls, 4 oracle mutants, singleton and naming-parser controls |
| low2-head-docs-contract | same, `--docs-contract` | **FAILED — declared false-positive on a file name, not on content**; exit 1; `PIPESTATUS=1 0` |
| low2-head-focused | `bash cell.sh head bun test test/recipient-policy.test.ts test/recipe-unknown-keys.test.ts test/mcpl-agent-overlay.test.ts` | PASS: Bun 1.4.3, 93 pass / 0 fail, 584 assertions |
| low2-diff-check | `git diff --check c9f0991d20e9e2476f7ff57b09f1f2685671d97a HEAD` | PASS; no output |
| low2-bindings-before / low2-bindings-final | `python3 bindings.py` | PASS; empty runtime `src` diff, existing isolated root closed and installed closure unchanged |

Docs-contract exact failure excerpt:
```
PASS closed fixture schema and documentation oracle: 57 cases
AssertionError [ERR_ASSERTION]: internal identifier
    at neutral (.../check-scope-contract.mjs:233:3)
    at docsContract (.../check-scope-contract.mjs:361:9)
status=1 bytes=1274 PIPESTATUS=1 0
```
Matched line **checker:343**: `'../../stubs/f-host-3/run-mutants.mjs',`. Case-insensitive `TASK|RUN|AGENT|PROJECT-` predicate matches `run-mutants` in this real filename, introduced into the checker’s own scanned text by the expanded input list. The full exact output is preserved in the failed gate log. Operator answer `ask-fe77108f35b0462184f1c73ce0ede855` directs retention, no enumeration change and no rerun. Follow-up: **TASK-EC6WW** (narrowing that regex); no URL was supplied, so no tracker URL is invented.

Separately, my supplemental content-only observation (`observe-new-content.mjs`, `new-content-observation.txt`) applies the exact unchanged neutral predicates to the eight newly included files: all clean after the edits, with an empty private forbidden-name list. This is my observation, **not a docs-contract pass** or full naming qualification. The original content hits remain recorded above. Naming coverage remains PARTIAL without the full private policy; the failed gate did not reach its final PARTIAL/pass banner.

Logs (all under this report’s evidence directory):
- `gate-low2-bindings-before-01M684KR9AZFN18WAEXEFZ2PHT.log`
- `gate-low2-bindings-final-01M684MWZXY6S4N3RVJ334MSVH.log`
- `gate-low2-diff-check-01M684MWTQ1MTEM9V4ZYN1VCGA.log`
- `gate-low2-head-docs-contract-01M684MQ8J9KEHHB1B0RQ8NNYB.log`
- `gate-low2-head-focused-01M684MWE27T48363AA0BAFD4H.log`
- `gate-low2-head-self-test-01M684MQ3H9XHWWBNNZ0AKCW0R.log`

## Input binding / inherited evidence

No install or root staging. The new checker runs by absolute worktree path from the scrubbed existing head cell; its relative doc/stub inputs resolve to this commit’s worktree, not the stale isolated checker. Focused tests consume unchanged isolated source. Both binding checks verify the existing head’s closed 437-file src/test inventory against `c9f0991d` (no extras/missing/hash drift); the only test-tree change this round is the independently executed checker input list. Installed head root+web tree: 16820 entries, manifest SHA256 `6b42678559f175f926db4a39c1527f2415c37bab1392be6a74ae95446233ed64`, byte-identical to the prior round before/after this round. No build outputs consumed; Bun executes unchanged source. `src/bug-report.ts` and all runtime `src` files have **zero diff** from the round base.

Inherited reference only: full review SHA256 `27d354e49fb53e4ce4f9af7a0defd006f8447bf07db711953ea2ef4a95b432e7`; prior evidence `/home/orgasmic/.orgasmic/runs/run-01M682G1X93YCCNXW0A8725SAN/evidence`. Prior matched feature-off result SHA256 `5af23aaa62c8996f61dc32caf50443b233f92b6e0675a68f98cbb88d97936e82` and runtime gates are historical observations only, not fresh passes. Profile-off/feature-off unaffected here by fresh structural proof of empty runtime diff. Original base `d7154d79`, prior old-head `9c6f6ea1`, and fix-head `c9f0991d` observations are not conflated with this docs-only head. No base/full-suite/typecheck/mutant rerun assigned this round.

`process-reconciliation.json`: 12 known owner pids, zero matching live owners, six checked gate scopes, zero nonempty scopes and zero build-cwd processes. Pid/start/cgroup reconciliation complete; no unrelated processes killed.

# Unmet Criteria

All four LOW changes addressed as narrowed by operator direction, including preserving the docs-contract failed receipt. **Docs-contract is not green**: filename false positive deferred to TASK-EC6WW; full private naming input absent. No claim of H.3 runtime completion.

# Residual Risk

- Remaining literal programme label outside the scan: `src/recipient-policy.ts:8` contains `INT-1` in the pending-reason string (`programme-label-outside-scan.txt`). Recorded only, unchanged per operator; scan is not a runtime/whole-tree/history privacy check.
- Runtime-binding remains PENDING on canonical selector schema, qualified composed release and reviewed scope integration. Protected overlay guards remain unit-only and require runtime-binding proof. A guards/native scope/B/D/storage remain unqualified here.
- Bug-report diagnostic paths still flatten named refusals to generic notes; no bypass or policy stripping introduced. Prior quiescence was point-in-time evidence, not a lock; no root writes this round.
- Private ancestry retained. LOCAL ONLY; no public-history/privacy approval or push permission.
