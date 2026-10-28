# Documentation fix round

## Changed

- Amended commit: **`172f32662a570135c3fd06f0384794bbc47c9509`**.
- Exactly one commit on parent `8f2bb10353757921b8b9a49c570537a764bc68de`; replaces `8d068b4badcc9eebe65aabba6c1f960c8494eb03` on the assigned local branch.
- Author and committer: **Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>**.
- Trailer replaced with `Co-Authored-By: GPT-6-Astra <noreply@openai.com>`.
- `changelog.d/470-turn-retry.changed.md`: explains existing non-empty retry recipes moving turns onto the recipe-derived policy, default 529 sends dropping 13 → 7 and stated-wait ceilings becoming `max(60 s, maxRetryDelayMs)` per schedule instead of at least ten minutes. Gives explicit base/overloaded settings or omission of `agent.retry` as migration. Kept `.changed`.
- `src/membrane-retry.ts`: adds only the requested header sentence pointing to `src/framework-error-policy.ts` for turn handling.
- `README.md`: removes the hypothetical same-instance fleet-child clause; says fleet children each load their own recipe and policy.
- No runtime, tests, dependencies, lockfiles, or other source changes. No push, merge, or main-checkout edits. Working tree clean.

### Diff versus 8d068b4b

```
 README.md                             |  7 +++----
 changelog.d/470-turn-retry.changed.md | 12 +++++++++---
 src/membrane-retry.ts                 |  3 ++-
 3 files changed, 14 insertions(+), 8 deletions(-)
```

Full patch: [round2.diff](round2.diff). Metadata, parent/count, whitespace check, clean status, and process ownership: [round2-inspection.txt](round2-inspection.txt). Commit-message trailer replacement is in addition to the file diff above.

## Verification Gates

All three assigned gates ran **fresh at the amended commit**, through `orgasmic dispatch gate`, with Bun **1.4.3 (c6da4a4d3)**. PATH was prefixed with `/home/orgasmic/probe-kqnb1/tools/bun-linux-x64`. All receipts record a clean tree.

| Gate | Command | Result | Log |
|---|---|---|---|
| round2-install | `bun install --frozen-lockfile` | PASS; no changes | [log](gate-round2-install-01M62AFJXY3HD805Y7E9B4QBAP.log) |
| round2-test | `bun test` | **4182 pass, 0 fail**, 251 files, 29707 assertions, 340.26 s | [log](gate-round2-test-01M62AFKBGF9044NRR6M03GMHR.log) |
| round2-typecheck | `bun x tsc --noEmit` | PASS | [log](gate-round2-typecheck-01M62AFK36ZTP02VB6X1F493C3.log) |

Additionally, `git diff --check 8d068b4b HEAD` passed. The changed prose was checked against the current README/header/changelog and the installed Membrane `membraneRetrySettings` implementation: each ceiling is the larger of 60 seconds and that schedule's maximum delay. The round changes exactly three files, with the TypeScript change confined to a comment.

### Inherited evidence and its scope

No earlier gate receipt substitutes for a gate in this round. The earlier commit was amended rather than retained as an ancestor, so its passes are not claimed as reusable receipts at this SHA.

The following remain valid **historical evidence** for unchanged inputs:
- The base is still exactly `8f2bb103`; its successful baseline was **4157 pass / 0 fail**: [base test log](/home/orgasmic/.orgasmic/runs/run-01M628Q85HN1HVBHP7H2WZ5VT6/evidence/gate-base-test-01M6295T710EYN1Q0Z2TX2VHZ9.log).
- The original regression test contents and executable retry implementation are unchanged this round. On that exact base, the tests-only negative probe recorded **14 pass / 11 fail**: [negative-control log](/home/orgasmic/.orgasmic/runs/run-01M628Q85HN1HVBHP7H2WZ5VT6/evidence/gate-base-retry-negative-01M6295G4E294PZYP04NDESHPG.log). This is historical bug-reproduction evidence, not a passing receipt. The current full suite passes all 25 regression tests again.
- [Upstream inspection](/home/orgasmic/.orgasmic/runs/run-01M628Q85HN1HVBHP7H2WZ5VT6/evidence/upstream-inspection.txt) still describes the same base and locked agent-framework 0.29.0 / Membrane 0.8.0 packages. It established that upstream corrected retry documentation but did not wire this recipe setting into turn policy. No dependency or executable implementation changed this round.

## Unmet Criteria

None. All four requested corrections are incorporated into the single amended commit, all assigned gates pass, and nothing was pushed.

## Residual Risk

No live-provider or production-fleet exercise was performed. This round changes documentation and commit metadata only; the existing private-resolver maintenance risk is unchanged and covered by the retained constructor-oracle tests. Web typecheck was not rerun because this narrowed round assigns only the host typecheck and changes no web inputs.
