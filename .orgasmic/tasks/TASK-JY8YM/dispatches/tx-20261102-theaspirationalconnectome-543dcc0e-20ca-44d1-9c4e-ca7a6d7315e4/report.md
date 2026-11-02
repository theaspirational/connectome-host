# Changed

Review integrity verified before edits: SHA-256 `97a22890fe57f109a26d64610bc72c0e92f577e0ae81cec30141359a09cf8f02` for `/srv/review-drop/upstream-reviews/connectome-host-523-R1-5732399297.md`; read in full.

Started at `c27bbc2d6fe44ef91ac35ed728388269101cdb33`. Rebased (rather than merged) onto the available fetched `upstream/main`, `5d60376e15087fff53b7e7648a51b5beabbdd470`: 133 commits beyond merge-base `a111f993ff34100bd2c3ab47c945564c0fa8591c`. Rebase was conflict-free, yielding pre-fix snapshot `2bc7ec116f2daf441937325cd748fc6e4f41bb31`. No network fetch was attempted. Dependencies now use agent-framework 0.32.0 and Membrane 0.8.0 from main's existing lockfiles.

| Finding | Disposition | Commit | Regression / verification |
|---|---|---|---|
| R1-1 | Fixed: carry the framework deadline schedule; use the registered expiry symbol; correct README | 09fd7328bb40e67b17302d46d5b62031cb73921a | Create-call probes for delay-only overrides and maxRetries 0/4/8; deadline settings compared to DefaultErrorPolicy's private settings oracle. Red: 22 pass / 13 fail. Green: 35 pass / 0 fail. |
| R1-2 | Fixed using the recommended turn-default completion; retain `.changed`, remove migration workaround | 301530b7d17452438df98ddbeab025c97b5fa58e | Independent default-policy comparison, partial-field assertions, explicit counts/zero controls, 13-send decisions and ceiling boundaries. Focused red: 0 pass / 2 fail (36 filtered); final combined green: 40 pass / 0 fail. |
| R1-3 | Fixed by the ceiling lift, plus corrected agent-scope documentation | 301530b7d17452438df98ddbeab025c97b5fa58e | Actual SubagentModule spawn → framework ephemeral run, 429/120 s and 529/360 s. Deterministic red: 0 pass / 2 fail; green: both pass within the combined 40/0. One completed run, two sends, exact selected sleep, no rate-limit throttle. |
| R1-4 | Fixed documentation: send counts are per policy cycle; separate default 2/8/32-minute resident re-arms apply under either policy | b459ca4352926ab191c9aa096aa97f2a7a554724 | Documentation-only; checked installed framework constant and absence of a source override (`runtime-and-rearm.txt`). No behavioral code change requiring a red test. |

**Option taken:** Preserve unspecified framework turn defaults, rather than Membrane-only completion. This avoids reducing patience for a partial override and eliminates the non-turn maximum-delay workaround. Explicit `overloaded.maxRetries`, including 0 or 7, remains an override. Accordingly, the whole-policy equality oracle restates the *base* Membrane defaults with the overload count omitted: passing the full DEFAULT_RETRY_CONFIG would explicitly request 7, which must not silently become 13. The fragment remains `470-turn-retry.changed.md`, as recommended for this option.

Fix-round changed paths: `src/framework-error-policy.ts`, `src/recipe.ts`, `test/capture-retry-create.preload.ts`, `test/framework-error-policy.test.ts`, new `test/framework-retry-subagent.test.ts`, `README.md`, `changelog.d/470-turn-retry.changed.md`. No authored dependency/lockfile edits or generated files; their upstream changes arrived through the rebase. No conventions/gotchas were supplied; applicable CONTRIBUTING.md was read.

# Verification Gates

All shell commands inherited `NO_UPDATE_NOTIFIER=1 npm_config_update_notifier=false`. Installs were offline and scripts-off: `npm ci --ignore-scripts --offline --no-audit --no-fund` and the same with `--prefix web`; logs `install.log` and `web-install.log`. Node 22.23.3, Bun 1.4.4, TypeScript 7.0.2 (`runtime-and-rearm.txt`).

Every test/typecheck gate used:

```sh
orgasmic dispatch gate <name> -- env XDG_RUNTIME_DIR=/run/user/1002 \
  systemd-run --user --scope -p MemoryMax=4G -p MemorySwapMax=0 <command>
```

Commands: `bun test` (full suite); `./node_modules/.bin/tsc --noEmit` (source); `./web/node_modules/.bin/tsc --noEmit -p web/tsconfig.json` (web).

| Snapshot | Full suite: pass / fail / skip (errors included in fail) | Source typecheck | Web typecheck |
|---|---|---|---|
| Upstream main/base `5d60376e` | 4592 / 5 / 0 (0 errors) | Pass, 0 diagnostics | Pass, 0 diagnostics |
| Supplemental rebased pre-fix `2bc7ec11` | 4605 / 8 / 0 (2 errors) | Pass, 0 diagnostics | Pass, 0 diagnostics |
| Final head `b459ca43` | 4633 / 4 / 0 (0 errors) | Pass, 0 diagnostics | Pass, 0 diagnostics |

Typecheck gate counts at each snapshot: **2 pass / 0 fail / 0 skip** (source and web). No files were excluded from the full-suite commands; Bun reported no skipped tests. The main baseline and head were started after both dependency installs; the supplemental pre-fix suite started before the web install completed, so two web-import files failed to load there. Its 2 setup errors are not asserted to be source regressions. Main is the authoritative upstream-base comparison.


Main was tested in a separate detached scratch checkout so the rebased pre-fix suite could finish without source changes. At main-gate start, the only untracked entries were dependency symlinks; root package-lock was byte-identical to the assigned checkout. Main and narrow scratch receipts are explicitly marked other-checkout and are not claimed as reusable clean-tree receipts. The assigned checkout's tracked inputs remained unchanged throughout its pre-fix suite. That run generated an untracked `logs/failures.log` (8 KB directory), which caused a dirty receipt; it was preserved in `base-generated-logs/`, not committed. Final required gates ran at the reported local head commit. The head suite likewise generated only untracked failure logs, preserved in `head-generated-logs/`; its dirty receipt does not reflect any tracked-source change. The assigned checkout is clean after preserving those outputs; `git-final.txt` records the commit, scoped diff, and successful whitespace checks.

| Gate | Log (relative to this report) |
|---|---|
| `main-tests` | `gate-main-tests-01M6DTH9JBWHRFGDSK2XVJ4X74.log` |
| `main-typecheck` | `gate-main-typecheck-01M6DTH1A28Q4F5DWWZB970SXC.log` |
| `main-web-typecheck` | `gate-main-web-typecheck-01M6DTZENA83CCB31RPV0EPSAF.log` |
| `base-tests` | `gate-base-tests-01M6DTASRZGB88Y4XQ0AEM95RX.log` |
| `base-typecheck` | `gate-base-typecheck-01M6DTAM5CPGYH2MJBVPGZD1ZF.log` |
| `base-web-typecheck` | `gate-base-web-typecheck-01M6DTD347K19161BEVAQX5MWH.log` |
| `head-tests` | `gate-head-tests-01M6DVR18VWVZ3DJ3C13ZFRTXV.log` |
| `head-typecheck` | `gate-head-typecheck-01M6DVR0A5D24G97G098K5ZK3E.log` |
| `head-web-typecheck` | `gate-head-web-typecheck-01M6DVR0PEA0JMSBK2VCJSGA99.log` |
| `committed-r1-1` | `gate-committed-r1-1-01M6DVPK5DHTNQKZVJ0NHBPDRS.log` |
| `head-retry-tests` | `gate-head-retry-tests-01M6DVRJJ6R9CKS72SRKWCNMNE.log` |
| `main-timeout-probe-final` | `gate-main-timeout-probe-final-01M6DVAGPSNJEYBWM456ACTXXH.log` |

At the actual commits: `committed-r1-1` passed 35/0 at `09fd7328`; `head-retry-tests` passed **40/0/0** at `b459ca43`. Both receipts were clean-tree passes. Test-created untracked failure logs are preserved as evidence rather than committed.


## Red/green evidence

Narrow commands were `bun test test/framework-error-policy.test.ts`, `bun test test/framework-error-policy.test.ts -t R1-2`, and `bun test test/framework-retry-subagent.test.ts`; combined green used both files. These ran in a separate scratch copy of the same rebased pre-fix tree while preserving the full baseline's inputs. The R1-1 production patch was applied first, then R1-2/R1-3. `r1-1.patch` preserves that first fix; `r1-2-production-green.ts` preserves the ceiling/floor implementation used by the probes. Final versions were transferred to the assigned checkout and committed, not committed in the scratch checkout.

- `gate-r1-1-red-01M6DTJY4PCXGGT2N6WNX5VZW7.log`: 22 pass / 13 fail, including all four new deadline cases.
- `gate-r1-1-green-01M6DTKRFMKFDDWGK2BH2MTJK8.log`: 35 pass / 0 fail.
- `gate-r1-2-red-final-01M6DTTVFMVD54ZCFAK1ADY982.log`: 0 pass / 2 fail, 36 filtered out. Shows missing overload allowance and 60/300-second rather than 600-second ceilings.
- `gate-r1-3-red-deterministic-01M6DTQZ3B1H4ZRJ23WE5J6DMF.log`: 0 pass / 2 fail; both launches return unsuccessful before the lift. The cooldown is zeroed only in the test to avoid an unrelated 30-second wait.
- `gate-r1-2-r1-3-green-final-01M6DTV4BJW6R8JQQABS0XR3AE.log`: 40 pass / 0 fail, including both real launch-path tests.
- Earlier exploratory runs are retained honestly: the first 429 red test timed out in the existing cooldown; one interim green attempt caught an overly broad test oracle that incorrectly treated an explicitly supplied overload count of 7 as omitted. The corrected final test and implementation respect the review's explicit-count preference.

## Failure classification

- **Environment-required:** the release-notice test inherits mandatory `NO_UPDATE_NOTIFIER=1`; `src/update/update.ts:100` explicitly disables checks under that setting, even when the test requests a check. It fails on main, the rebased pre-fix tree, and head. No notifier suppression was bypassed.
- **Pre-existing timing failures:** the queued prompt/maxTokens and per-session store-audit cases exceed Bun's default 5-second test budget. Both pass on unchanged main with `--timeout 30000`: **2 pass / 0 fail / 131 filtered** (`main-timeout-probe-final`). The diagnostic command was `bun test test/subagent-allowed-tools.test.ts test/update-audit-stores.test.ts --timeout 30000 -t "queued explicit prompt and maxTokens precedence survive caller disposal|each session.s store is opened"`, through the same memory-scoped gate wrapper. This did not replace or relabel the full-suite failures.
- **Pre-existing short-deadline fixtures:** async subagent tests use 1/1.5-second wall-clock limits and startup-latency assertions. The baseline logs show missed startup windows / reduced remaining startup time. These files were not changed. Exact head failure matches are below; no unrelated timeout or scheduling change was bundled into the fixes.
- **Supplemental setup errors:** pre-fix `web-wire.test.ts` and `web-health-without-ops.test.ts` could not resolve `solid-js`; that suite had already started when web dependencies were installed. Main and head have the complete web dependency installation from process start.

| Head failure | Also failed before fixes |
|---|---|
| an async run is stopped at its time limit, which covers its retries > a run not started by its deadline is dropped by the framework's startup watchdog, and reported as the time limit | upstream main, rebased pre-fix |
| an async run is stopped at its time limit, which covers its retries > an async run's limit counts from when it holds a slot, not from the call | upstream main, rebased pre-fix |
| in readline mode, a notice of a newer release that comes while the prompt waits is shown, each line whole, and what was typed is kept | upstream main, rebased pre-fix |
| subagent allowed-tools inheritance > queued explicit prompt and maxTokens precedence survive caller disposal | upstream main, rebased pre-fix |

Every named head failure was already observed before these fix commits. No new named failure or import error was introduced. Raw counts are not presented as an all-green suite.


# Draft review reply

- R1-1: Addressed in 09fd7328bb40e67b17302d46d5b62031cb73921a. Rebased onto `5d60376e`, retained the separate deadline schedule, and corrected the expiry marker and documentation. Deadline regressions were red before and green after.
- R1-2: Addressed in 301530b7d17452438df98ddbeab025c97b5fa58e. Took the recommended turn-default completion, so partial overrides retain 13 overload sends and 10-minute ceilings. Explicit counts (including 7) still override; the default-equality test therefore restates base fields without that nested count. Kept `.changed` and removed the workaround.
- R1-3: Addressed in 301530b7d17452438df98ddbeab025c97b5fa58e. The ceiling lift keeps stated waits inside subagent runs. Real launch-path regressions cover 429/120 s and 529/360 s; docs distinguish subagents from resident/fork holds and remove the workaround.
- R1-4: Addressed in b459ca4352926ab191c9aa096aa97f2a7a554724. Documented per-cycle counts and the independent default 2/8/32-minute resident re-arms under either policy.


# Draft PR title/body update

Title: fix(recipe): apply retry overrides without replacing turn defaults

## Problem
`agent.retry` configured other provider calls but did not reach framework turn retries (#470).

## Changes
- Apply non-empty recipe retry overrides at the shared framework creation call; omitted or empty settings retain the default policy.
- Preserve unspecified turn defaults: the overload allowance, at-least-10-minute stated-wait ceilings, and one fresh call after a deadline cut. Explicit overload counts, including zero, remain overrides.
- Explain subagent policy waits versus resident/fork provider holds, and distinguish retry cycles from later resident re-arms. No maximum-delay migration workaround is required.
- Rebased onto `5d60376e15087fff53b7e7648a51b5beabbdd470` (agent-framework 0.32.0). This replaces the outdated claim that only a version/changelog commit had landed since the original base.

## Tests
- `bun test`: main **4592 pass / 5 fail / 0 skip**; head **4633 pass / 4 fail / 0 skip**. All named head failures were also observed before the fixes; see the verification report for the environment/timing classifications.
- Source and web typechecks: **2 pass / 0 fail / 0 skip** on both main and head.
- Targeted retry tests at head: **40 pass / 0 fail / 0 skip**.

Deadline, partial-default, boundary and real subagent launch regressions were shown red before the fixes and green after.

## Not verified
No live-provider runs. The launch-path tests record the requested retry sleep instead of waiting several minutes.

Generated with OpenAI Codex.


# Unmet Criteria

All four findings are addressed in local commits. No review finding remains open. Required base/head commands completed; the full suite is not all-green because of the classified baseline failures above. No behavior finding was skipped or rejected.

# Residual Risk

- No live-provider or external-service exercise under the loopback-only restriction.
- The production-path stated-wait tests record the framework's selected sleep rather than waiting 120/360 wall-clock seconds; they do exercise actual subagent launch, provider error propagation, retry selection and single-run completion.
- The framework does not export its turn deadline schedule. A small copied schedule and turn lifts remain version-coupled; reflection-oracle tests detect drift on dependency upgrades.
- No push, PR creation, or public posting was performed. Reply and PR update remain drafts.

Report prepared: 2026-11-02 10:34:09 UTC.
