# TASK-59AGK — fix round 1

## Changed

Single new commit: `607b8ef737f1f2d706a86fa019ebe25f735d7b40` — test(retry): cover wait ceilings and clarify migration limits.
Its parent is exactly `925e4f70758a8d6e738bc70f0f3c1eb773be0a27` on the new `task-59agk-fix` branch.

Recreated `7919c9dc84c2616c81416e76a757cea4c7fee82a` via `git cherry-pick --no-commit`, removed its staged `replies.md`, and committed only the four project paths, including the two requested LOW changes. No earlier branch history was rewritten; no push or GitHub writes.

| Round request | Status / evidence |
|---|---|
| Remove reply drafts from tracked tree | Fixed. `git ls-files replies.md` is empty, no worktree `replies.md` exists, and `.gitignore` is unchanged. |
| LOW (a): ceiling versus exhaustion / dropped metadata | Added paired first-failure assertions: a wait at the ceiling retries, while one above it refuses and has no `statedWaitMs` property. For the three explicit-policy cases, their already-captured settings independently prove Membrane returns `statedWaitMs` for ceiling refusal but not for attempt exhaustion. The policy response is asserted to discard it. |
| LOW (b): fallback identity | Used the permitted documented-limitation route. The preload stops before framework startup and therefore cannot observe the installed fallback identity without starting the framework or coupling to private state. The comment explicitly warns that a changed upstream fallback could escape these default cases; omission controls pin only the host half. |
| Reply wording / destination | All three URL-keyed English drafts are only at `/home/orgasmic/.orgasmic/runs/run-01M6BBCCDXHRQ7N5M7BRZFFZR6/evidence/replies.md`, reference the new SHA, and use “this follow-up changes no runtime behavior”. The partial-override rationale remains explicitly an undecided implementer's proposal. |

The diff from the requested base changes only:
- `README.md`
- `changelog.d/470-turn-retry.changed.md`
- `test/capture-retry-create.preload.ts`
- `test/framework-error-policy.test.ts`

README/changelog blobs exactly match `7919c9dc`. Relative to that reviewed commit, the only changes are removal of `replies.md` and the two requested test-file hardenings. The packaging gate verifies these path sets, parentage, absence of drafts, unchanged ignore rules, identical documentation, and whitespace; its log contains the complete test-only delta.

The original three findings remain covered: stated-wait regression tests, an explicitly undecided policy proposal for partial-default completion, and accurate migration side-effect guidance/test. This round changes no runtime implementation or dependency inputs.

## Verification Gates

All checks used `orgasmic dispatch gate <name> -- <command>` and the requested
`PATH=/srv/ci-fixtures/bun-1.4.4-linux-x64:/opt/node-v22.23.3-linux-x64/bin:$PATH`.

| Gate | Command | Result |
|---|---|---|
| `round1-suite` | Full `bun test`, with generated `logs/` moved to evidence and original exit status preserved | PASS at final commit, clean tree: 4296 tests / 0 failures / 257 files / 30463 assertions |
| `round1-host-typecheck` | `node node_modules/typescript/bin/tsc --noEmit` | PASS at final commit, clean tree |
| `round1-web-typecheck` | `node node_modules/typescript/bin/tsc --noEmit --project web/tsconfig.json` | PASS at final commit, clean tree |
| `round1-packaging` | `sh "$ORGASMIC_EVIDENCE_DIR/verify-packaging.sh"` | PASS at final commit, clean tree |
| `round1-focused` | `bun test test/framework-error-policy.test.ts test/membrane-retry.test.ts` | PASS, 34 tests / 182 assertions; pre-commit dirty-tree probe, not claimed as a reusable final-head receipt |

Exact full-suite command:
`sh -c 'bun test; result=$?; if [ -d logs ]; then mv logs "$ORGASMIC_EVIDENCE_DIR/generated-logs-$(date -u +%Y%m%dT%H%M%S)-$$"; fi; exit "$result"'`

### Inherited evidence and scope

Read `/srv/review-drop/TASK-59AGK-REVIEW1/report.md` in full. Its review findings guided only the requested packaging/wording/test changes; its earlier head gates and mutation probes are **not** claimed as passes in this round.

Historical base comparison: the prior clean `baseline-suite-ready` log at the exact unchanged base records 4290 passes / 0 failures / 257 files. Its scope is only that base, not the new head. The runtime source, dependency manifests/lockfiles, typecheck configuration, and web inputs remain unchanged from that base; the tests differ, so this round runs all assigned final-head gates afresh. No new baseline run was requested. Inspected log:
`/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-baseline-suite-ready-01M6B9T0ZD5ARPGMY6JZKQN8GE.log`.
The base log's identity/result and the unchanged-input check are recorded in `/home/orgasmic/.orgasmic/runs/run-01M6BBCCDXHRQ7N5M7BRZFFZR6/evidence/inherited-baseline-scope.log`.

Current gate logs:

- `/home/orgasmic/.orgasmic/runs/run-01M6BBCCDXHRQ7N5M7BRZFFZR6/evidence/gate-round1-focused-01M6BBDPNAGFHGPE1JS9TE81HK.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6BBCCDXHRQ7N5M7BRZFFZR6/evidence/gate-round1-host-typecheck-01M6BBEC6T7N5AQHV6N3ZR2PVK.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6BBCCDXHRQ7N5M7BRZFFZR6/evidence/gate-round1-packaging-01M6BBF2438KNM1C7BMQ9G6T6Y.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6BBCCDXHRQ7N5M7BRZFFZR6/evidence/gate-round1-suite-01M6BBEB30QSFC7BHGN54R31JR.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6BBCCDXHRQ7N5M7BRZFFZR6/evidence/gate-round1-web-typecheck-01M6BBECF34KQHT5KCM1E84M7P.log`

All three assigned final-head gates passed afresh at the reported commit. No earlier head gate was reused or inferred. The worktree is clean after moving the suite-generated logs into this evidence directory.

## Unmet Criteria

None. LOW (b) follows the brief's permitted comment/documented-limitation option rather than adding a brittle identity assertion.

## Residual Risk

- The no-startup probe still mirrors, rather than observes, the framework's installed default policy. A future upstream fallback replacement could escape those default-policy cases; this is now explicitly documented.
- The partial-override policy remains an undecided proposal. The reduced turn tolerance already present in the PR still needs explicit acceptance or a later runtime change; this packaging/test follow-up decides neither.
- These policy tests do not exercise live providers or the full hold/admission path. Verification was local Linux with the requested toolchain, not the entire CI matrix.
