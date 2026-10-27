# Review: TASK-VNR9Y

## Verdict

**APPROVE the implementation. No actionable findings.** Reviewed `cc0d1f7e47c438ff9cdb7542a43492ef68e42643` against `3e4cf9b39df97f00f9e62e09571201966142c0f3`. This is not unrestricted full-suite qualification; the residual failures below remain visible for that stage.

## Findings

None. No correctness, regression, security, or scope-drift defect identified in the seven-file diff.

## Open Questions

No implementation decision requires clarification. The fleet timeout reproduced intermittently on both the verified base and final trees in the reviewer follow-up; it is a pre-existing harness-sensitive flake, not evidence of a regression in this change. Its exact timing trigger remains undiagnosed.

## Verification Notes

### Source and behavior

Read the live task record, project constraints, implementer dispatch brief including REVIEWED DISPATCH CLARIFICATION, and complete implementer report (`last.txt`). Gotchas and convention collections were empty. Repository conventions checked: `CONTRIBUTING.md`, `changelog.d/README.md`, `bunfig.toml`, and `tsconfig.json`. Ordinary tier: P1 priority trigger; no coupling or breadth trigger (seven files, 232 net lines).

- **Silent/default behavior:** `src/framework-error-policy.ts:38-40` returns an empty object for omission and `{}`; `src/index.ts:657` spreads it, adding no `errorPolicy` property. The production-entry capture asserts property absence, not merely an undefined value. `{overloaded:{}}` intentionally counts as non-empty.
- **Completion parity:** compared `src/framework-error-policy.ts:13-34` field-by-field with membrane 0.7.2's private `resolveRetryConfig` and `resolveRetryFields` at `node_modules/@animalabs/membrane/src/membrane.ts:430-457`. Base and nested fields, undefined handling, finite/nonnegative checks, exception types and messages match. Package exports do not expose that resolver. The helper correctly uses Membrane defaults, not the host's overloaded override.
- **Independent oracle/test sensitivity:** `test/framework-error-policy.test.ts:39-41,63-78` derives expected settings from a real Membrane constructor, independently of the helper. Applying `DefaultErrorPolicy` to both sides correctly accounts for its stated-wait-ceiling adjustment. My in-memory sensitivity probe reused those seven parity inputs and the same comparison: ignoring any one of the eight base/nested fields caused a mismatch. No source mutation was written.
- **Non-turn behavior and validation:** `src/membrane-retry.ts` and the Membrane-side configuration at `src/index.ts:1352` are unchanged, including overloaded maxRetries 0. Production capture controls compare both its raw config and resolved settings. Retry validation is unchanged at `src/recipe.ts:2282-2314`; explicit recipe loading validates at `:1601`, before policy creation. Saved recipes also validate before framework creation.
- **Scope:** the pinned framework selects one policy at `node_modules/@animalabs/agent-framework/src/framework.ts:3739` and consults the shared member at `:19666,21361`. Forks are created within that framework (`:13702`); host subagents use its ephemeral APIs (`src/modules/subagent-module.ts:3395,3460,3620,3782`). FleetModule launches a separate host with the child's recipe (`src/modules/fleet-module.ts:1959-1960`), so that process gets its own policy. README's conditional same-instance language is accurate.
- **Counts/docs:** checked `membraneRetrySettings`, `retryDecision`, defaults, and `DefaultErrorPolicy.onInferenceError` in the pinned dependencies. maxRetries 4 means four ordinary sends/three waits; default 3 means three sends; rate limits retain at least five sends; positive overloaded bounds use the larger base/overloaded count; overloaded 0 uses the unjittered base schedule. Zero still permits the initial send. The report's delay table matches, including lower-bound equal jitter and the framework's intentional deadline handling. README, recipe field documentation, and the behavior-change changelog fragment conform to the requested scope and fragment convention.

### Gates and evidence

Reviewed evidence: `run-01M5XHSBH5QT8D8KRDXSNNW2GN/evidence/`.

Per the explicit reusable-gate instructions, read the listed clean-commit receipts instead of rerunning them:

- `retry`: `bun-1.4.3 test test/framework-error-policy.test.ts test/membrane-retry.test.ts test/recipe-retry-and-mcp-timeout.test.ts` — **38 pass, 0 fail, 84 assertions**. Receipt: `gate-retry-01M5XKBM7DV2HAX7J1MNWC2RR9.log`.
- `typecheck`: `node node_modules/typescript/bin/tsc --noEmit` — **pass**. Receipt: `gate-typecheck-01M5XKBKVC078E6TFB8BYDAJP7.log`.
- `diff-check`, `fleet-race`, and ten-sample `baseline-fleet-race` — inspected their named receipt logs; all pass.
- Baseline added-test transcript: **14 controls pass / 11 new-behavior failures**, naturally failing because no policy is supplied. Final focused and full-suite logs show all 25 added tests passing.

Own additional gates at the reviewed commit, all leaving the tree clean:

- `reviewer-completion-parity`: isolated `bun-1.4.3 --eval` probe — **515 partial-config cases and 40 invalid-field/error parity cases pass**, using the real Membrane constructor. Log: `gate-reviewer-completion-parity-01M5XMY2QS7Z3YYSWXSR8JQ7C6.log`.
- `reviewer-parity-sensitivity`: isolated `bun-1.4.3 --eval` probe — original passes; **all eight in-memory ignore-field mutants rejected**. Log: `gate-reviewer-parity-sensitivity-01M5XMZA7CR4EZZTK2KX74HATX.log`.

These reviewer logs are in this review run's evidence directory. The probes used the retained credential-free environment and inherited no-network filter; no adapter methods, providers, acquisition, or reinstall were invoked. Inspected the wrapper/filter source and matching retained digests. The existing real-entry capture exercises the production create-call boundary, then exits before framework startup.

### Residual test gaps and failure classification

Inspected the retained full-suite logs, without rerunning the suite: baseline **3280 pass / 413 fail / 6 errors**; implementation **3304 pass / 414 fail / 7 errors**. Existing failures are restricted-environment failures (including denied sockets, missing optional Web UI dependencies and unavailable npm), not evidence of an unrestricted green baseline.

Comparing named failures confirms exactly one additional name and no removed failures in the retained full-suite comparison: `test/fleet-children-file.test.ts:264-283`, the child-record synchronization case. The implementation suite timed out and observed `crashed` instead of `starting`. It passes in the retained isolated final-commit rerun and ten base samples. My repeated whole-file follow-up below reproduced the same timeout/assertion intermittently on the base as well as final. Classification is now **pre-existing harness-sensitive flake**, not a demonstrated change regression. Exact timing cause remains unproven. Do not claim identical original baseline/final failure counts or unrestricted full-suite qualification.

No live-provider end-to-end run or full framework retry-loop execution was performed. The capture proves host wiring and exercises the actual policy; pinned framework source establishes its consumers. Private dependency reflection is intentionally upgrade-sensitive.

### Manager follow-up checks

The manager requested repeated fleet-file comparison, an owner-visible 529 explanation, and answers about fractional counts, nested-empty input and CI coverage. Those checks are complete:

1. **Repeated fleet file:** `reviewer-fleet-repeated` alternated three runs each of `bun-1.4.3 test test/fleet-children-file.test.ts` on the exact base archive and reviewed worktree, under the retained isolation. Before running, verified 358 base source/test/config blobs against git and verified both trees used the same installed node_modules. Owner PID was recorded in the gate log. No matching fixture processes remained after completion.

   | Sample | Base pass/fail/errors; disputed case | Final pass/fail/errors; disputed case |
   |---|---|---|
   | 1 | 11/34/2; FAIL | 11/34/2; FAIL |
   | 2 | 12/33/1; PASS | 11/34/2; FAIL |
   | 3 | 12/33/1; PASS | 12/33/1; PASS |

   The gate correctly **exited 1**, not a pass: the whole file has restricted-harness failures. It supplies differential evidence, not a green qualification gate. The disputed case's timeout and `starting`/`crashed` assertion reproduce on unmodified base source. The affected fixture starts no framework/provider; the changed policy wiring is not on its path. Evidence: `gate-reviewer-fleet-repeated-01M5XN2QT3MZN4DVW4Z8VNCW31.log` in this review run.

2. **Owner-visible semantics, not a defect:** a non-empty `{maxRetries:4}` config uses the fully defaulted turn policy, including **seven total 529 sends and six jittered waits**. Nominal steps are 10, 20, 40, 80, 160, 300 seconds; actual equal-jitter waits are half to below each step. With random draw 0, waits are exactly 5, 10, 20, 40, 80, 150 seconds (305 seconds total). This differs from the unchanged non-turn host default, where overloaded is 0 and that config uses four sends/three base waits. Importantly, the old framework default already had the patient seven-send schedule: the wiring change must not be described as newly increasing 529 sends from four to seven relative to old turn behavior. The changelog and README disclose non-empty retry configuration's framework-wide behavior and its overloaded defaults.

3. **Fractional counts:** membrane 0.7.2's resolver checks finite/nonnegative, not integer, and preserves `maxRetries:2.5`. Integer failure numbers 1 and 2 satisfy `<2.5`, so ordinary failures wait 1000/2000 ms and failure 3 stops: three sends. The helper matches. Existing committed parity cases cover fractional backoffMultiplier, **not an explicit fractional maxRetries**. My `reviewer-fractional-count` gate directly checked 2.5 constructor preservation, policy-settings parity and the decision sequence; it passed. Evidence: `gate-reviewer-fractional-count-01M5XN3H1AJYXFTW63B8BK539Q.log`. This is unchanged validation/API behavior, not a new rejection or rounding rule.

4. **Nested empty object:** `{overloaded:{}}` intentionally counts as non-empty and installs the fully defaulted policy; `{}` omits it. The report explicitly calls this out, README describes non-empty objects and nested completion (`README.md:263-267`), and the committed tests cover both. For the current pinned framework, both happen to retain the same patient default 529 decisions, but the create-argument property presence is intentionally different.

5. **Normal test/CI discovery:** yes, `test/framework-error-policy.test.ts` is discovered by normal `bun test`; `package.json:20` maps npm test to that command, and `.github/workflows/ci.yml:75-77` runs it on Linux. The retained full-suite transcript confirms these tests actually ran. Tested resolution/field divergences therefore fail ordinary tests and CI when the divergent dependency is installed. Limits: CI uses lockfiles, so it does not automatically sample every future patch allowed by `^0.7.2`; consumers resolving a later patch before it has been tested remain an upgrade risk. The finite test matrix also cannot prove every possible future resolver change, particularly fractional-count-only changes. Optional hardening would add `{maxRetries:2.5}` to the committed parity matrix; this is not required to fix the pinned implementation.

## Fix Directions

No code fixes required. Carry the reproduced pre-existing fleet flake and restricted-harness limitations into separate qualification. Worktree and HEAD were left unchanged; no commits or pushes.
