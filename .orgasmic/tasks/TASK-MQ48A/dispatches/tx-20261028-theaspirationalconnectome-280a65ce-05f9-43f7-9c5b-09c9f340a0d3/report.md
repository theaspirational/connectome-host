# Review — TASK-MQ48A: rebase of the `agent.retry` turn-policy fix onto 0.17.0

Commit reviewed: `8d068b4badcc9eebe65aabba6c1f960c8494eb03`, sole parent
`8f2bb10353757921b8b9a49c570537a764bc68de` (0.17.0). Rebase of
`cc0d1f7e47c438ff9cdb7542a43492ef68e42643`.

## Verdict

**Approve — HIGH confidence.** The rebase preserves the original fix's runtime
intent byte-for-byte (the only production/test delta from `cc0d1f7` is a
`0.7.2` → `0.8.0` comment bump), the two conflict resolutions are accurate
against upstream's current code, every numeric claim added to README.md and
`src/recipe.ts` checks out against the locked `@animalabs/agent-framework`
0.29.0 and `@animalabs/membrane` 0.8.0, and I independently reproduced the
negative control and all five required gates. One MEDIUM docs finding
(changelog fragment understates the operator-visible behavior change) and two
LOW findings; none block.

## Findings

### MEDIUM

- **MEDIUM docs `changelog.d/470-turn-retry.changed.md:1`:** the fragment is
  the only upgrade-facing text an operator reads, and it omits the half of the
  behavior change that can *reduce* resilience for a recipe that already sets
  `agent.retry`. For such a recipe, turns move off the framework default and
  onto the recipe-derived policy, which means (verified against the locked
  packages, see Verification Gates):
  - 529 sends drop from 13 (`OVERLOADED_TURN_TRIES`,
    `framework.ts:2440`) to Membrane's 7 (`DEFAULT_RETRY_CONFIG.overloaded`,
    `membrane/src/types/config.ts:264`);
  - stated-wait ceilings drop from at least ten minutes
    (`PROVIDER_ACCELERATION_MAX_COOLDOWN_MS`, `framework.ts:2439/2445`) to
    `max(60 s, maxRetryDelayMs)` (`membrane/src/retry.ts`,
    `membraneRetrySettings`). Concretely: with `{"maxRetries": 4}`, a 503
    carrying a 5-minute `retry-after` is waited out today (ceiling 600 s) and
    after this change ends the turn (retryable ceiling 60 s).

  The fragment says only "Turns retain Membrane's default overloaded schedule
  unless overridden", which reads as *no loss*. README.md:323-327 does state
  both reductions correctly — the gap is the changelog alone.

  Fix direction: add one bullet (or extend the existing one) naming the two
  reductions and the migration (set `overloaded.maxRetries` / `maxRetryDelayMs`
  explicitly to keep the old patience, or drop `agent.retry` to keep the
  framework default). While there: consider whether this belongs in
  `.breaking.md` with the `- **Recipe authors:** …` opener CONTRIBUTING.md:115
  requires, since an existing recipe's turn behavior changes without the
  recipe changing. Precedent cuts the other way — the reverse change in
  0.14.1 (`CHANGELOG.md:460-480`) was filed under **Changed** — so `.changed`
  is defensible, and a `.breaking` fragment would force a minor bump
  (`scripts/release-bump.mjs:19-23`). Maintainer's call; the content gap is the
  part worth fixing.

### LOW

- **LOW docs `src/membrane-retry.ts:1-13`:** the commit updated README.md and
  `src/recipe.ts` for the new turn behavior but left this header unrevised. It
  is still literally true of the Membrane config it returns, but it closes with
  "A recipe's `agent.retry` sets every field, its `overloaded` schedule
  included" and opens with "agent-framework … retries them itself, so this
  config reaches only the host's own other calls" — a reader now concludes that
  `agent.retry` does not reach turns, which this commit makes false. Upstream
  spent `365015d` fixing exactly this class of staleness in this exact file, so
  a maintainer is likely to flag it. Fix direction: one sentence pointing at
  `src/framework-error-policy.ts` for the turn half.

- **LOW docs `README.md:302-305`:** "any fleet children running in the **same
  framework instance**" has no referent — every fleet child is a detached
  `bun src/index.ts` subprocess (`src/modules/fleet-module.ts:2`), so no fleet
  child ever shares the instance. The following sentence says this correctly;
  the clause before it invites the opposite reading. Fix direction: drop the
  clause, or say "fleet children each load their own recipe" directly.

### Checks that came back clean (no finding)

- **Conflict resolution, README.md.** Upstream's replaced sentence (`8f2bb103`,
  README.md:284-286, from `365015d`) said retry reaches only non-turn calls.
  The resolution replaces exactly that sentence with a link to the new section
  and preserves upstream's surrounding MCPL/`requestTimeoutMs`/env-allowlist
  prose untouched. Anchor `#provider-retries-agentretry` matches the heading
  `### Provider retries (\`agent.retry\`)` under GitHub's slug rules.
- **Conflict resolution, `src/recipe.ts:274-290`.** The obsolete non-turn-only
  JSDoc is replaced; upstream's fuller non-turn call list ("compression and
  merging, retrieval, session naming and the TUI status line") is carried over
  rather than reverted to the original's shorter list. No schema or validation
  change — `validateRecipe`'s retry block (`src/recipe.ts:2316-2348`) is
  untouched, so README's "Recipe validation still runs first; unknown keys,
  negative values and non-finite numbers are rejected" is accurate.
- **"Upstream did not wire retry into turn policy" — confirmed.** `git grep
  errorPolicy 8f2bb103 -- src/` is empty; upstream's only retry path is
  `src/index.ts:1038 retry: hostMembraneRetry(recipe.agent.retry)`, and
  `365015d` is documentation-only (it says so in its own message). The
  framework therefore falls through to `config.errorPolicy ?? new
  DefaultErrorPolicy()` (`framework.ts:4268`) at the base.
- **Helper correctness against the locked resolver.** `completeRetryConfig`
  (`src/framework-error-policy.ts:13-35`) is logically identical to Membrane's
  private `resolveRetryConfig` / `resolveRetryFields`
  (`node_modules/@animalabs/membrane/src/membrane.ts:312-338`), down to the
  `TypeError`/`RangeError` messages and the `undefined`-means-omitted rule for
  nested fields. The tests also pin it against the real `Membrane` constructor
  as an independent oracle, so a drift in Membrane's defaults fails the suite
  rather than diverging silently.
- **Omitted/empty retry leaves the framework default.** `frameworkErrorPolicy`
  returns `{}` for `undefined` and for `{}` (`src/framework-error-policy.ts:38-41`),
  the spread then contributes no `errorPolicy` key, and the framework uses its
  own default. Proven on the production path, not just by reading:
  `test/framework-error-policy.test.ts` spawns the real `src/index.ts` with a
  preload that intercepts the actual `AgentFramework.create` config and asserts
  `Object.hasOwn(config, 'errorPolicy') === false`.
- **Single framework-wide policy, as documented.** `AgentFramework.create` is
  called exactly once in `src/` (`src/index.ts:497`), and the framework holds
  one `errorPolicy` used by both turn paths (`framework.ts:21608`, `23452`), so
  "covers the resident, forks and subagents in that instance" is right.
- **Every numeric claim added to the docs verified** against the locked
  packages: 13 vs 7 overloaded sends; base default 3 sends; rate-limit floor of
  5 regardless of `maxRetries`; `maxRetries` counting total sends
  (`retryDecision`'s `attempt < maxAttempts`), so `maxRetries: 4` is 4 sends /
  3 waits at 1 s, 2 s, 4 s; `maxRetries: 0` stopping ordinary resends but not
  the initial send or the class floors; overloaded send limit =
  `max(base, overloaded)`; equal jitter drawn from `[step/2, step)`;
  `overloaded.maxRetries: 0` falling back to the unjittered retryable schedule;
  stated-wait ceiling `max(60 s, maxRetryDelayMs)`; the framework deliberately
  classifying a stream deadline as a retryable timeout.
- **No new failure mode from the mirrored validator.** Recipe validation runs
  at load (`src/recipe.ts:1666`) and `new Membrane(...)` (`src/index.ts:1039`)
  runs its own resolver before `createFramework` (`src/index.ts:1090`), so
  `completeRetryConfig`'s throw branches are unreachable belt-and-braces, not a
  new rejection path.
- **Scope and upstream-readiness.** Diff is 7 files / +251 −14; no dependency,
  lockfile, generated-file or unrelated change. `src` is wholesale in
  `package.json`'s `files`, so the new module packs. Fragment name
  `470-turn-retry.changed.md` matches `changelog.d/README.md`'s format. One
  commit, right parent, author and committer `Vasyl Dovhanchyn
  <48733736+theaspirational@users.noreply.github.com>`. No internal names:
  `git diff 8f2bb103 HEAD | grep -iE "orgasmic|TASK-|WO-2|wo2|devbox|dispatch|ledger|probe-"`
  is empty. Pre-existing oddity `agents: [agentConfig],` with no indentation at
  `src/index.ts:500` is upstream's (`git show 8f2bb103:src/index.ts`), not
  introduced here.
- **Attribution.** The inherited `Co-Authored-By: OpenAI Codex
  <codex@openai.com>` trailer satisfies CONTRIBUTING.md:77's requirement of *a*
  trailer, though it names a tool rather than a model and every recent upstream
  commit uses `Claude Opus 5.5 <noreply@anthropic.com>`. Nothing enforces it in
  CI. Flagging only so whoever opens the PR can confirm it reflects who
  actually wrote the rebased prose; not a finding.

## Verification Gates

All run by me in this worktree at `8d068b4`, through `orgasmic dispatch gate`.
No implementer receipt was reused.

| Gate | Command | Result | Log |
|---|---|---|---|
| review-install | `bun install --frozen-lockfile` | exit 0, "no changes" | `gate-review-install-01M62A2Q9ZXE81V1VS5TWWK64J.log` |
| review-web-install | `npm ci --prefix web --ignore-scripts --no-audit --no-fund` | exit 0, 94 packages | `gate-review-web-install-01M62A2SY2X7RZPCAJN56NJWTZ.log` |
| review-test | `bun test` | exit 0, **4182 pass, 0 fail**, 251 files, 29707 assertions | `gate-review-test-01M62A328ZESE5JHCYWADNAT91.log` |
| review-typecheck | `bun x tsc --noEmit` | exit 0 | `gate-review-typecheck-01M62A6H3DRVNMDDFGHEHR0ZYK.log` |
| review-web-typecheck | `sh -c 'cd web && bun x tsc --noEmit'` | exit 0 | `gate-review-web-typecheck-01M62A6KSYJMNAYCV7Y8XH8Z1B.log` |

Bun 1.4.3 from `/home/orgasmic/probe-kqnb1/tools/bun-linux-x64` on PATH.

**Negative control, reproduced independently (not reused).** I added a
throwaway detached worktree at the base `8f2bb103` outside the tracked tree
(`/tmp/mq48a-neg`), symlinked this worktree's `node_modules`, copied in *only*
`test/framework-error-policy.test.ts` and
`test/capture-retry-create.preload.ts` from `8d068b4`, confirmed
`grep -c frameworkErrorPolicy src/index.ts` was `0`, and ran
`bun test test/framework-error-policy.test.ts`:

```
 14 pass
 11 fail
 48 expect() calls
Ran 25 tests across 1 file. [3.81s]
```

The 11 failures are exactly the turn-policy assertions (`observed.decisions`
is `undefined`, `hasErrorPolicy` false); the 14 that pass are the controls
(Membrane-side settings, recipe-validation rejections). This matches the
implementer's claim. The scratch worktree was removed
(`git worktree remove --force`); `git worktree list` and
`git status --porcelain` are back to their original state.

**Production-path probe.** The acceptance criterion is exercised on the real
path, not a unit stub: the tests spawn `process.execPath src/index.ts <recipe>`
with a `--preload` that replaces `AgentFramework.create` and captures the
actual config object — the live `errorPolicy` instance (asserted
`instanceof DefaultErrorPolicy`), its resolved settings, the constructed
Membrane's own `retrySettings`, and `onInferenceError` decisions for synthetic
`MembraneError`s of every retry class across 8 attempts. `Math.random` is
pinned so the equal-jitter sequences are deterministic. No provider call or
credential is involved.

**Range-diff fidelity.** `rebase-range-diff.txt` plus
`git diff 8f2bb103 HEAD` show the original-to-rebased delta in
`src/framework-error-policy.ts` and `test/framework-error-policy.test.ts` is a
single `0.7.2` → `0.8.0` comment change; `src/index.ts`'s two-line wiring
landed at upstream's current `create` call with its `mcplServers`, heartbeat,
gate and quota-hold options intact. The README/`recipe.ts` prose differs from
the original by adopting upstream's wording and by the newly added, verified
claims about the 13-vs-7 counts and the ceilings.

## Unmet criteria / test gaps

No unmet acceptance criterion.

- `completeRetryConfig`'s `TypeError`/`RangeError` branches
  (`src/framework-error-policy.ts:19-24`) have no direct test. They are
  unreachable through the product (recipe validation and Membrane's own
  resolver both reject first), so this is dead-code coverage rather than a
  risk, but a drift in recipe validation would go unnoticed here.
- No test asserts the *difference* from the framework default — i.e. nothing
  pins "with `agent.retry` set, overloaded sends are 7 and not 13" or the
  60 s-vs-600 s stated-wait ceiling. Every retry test also passes no
  `retryAfterMs`, so the stated-wait ceiling behavior (the MEDIUM finding's
  user-visible consequence) is unexercised. A single case passing an error with
  `retryAfterMs: 300_000` and asserting `{retry: false, ...}` on the
  recipe-derived policy would both document and lock the change.
- No live-provider exercise; verification is offline against the locked
  packages, which is appropriate here.

## Residual risk

- The mirrored resolver stays correct only while Membrane keeps no public
  partial-config resolver. The constructor-oracle tests fail loudly on drift,
  so the risk is a *failing suite* on a Membrane bump, not silent divergence.
  That is the right trade.
- An operator with `agent.retry` already set gets the behavior change in the
  MEDIUM finding at upgrade. It is intentional and documented in README.md;
  the exposure is the changelog gap, not the code.
- New: a recipe setting a large `maxRetryDelayMs` (or
  `overloaded.maxRetryDelayMs`) now raises the *turn* stated-wait ceiling above
  the framework's ten-minute cap, since the ceiling is
  `max(60 s, maxRetryDelayMs)` with no upper bound. Before this change nothing
  in a recipe could do that. README.md:323-325 states the mechanism; it does
  not warn. Operator-controlled and bounded by the framework's chunked sleeps,
  so LOW exposure, but worth knowing given the `#300` hold on
  `DefaultErrorPolicy` sleeping a stated wait uncapped
  (`test/release-holds.test.ts:63`).

## Fix directions

1. Extend `changelog.d/470-turn-retry.changed.md` to name the 13 → 7 overloaded
   sends and the ten-minute → `max(60 s, maxRetryDelayMs)` stated-wait ceiling
   for recipes that already set `agent.retry`, with the one-line migration.
   Decide `.changed` vs `.breaking` deliberately (precedent supports
   `.changed`).
2. Add a sentence to `src/membrane-retry.ts`'s header pointing at
   `src/framework-error-policy.ts` for the turn half, so the file is not stale
   the way `365015d` found it.
3. Drop or reword the "same framework instance" clause at README.md:302-303.
4. Optional, closes the test gap: one case asserting the recipe-derived policy
   refuses a `retryAfterMs` past its ceiling where the framework default would
   have waited it.

None of these change the shipped behavior; items 1-3 are text.
