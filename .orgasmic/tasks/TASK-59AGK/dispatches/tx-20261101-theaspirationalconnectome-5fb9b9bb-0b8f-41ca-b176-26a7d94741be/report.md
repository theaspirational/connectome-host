# Review 2 — TASK-59AGK fix round 1 (connectome-host #523)

Reviewed: `607b8ef737f1f2d706a86fa019ebe25f735d7b40`, parent `925e4f70758a8d6e738bc70f0f3c1eb773be0a27`.
Confirmation review of the round-1 response to `/srv/review-drop/TASK-59AGK-REVIEW1/report.md`
(fix report and reply drafts: `/srv/review-drop/TASK-59AGK-FIX1/`).

## Verdict

**Approve — HIGH confidence.** All four brief items check out, the prior MEDIUM is fixed,
both LOWs are addressed (one with a real assertion, one with the permitted documented
limitation), and all three rerun gates pass at this commit. No new findings at HIGH or MEDIUM.

## Findings

No HIGH or MEDIUM findings.

### LOW — test — `test/framework-error-policy.test.ts:128` (carried, not a regression)

The ceiling-vs-exhaustion assertions are guarded by `if (observed.settings)`, so they run
only for the three explicit-policy cases; `omitted retry` and `empty retry` (the framework
default, where the host installs no `errorPolicy` and the probe has no settings to mirror
against) keep only the weaker `toEqual({ retry: false })` plus the no-`statedWaitMs` check.
Proved by mutation B below: inverting membrane's check order fails exactly the three explicit
cases and leaves both default cases green. This is a narrower hole than before (the paired
first-failure argument at `:124-126` still rules out exhaustion for every case, including the
defaults) and is consistent with the implementer's documented no-startup limitation. Fix
direction: optional — capture `TURN_RETRY_SETTINGS` in the probe (it is module-private today,
so this would need an upstream export) or leave as documented.

## Brief items verified

**1. Diff equality — CONFIRMED.** `git diff --stat 7919c9dc 607b8ef7` is exactly
`replies.md | 13 ---` plus `test/capture-retry-create.preload.ts | 6 +++++-` and
`test/framework-error-policy.test.ts | 14 ++++++++++++++`. `git diff 7919c9dc 607b8ef7 --
README.md changelog.d/` is empty (documentation blobs identical). Parent is exactly
`925e4f70`, `git ls-files replies.md` is empty, no worktree `replies.md` exists, and
`git diff 925e4f70 607b8ef7 -- .gitignore` is empty. Round-1 stat against the base is the
four project paths only (README, changelog fragment, two test files). The prior MEDIUM
(reply drafts committed into the PR commit) is fixed; the drafts live only at
`<impl evidence>/replies.md`.

**2. New ceiling-vs-exhaustion assertions — SOUND.**
- `DefaultErrorPolicy.onInferenceError` calls `retryDecision(cls, attempt + 1, error.retryAfterMs, this.settings)`
  (`node_modules/@animalabs/agent-framework/src/framework.ts:2398`), so the test's mirror at
  membrane `attempt = 1` matches the probe's framework `attempt: 0` exactly.
- Membrane's exhaustion check (`!(attempt < schedule.maxAttempts)`) precedes the stated-wait
  ceiling check (`node_modules/@animalabs/membrane/src/retry.ts`, `retryDecision`), so
  `{retry:false, statedWaitMs: ceiling+1}` at attempt 1 versus bare `{retry:false}` at
  `attempt === maxAttempts` is a real, load-bearing distinction — exactly what the new
  assertions pin. `expect(schedule.maxAttempts).toBeGreaterThan(1)` keeps the first mirror
  assertion from degenerating, and `settings[cls] ?? settings.retryable` correctly follows
  membrane's overloaded→retryable fallback for the `overloaded.maxRetries: 0` case.
- `.not.toHaveProperty('statedWaitMs')` is non-vacuous: the framework returns a literal
  `{ retry: false }` (no undefined-valued key that JSON would drop).
- Expected ceilings per case match `membraneRetrySettings` (`max(60 s, maxRetryDelayMs)`) and
  are all inside the probe's captured `statedWaits` grid, so a mismatch fails loudly rather
  than silently reading `undefined`.
- Mutation probes (unwrapped, `node_modules` backed up, restored, md5 re-verified):
  | Mutation | Effect on `bun test test/framework-error-policy.test.ts` |
  |---|---|
  | A: framework forwards `decision.statedWaitMs` on refusal | **5 fail** (all stated-wait cases) |
  | B: membrane checks the stated-wait ceiling before exhaustion | **3 fail** (the explicit-policy cases; both default cases pass — see LOW) |

**3. Fallback-limitation comment — ACCURATE.** The comment at
`test/capture-retry-create.preload.ts:17-21` says the probe mirrors "today's framework
fallback": the framework does `config.errorPolicy ?? new DefaultErrorPolicy()`
(`framework.ts:4529`), whose default settings are `TURN_RETRY_SETTINGS` (`:2377`). The probe
replaces `AgentFramework.create` outright, so the installed fallback's identity genuinely
cannot be observed without startup or private-state coupling, as claimed. The last clause is
also true: the omission controls assert only `hasErrorPolicy === false`.

**4. Reply drafts — ACCURATE.** Three drafts keyed by the same three
`discussion_r4408961777/785/791` URLs as the drafting report, all in English, all citing
`607b8ef7…`, no task ids or internal names. Draft 1's scope claim ("below/at/above-ceiling …
distinguish ceiling refusal from exhaustion **where the captured settings allow**") matches
the guarded coverage above, and the documented identity limitation is disclosed. Draft 2
keeps the partial-override rationale explicitly an undecided implementer's proposal, states
the breaking cost, and uses the corrected wording "this follow-up changes no runtime
behavior" (true: `607b8ef7` touches no `src/`). Draft 3's non-turn claims were verified
against the code in review 1 and the docs blobs are unchanged here.

## Verification Gates

Rerun at `607b8ef7`, clean tree, `PATH=/srv/ci-fixtures/bun-1.4.4-linux-x64:/opt/node-v22.23.3-linux-x64/bin:$PATH`,
through `orgasmic dispatch gate`; the suite wrapper moved generated `logs/` into evidence.

| Gate | Command | Result | Log |
|---|---|---|---|
| `review2-suite` | `sh -c 'bun test; … mv logs …'` | **PASS** 4296 pass / 0 fail, 257 files, 411.99 s | `gate-review2-suite-01M6BC0FYDB46R76XJ7SN9A7R7.log` |
| `review2-host-typecheck` | `node node_modules/typescript/bin/tsc --noEmit` | **PASS** exit 0 | `gate-review2-host-typecheck-01M6BC16HD8GDFP6YEBMNFTVF7.log` |
| `review2-web-typecheck` | `… --noEmit --project web/tsconfig.json` | **PASS** exit 0 | `gate-review2-web-typecheck-01M6BC19F8T46CWY0J799TYWXH.log` |
| mutation probes A/B (unwrapped) | `bun test test/framework-error-policy.test.ts` | each fails as expected; see table above | `progress.log` |

Counts match the implementer's claim and review 1 exactly (4296 pass; round 1 adds assertions,
not tests, so the count is unchanged from `7919c9dc`). The implementer's `round1-packaging`
receipt was read, not rerun, and its recorded delta matches my own `git diff`. Worktree left as
found: HEAD `607b8ef7`, `git status --porcelain` empty, no `logs/` directory, mutated
`node_modules` files restored and md5-verified against pre-probe checksums.

## Unmet Criteria / test gaps

- Acceptance is met: the prior MEDIUM is fixed, LOW (a) is fixed with a live assertion, LOW (b)
  took the permitted documented-limitation route, and the reply drafts satisfy the wording and
  destination criteria. No history rewrite, no push.
- Gaps (all carried from review 1, none introduced here): the framework's own `errorPolicy`
  fallback identity is mirrored, not observed; the two framework-default cases do not get the
  settings-based ceiling-vs-exhaustion assertions (LOW above); no stated-wait coverage on the
  `deadline` class or the non-`MembraneError` path; nothing exercises the real turn path
  end-to-end past `AgentFramework.create` (holds and admission).

## Residual Risk

- The partial-override policy question is still undecided; the branch as it stands is a
  breaking reduction in turn tolerance for recipes that set any `agent.retry` field. This
  round documents it and does not resolve it, as the assignment allowed — explicit owner
  acceptance is still needed before release.
- Ceiling expectations are hardcoded against upstream constants (`STATED_WAIT_CEILING_MS`,
  `PROVIDER_ACCELERATION_MAX_COOLDOWN_MS`); a dependency bump that moves them fails these
  tests loudly and will need a doc update alongside.
- Verification was local Linux with Bun 1.4.4 / Node 22.23.3 only, not the CI matrix.
- The reply drafts are unpublished; posting them is still a separate manual step.

## Fix Directions

1. Nothing blocking. Ship as-is pending the policy decision in draft 2.
2. Optional: lift the `if (observed.settings)` guard by capturing the framework's installed
   turn settings (needs an upstream export of `TURN_RETRY_SETTINGS`), or add one stated-wait
   case on the `deadline` class.
