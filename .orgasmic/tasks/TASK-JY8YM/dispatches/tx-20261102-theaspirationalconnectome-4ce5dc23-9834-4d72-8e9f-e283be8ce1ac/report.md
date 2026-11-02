# Verdict: APPROVE

## Findings

No HIGH, MEDIUM, or LOW actionable findings. All R1 findings are addressed at the reviewed head `b459ca4352926ab191c9aa096aa97f2a7a554724`.

| Request | Independent disposition |
|---|---|
| R1-1 | `09fd7328`: `src/framework-error-policy.ts:8-11,58` preserves AF 0.32's separate two-attempt, 1-second deadline schedule. The preload uses the registered expiry symbol. Tests compare the installed framework's settings, not the host's copied constant. Actual ephemeral launches independently confirmed two sends/one selected 1000 ms sleep for both `maxRetries:0` and `maxRetries:8`, with success after one cut and failure after two consecutive cuts. |
| R1-2 | `301530b7`: `src/framework-error-policy.ts:47-55` retains the overload floor and stated-wait ceilings for unspecified fields. The undefined check preserves explicit overload counts, including 0 and 7. Base counts still override ordinary retries and retain the rate-limit floor. Tests cover nested partials, explicit counts, zero, default equivalence and larger maximum delays. |
| R1-3 | `301530b7`: ceiling lift avoids handing ordinary 120/360-second stated waits back to the subagent restart mechanism. The real SubagentModule/framework ephemeral tests passed for 429/120 s and 529/360 s: one completed run, two sends, exact selected wait, no throttle. `README.md:360-369` correctly distinguishes these policy waits from resident/fork holds and the separate ephemeral cap. |
| R1-4 | `b459ca43`: `README.md:376-378` explicitly describes per-cycle counts and independent default 2/8/32-minute resident re-arms. Confirmed the installed framework constant and no host override. |

The copied deadline settings are cloned per policy; an additional ownership probe mutated one instance and confirmed that the next policy and upstream defaults were unchanged. Other settings are newly built before the turn-only lifts. No mutation of upstream defaults or non-turn Membrane settings was found.

### Rebase

`git range-diff a111f993..c27bbc2d 5d60376e..2bc7ec11` maps both original commits with `=`. The complete original and rebased net patches are byte-identical after removing blob-index and hunk-location headers; no added/deleted content or context differs. See `rebase-comparison.txt`, both saved patches and their normalized counterparts. The AF 0.30 → 0.32 dependency behavior change is the known reason for R1, not a silent edit to the replayed commits. Main's dependency/lockfile inputs are unchanged by the PR itself.

### Documentation and publication drafts

Read README, recipe JSDoc, changelog fragment, the final title/body at `drafts/pr-update-final.md`, and `drafts/reply.md` in the reviewed evidence. Their behavior, test counts, commit references, scope distinctions and not-verified qualifications are supported by the code/logs. `.changed` is consistent with taking the maintainer's recommended preservation option. No internal worker identities, machine names, orchestration names, task IDs or private filesystem paths occur in these publication materials. Generic product terms such as resident, subagent and fleet host are appropriate. `drafts/pr-update.md` is an intermediate template containing `GATE_NUMBERS`, not the final publication draft.

## Verification Gates

All commands retained `NO_UPDATE_NOTIFIER=1` and `npm_config_update_notifier=false`. No installs or network access were needed. All executed test/typecheck/probe processes ran under:

```
env XDG_RUNTIME_DIR=/run/user/1002 systemd-run --user --scope -p MemoryMax=4G -p MemorySwapMax=0 ...
```

Input review SHA-256 matched `97a22890fe57f109a26d64610bc72c0e92f577e0ae81cec30141359a09cf8f02`. Read supplied project baseline and CONTRIBUTING; no additional conventions/gotchas were supplied.

### Existing head passes inspected and reused

These clean-tree gates are at the exact reviewed commit; read their logs rather than rerunning them in the worktree:

- `head-retry-tests`: `bun test test/framework-error-policy.test.ts test/framework-retry-subagent.test.ts` — **40 pass / 0 fail**, 238 assertions.
- `head-typecheck`: `./node_modules/.bin/tsc --noEmit` — exit 0, no diagnostics.
- `head-web-typecheck`: `./web/node_modules/.bin/tsc --noEmit -p web/tsconfig.json` — exit 0, no diagnostics.

Logs: /home/orgasmic/.orgasmic/runs/run-01M6DT8DDR5HRY1NJMPRW3AK56/evidence/gate-head-retry-tests-01M6DVRJJ6R9CKS72SRKWCNMNE.log; /home/orgasmic/.orgasmic/runs/run-01M6DT8DDR5HRY1NJMPRW3AK56/evidence/gate-head-typecheck-01M6DVR0A5D24G97G098K5ZK3E.log; /home/orgasmic/.orgasmic/runs/run-01M6DT8DDR5HRY1NJMPRW3AK56/evidence/gate-head-web-typecheck-01M6DVR0PEA0JMSBK2VCJSGA99.log.

### Full suite: raw main/head evidence independently compared

| Snapshot | Command | Pass | Fail | Skip | Load errors |
|---|---|---:|---:|---:|---:|
| main `5d60376e` | `bun test` | 4592 | 5 | 0 | 0 |
| head `b459ca43` | `bun test` | 4633 | 4 | 0 | 0 |

Inspected original logs, extracted failures independently into `independent-failure-comparison.json`, and saved diagnostic contexts in `main-tests-failure-excerpts.txt` and `head-tests-failure-excerpts.txt`. The head-only failure set is empty. Logs: /home/orgasmic/.orgasmic/runs/run-01M6DT8DDR5HRY1NJMPRW3AK56/evidence/gate-main-tests-01M6DTH9JBWHRFGDSK2XVJ4X74.log and /home/orgasmic/.orgasmic/runs/run-01M6DT8DDR5HRY1NJMPRW3AK56/evidence/gate-head-tests-01M6DVR18VWVZ3DJ3C13ZFRTXV.log.

These full suites are **failed documentary evidence, not reusable passing receipts**. Main's receipt had dependency symlinks/untracked output; head produced an untracked failure log. I do not claim either as a clean all-green gate. I did not repeat two 23–25-minute suites within this 30-minute review; instead I inspected raw diagnostics and independently rechecked the exact failing cases in disposable source snapshots.

The four head failures are exactly:

1. `test/readline-stderr.test.ts:205`: newer-release notice — environment-required. `src/update/update.ts:100` suppresses polling when mandatory `NO_UPDATE_NOTIFIER=1` is set, while this test expects polling. Main and head have the same 20-second missing-notice symptom.
2. `test/subagent-allowed-tools.test.ts`: queued explicit prompt/maxTokens precedence — exceeds Bun's default 5-second budget on both full runs. My narrow `--timeout 30000` rechecks passed on both main and head (about 17.0 and 13.4 seconds).
3. `test/subagent-own-failures.test.ts:434`: startup watchdog test's remaining-startup-time assertion — main and head full logs both fail the >1000 ms expectation within a 1500 ms budget. It passed on both narrow rechecks.
4. `test/subagent-own-failures.test.ts:466`: queued run's remaining-startup-time assertion — same >1000 ms expectation and short budget, failed in both full logs. Narrow recheck failed on main (677 ms) and passed on head, reinforcing timing sensitivity rather than regression.

All these test files and their production paths are unchanged by the PR. Main's fifth failure was the existing per-session store audit timeout; the implementer's main `--timeout 30000` diagnostic passed it, and its file is likewise unchanged. No head-only import error or failure was hidden by count comparison.

### New independent gates

1. `review-mutations-source` — `python3 /home/orgasmic/.orgasmic/runs/run-01M6DX8V6BVZ039A95F2PTGZBQ/evidence/mutation-review.py`, exit 0. Seven production mutations were killed by the existing tests, plus three active dependency-default drift mutations; restored source passed **40/0**, 238 assertions. Individual commands, filters and diagnostics are saved in the script, `mutation-results.json`, and per-mutation `.log` files.
2. `review-deadline-production` — `bash -c 'cd "$1" && bun test deadline-production-probe.test.ts' bash <scratch>`, exit 0: **5 pass / 0 fail**. Four actual SubagentModule → framework ephemeral deadline probes and one settings-ownership probe. Source retained as `deadline-production-probe.ts`.
3. `review-baseline-and-failures` — `python3 /home/orgasmic/.orgasmic/runs/run-01M6DX8V6BVZ039A95F2PTGZBQ/evidence/baseline-review.py`, exit 0 for the diagnostic wrapper. Independently reran both main typechecks, each exit 0. Ran the four selected main/head failure cases with `bun test test/readline-stderr.test.ts test/subagent-allowed-tools.test.ts test/subagent-own-failures.test.ts --timeout 30000 -t 'run not started by its deadline|limit counts from|queued explicit prompt and maxTokens|notice of a newer release'`. Inner test outcomes are deliberately reported, not treated as passing: main **2 pass / 2 fail**, head **3 pass / 1 fail**. See `main-failure-recheck.log`, `head-failure-recheck.log`, and `baseline-review-results.json`.

New gate logs: /home/orgasmic/.orgasmic/runs/run-01M6DX8V6BVZ039A95F2PTGZBQ/evidence/gate-review-mutations-source-01M6DXCDATXNXSYKMN78Z19P8K.log; /home/orgasmic/.orgasmic/runs/run-01M6DX8V6BVZ039A95F2PTGZBQ/evidence/gate-review-deadline-production-01M6DXFA92Z8E1E17V8C1MGW6J.log; /home/orgasmic/.orgasmic/runs/run-01M6DX8V6BVZ039A95F2PTGZBQ/evidence/gate-review-baseline-and-failures-01M6DXEGT5X53CYP09QXFFZ357.log.

### Mutation results

| Mutation | Existing test selector | Result |
|---|---|---|
| Remove explicit deadline schedule | R1-1 | Killed, 4 fail |
| Change copied deadline first delay to 2000 | R1-1 | Killed, 4 fail |
| Reduce copied overload floor 13 → 7 | R1-2 | Killed, 2 fail |
| Reduce stated-wait floor 600000 → 60000 | R1-2 | Killed, 2 fail |
| Apply overload floor even to explicit counts | turn settings retain | Killed, 2 fail |
| Treat explicit base count 0 as omitted | maxRetries:0 stops | Killed, 1 fail |
| Remove production create-call spread | R1-1 | Killed, 4 fail |
| Upstream deadline attempts 2 → 3 | R1-1 | Killed, 4 fail |
| Upstream overload attempts 13 → 14 | R1-2 | Killed, 2 fail |
| Upstream wait ceiling 10 → 11 minutes | R1-2 | Killed, 2 fail |

No active mutation survived. The dependency probes changed the private copied package's **src/framework.ts**, which Bun selects through the package's `bun` export. An initial exploratory pass changed only inactive `dist/src/framework.js`; those three no-op drift mutations survived, as expected for an unloaded file. They are not counted as kills or evidence of a test blind spot. That run and its results remain recorded. An earlier launch failed before testing because Bun was absent from PATH; corrected PATH points to the existing Bun 1.4.4 fixture. Neither setup issue changed the reviewed tree.

## Unmet Criteria / Test Gaps

No unresolved acceptance finding. Full-suite comparison uses inspected original raw logs plus new targeted reproduction, not fresh full-suite runs. No live provider calls or multi-minute wall-clock waits were performed; retry sleeps are intercepted and their selected durations asserted. The preload intentionally does not exercise a hypothetical future replacement of AgentFramework's default-policy class; that separate upstream change would need integration coverage.

## Residual Risk

The deadline schedule and turn lifts remain version-coupled because AF does not export them. The active source-drift probes demonstrate that today's reflection oracles detect changes to all three copied values. Very short timing fixtures and the environment-incompatible update-notice fixture remain red/flaky independently of this change. No new product regression was identified.

## Fix Directions

None required for this PR. Publish only the final title/body and reply drafts, not the intermediate template. Keep the dependency-default oracle tests when upgrading AF/Membrane. Any future stabilization of existing timing/update tests should be a separate change.

Reviewed worktree remains detached at the original head and clean. No tracked edits, commits, pushes or posts were made; all probe sources, mutations and artifacts are outside it.
