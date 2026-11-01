# Review — TASK-59AGK (connectome-host #523 bot-review fix)

Reviewed: `7919c9dc84c2616c81416e76a757cea4c7fee82a` on base `925e4f70758a8d6e738bc70f0f3c1eb773be0a27`.
Also judged as text: `b51f2c84` (replies.md wording only).

## Verdict

**Approve — HIGH confidence** on the technical substance: the new stated-wait tests are
genuine regression tests (proved by three mutation probes), and every README/changelog
claim I checked is accurate against the actual retry code. All assigned gates pass at
`7919c9dc`.

One packaging defect should be fixed before this branch is pushed (Finding 1): the
review-reply drafts are committed into the PR commit itself, which the dispatch brief and
the implementer report both assume is not the case.

## Findings

### MEDIUM — docs/scope — `replies.md:1` (added by `7919c9dc`, not by `b51f2c84`)

`replies.md` — a file of GitHub review-reply drafts, keyed by `discussion_r...` URLs — is
committed as a tracked repo-root file by the very commit intended for PR #523, not by the
later drafts-only commit.

- `git show --stat 7919c9dc` lists `replies.md | 13 +++++++++`; `b51f2c84` only rewords it
  (`3 insertions(+), 3 deletions(-)`).
- `git check-ignore -v replies.md` → not ignored (exit 1).
- `git log --all --diff-filter=A -- replies.md` → first and only occurrence in repo history;
  no precedent for committing review correspondence.

Failure scenario: anyone pushing `agent-retry-turn-policy` ships `replies.md` to the repo
root and into the PR diff, where it reads as a self-review artifact — the opposite of what
the drafts are for. The brief's premise ("the later commit b51f2c84 only adds replies.md …
won't be pushed") is wrong about which commit carries it, so the risk is currently
unguarded. The implementer report's own wording ("committed `replies.md`") confirms the
intent was deliberate, but it conflicts with the handoff assumption.

Fix direction: keep the drafts out of the tracked tree (a durable copy already exists at
`<impl evidence>/replies.md`) — drop the file from `7919c9dc` (and `b51f2c84`) before any
push, or add it to `.gitignore`. No test or behavior change is involved.

### LOW — test — `test/capture-retry-create.preload.ts:19`

For the `omitted retry` / `empty retry` cases the probe builds its own
`new DefaultErrorPolicy()` rather than observing the policy the framework actually
installs. That matches upstream today (`node_modules/@animalabs/agent-framework/src/framework.ts`:
`config.errorPolicy ?? new DefaultErrorPolicy()`), and the comment says so, but the mirror
is unasserted: if upstream changed its fallback, the two "framework default" boundary cases
would keep passing while real turn behavior moved. Fix direction: optionally assert the
fallback identity (e.g. capture the policy the framework ends up holding) or leave as a
documented limitation — the pre-existing `hasErrorPolicy === false` control already pins the
host half.

### LOW — test — `test/framework-error-policy.test.ts:124`

`expect(decisions[ceiling + 1]).toEqual({ retry: false })` cannot distinguish a ceiling
refusal from attempt exhaustion, and it does not pin that Membrane's `statedWaitMs` (returned
by `retryDecision` above the ceiling) is deliberately dropped by `DefaultErrorPolicy`. Also
untouched by stated waits: the `deadline` class and the non-`MembraneError` path. Low impact
— the mutation probes below show the assertions do fire on the realistic breakages — but the
above-ceiling case is the weakest of the four.

### LOW — docs — `replies.md:10` (`b51f2c84` wording)

"Runtime behavior is unchanged" is true of this follow-up commit relative to `925e4f70`
(no `src/` file changed), but in a public thread about a breaking policy it can be read as
"the released behavior is unchanged", which is false — the changelog fragment is titled
**Behavior change for recipes setting `agent.retry`**. Suggest "this follow-up changes no
runtime behavior" instead.

## Judgement on the three findings

**Finding 1 (stated-wait ceiling coverage) — real regression tests, confirmed.** Expected
ceilings/backoffs are hardcoded constants, not re-derived from the code under test, and three
mutation probes in `node_modules` (ignored, backed up, restored, checksums re-verified) each
fail the new tests:

| Mutation | Effect |
|---|---|
| `@animalabs/membrane/src/retry.ts`: `STATED_WAIT_CEILING_MS` 60 s → 120 s | **3 new tests fail** (`partial recipe`, `larger maximum delays`, `overloaded schedule disabled`) |
| `@animalabs/agent-framework/src/framework.ts`: policy passes `undefined` instead of `error.retryAfterMs` | **5 new tests fail** (every stated-wait case) |
| same file: `TURN_RETRY_SETTINGS` drops `Math.max(…, PROVIDER_ACCELERATION_MAX_COOLDOWN_MS)` | **2 new tests fail** (`omitted retry`, `empty retry`) |

Note: under Bun these packages resolve through their `"bun"` export condition to `src/*.ts`,
not `dist/*.js` — an equivalent `dist` mutation is a no-op, so the probes above target `src`.
Coverage matches what the bot asked for: below / at / above ceiling for 503, 429 and 529,
for the framework default and for explicit recipes (partial, raised caps, overloaded
disabled), plus "a short stated wait never shortens backoff" (`decisions[500]`). Jitter is
deterministic via the pre-existing `Math.random = () => 0` in the preload, so the exact
`delayMs: 5000` overloaded expectations are not flaky.

**Finding 2 (partial override replaces turn defaults) — proposal text accurate, README
warning correct.** `src/framework-error-policy.ts:29-34` completes from `DEFAULT_RETRY_CONFIG`
and `:38-40` installs a fresh `DefaultErrorPolicy` for any non-empty object, so a partial
override does replace the omitted framework turn defaults — overloaded sends 13 → 7
(`OVERLOADED_TURN_TRIES` vs `DEFAULT_RETRY_CONFIG.overloaded.maxRetries`) and stated-wait
ceilings 10 min → `max(60 s, maxRetryDelayMs)` (`membraneRetrySettings`). The reply is
explicitly labelled the implementer's proposal, states the breaking cost, claims no decision
and says acceptance is still required — which satisfies the "documented rationale, marked as
proposal" allowance. Only nit is the LOW wording item above.

**Finding 3 (migration workaround) — docs accurate.** Checked each claim:

- "raises an explicit policy's send limit and stated-wait ceilings": with
  `overloaded.maxRetries: 13`, `maxRetryDelayMs: 600000`, `overloaded.maxRetryDelayMs: 600000`
  → overloaded `maxAttempts = max(3,13) = 13`, both ceilings `max(60 s, 600 s) = 600 s`. ✓
- "not an exact restoration … also raises the backoff delay caps": base `maxDelayMs`
  30 s → 600 s, overloaded 300 s → 600 s versus the default turn policy. ✓
- "enables the long overloaded schedule for compression, retrieval, session naming and other
  non-turn calls": `hostMembraneRetry` is `{ ...recipeRetry, overloaded: { maxRetries: 0,
  ...recipeRetry.overloaded } }`, so `maxRetries: 13` overrides the host's disabling 0 and
  `membraneRetrySettings` then emits an `overloaded` schedule. ✓ (pinned by the new
  `the longer-wait recipe also raises non-turn caps…` test)
- "Retry fields do not configure the framework's separate provider-hold/admission rules":
  `holdProviderAcceleration` / `nonOwningProviderWait` run **before** `errorPolicy` in the
  turn path and are driven by `PROVIDER_ACCELERATION_*` constants and host hold consultation,
  never by the retry settings. ✓
- "Omit `agent.retry` or use `{}` to keep the default turn policy without enabling that
  non-turn schedule": `frameworkErrorPolicy` returns `{}` for both, and
  `hostMembraneRetry({})` keeps `overloaded.maxRetries: 0`. ✓
- No stale copy of the old "retain the longer turn policy" advice remains (grep for
  `600000` / `600_000` across `*.md`).
- Recipe validation accepts the documented shape (`src/recipe.ts:2392-2425`).

## Verification Gates

All at `7919c9dc`, clean tree, `PATH=/srv/ci-fixtures/bun-1.4.4-linux-x64:/opt/node-v22.23.3-linux-x64/bin:$PATH`,
run through `orgasmic dispatch gate`; the suite wrapper moved generated `logs/` into evidence
as the implementer did.

| Gate | Command | Result | Log |
|---|---|---|---|
| `review-head-suite` | `sh -c 'bun test; … mv logs …'` | **PASS** 4296 pass / 0 fail, 257 files, 419 s | `gate-review-head-suite-01M6BAR57JPHZS9ADVZ4ZV73CQ.log` |
| `review-head-typecheck` | `node node_modules/typescript/bin/tsc --noEmit` | **PASS** (exit 0) | `gate-review-head-typecheck-01M6BAVK2J2QR126AVGMH1133K.log` |
| `review-head-web-typecheck` | `… --noEmit --project web/tsconfig.json` | **PASS** (exit 0) | `gate-review-head-web-typecheck-01M6BAVKE2HCE5HV2WJ6XX57KE.log` |
| focused (unwrapped probe) | `bun test test/framework-error-policy.test.ts test/membrane-retry.test.ts` | PASS 34/34 after restoring mutations | — |
| mutation probes (unwrapped) | see table above | each fails the new tests as expected | `progress.log` |

Reproduced counts match the implementer's claim exactly (head 4296 vs base 4290 = +6 new
tests: 5 stated-wait cases + 1 non-turn case). I did not re-run the base suite; the brief
asked only for `7919c9dc`, and the base receipt is reusable at
`gate-baseline-suite-ready-01M6B9T0ZD5ARPGMY6JZKQN8GE.log` (4290 pass). The implementer's
first baseline run's two `solid-js` module-load errors are credibly classified as
environment/setup (web deps installed after Bun started) — the dependency-ready rerun at the
unchanged base passed 4290/4290.

Worktree left as found: `git status --porcelain` empty, HEAD still `7919c9dc`, no `logs/`
directory, mutated `node_modules` files restored and checksum-verified.

## Unmet Criteria / test gaps

- Acceptance is met on all three findings (two fixed with regression tests, one documented as
  an explicitly-marked implementer proposal), with the caveat that the reply drafts live
  inside the PR commit (Finding 1) rather than outside the tracked tree.
- No test covers the framework's own `errorPolicy` fallback (LOW #1) or stated waits on the
  `deadline` / non-`MembraneError` paths (LOW #2).
- Nothing exercises the real turn path end-to-end: the probe stops at `AgentFramework.create`,
  so provider holds, admission and the interaction between a hold and the policy remain
  untested here. The docs correctly claim only that retry fields do not configure them.

## Residual Risk

- The underlying policy question (Membrane-default vs framework-default completion for partial
  overrides) is still undecided; shipping as-is is a breaking reduction in turn tolerance for
  recipes that set any `agent.retry` field. The commit documents this rather than resolving it,
  as the assignment allowed.
- Ceiling expectations are hardcoded against upstream constants (`STATED_WAIT_CEILING_MS`,
  `PROVIDER_ACCELERATION_MAX_COOLDOWN_MS`); a dependency bump that changes them will fail these
  tests loudly, which is the intended behavior but will need a doc update alongside.
- Verification was local Linux with Bun 1.4.4 / Node 22.23.3 only, not the CI matrix.

## Fix Directions

1. Before pushing: remove `replies.md` from the tracked tree (or ignore it) and keep the drafts
   in evidence; reply text itself needs no change beyond the LOW wording nit.
2. Optional: reword "Runtime behavior is unchanged" → "this follow-up changes no runtime
   behavior" in the Finding-2 reply.
3. Optional test hardening: pin the framework's `errorPolicy` fallback, and add one stated-wait
   case on the `deadline` class.
