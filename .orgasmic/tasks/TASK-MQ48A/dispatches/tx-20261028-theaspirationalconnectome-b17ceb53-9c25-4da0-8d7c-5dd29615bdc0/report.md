# Rebase agent.retry turn-policy fix

## Changed

- New commit: `8d068b4badcc9eebe65aabba6c1f960c8494eb03` on the assigned local branch `task-mq48a-impl`.
- Its sole parent is `8f2bb10353757921b8b9a49c570537a764bc68de` (0.17.0); exactly **one commit** above that base. Both `upstream/main` and `origin/main` resolved locally to the requested base. No remote update or push was needed or performed.
- Rebased original `cc0d1f7e47c438ff9cdb7542a43492ef68e42643`. Author and committer are **Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>**. Retained the public AI co-author trailer required by CONTRIBUTING.md. No internal task/worker names occur in added source/documentation lines or the final commit message.
- Runtime intent is unchanged: non-empty `agent.retry` creates a framework-wide `DefaultErrorPolicy`, completing partial/nested fields from Membrane defaults; omitted/empty config leaves the framework default alone. Non-turn Membrane behavior is untouched.
- **README.md conflict:** replaced upstream's statement that retry settings only reach non-turn calls with the turn-policy section/link, preserving upstream's surrounding MCPL/fleet documentation. Retained upstream's fuller non-turn call list. Clarified explicit-policy versus framework-default overloaded counts and stated-wait ceilings.
- **src/recipe.ts conflict:** replaced the obsolete non-turn-only JSDoc with the same turn-policy contract while retaining upstream's non-turn call list. No schema/validation changes.
- `src/index.ts` merged automatically: the two-line wiring change sits at upstream's current create call, preserving its heartbeat, gate, quota-hold, and other options. The helper and tests needed only comment-version updates from Membrane 0.7.2 to 0.8.0. Test assertions and preload are unchanged from the original fix.
- No dependency, lockfile, generated-file, or unrelated changes. Working tree is clean.

### Upstream retry assessment

Upstream **did not fix or partly wire this host setting into turn policy**. Commit `365015d` corrected README/recipe/helper comments to acknowledge that `agent.retry` reaches only non-turn calls. Upstream `src/index.ts` still passes retry solely through `hostMembraneRetry`, with no `errorPolicy` at `AgentFramework.create`.

The base advances agent-framework 0.27.0 → 0.29.0 and Membrane 0.7.2 → 0.8.0. Inspection of the locked packages confirms that `DefaultErrorPolicy` accepts explicit settings unchanged, and the private Membrane partial-config resolver still matches the helper. The current framework default has 13 overloaded attempts and stated-wait ceilings of at least ten minutes; explicit Membrane-derived settings have 7 overloaded attempts by default and ceilings of at least one minute (or max delay if longer). The original fix intentionally preserves the framework defaults only when recipe retry is omitted/empty; the updated docs make this distinction explicit. Quota/admission handling remains upstream's.

Evidence: [upstream inspection](upstream-inspection.txt), [original conflicts](rebase-conflicts.diff), [range diff](rebase-range-diff.txt), [final metadata and scope inspection](final-inspection.txt).

### Diff stat versus 8f2bb103

```
 README.md                             |  46 ++++++++++++-
 changelog.d/470-turn-retry.changed.md |   4 ++
 src/framework-error-policy.ts         |  41 ++++++++++++
 src/index.ts                          |   2 +
 src/recipe.ts                         |  24 +++----
 test/capture-retry-create.preload.ts  |  28 ++++++++
 test/framework-error-policy.test.ts   | 120 ++++++++++++++++++++++++++++++++++
 7 files changed, 251 insertions(+), 14 deletions(-)
```

## Verification Gates

All commands below ran through `orgasmic dispatch gate <name> -- <command>`. Clean base/final successful gates have recorded reusable receipts at the respective commits; no prior-run receipts were reused. Bun **1.4.3** was supplied by prepending `/home/orgasmic/probe-kqnb1/tools/bun-linux-x64` to PATH. `bun x tsc` is used because the available Bun distribution has no `bunx` executable.

| Gate | Command | Result | Log |
|---|---|---|---|
| base-install | `bun install --frozen-lockfile` | PASS, clean base | [log](gate-base-install-01M628RD74KWWMECQM61EDVH0E.log) |
| base-web-install | `npm ci --prefix web --ignore-scripts --no-audit --no-fund` | PASS | [log](gate-base-web-install-01M628S2E9SCD9NXKGX6870YJA.log) |
| base-test | `bun test` | **4157 pass, 0 fail**, 250 files, 29644 assertions | [log](gate-base-test-01M6295T710EYN1Q0Z2TX2VHZ9.log) |
| base-typecheck | `bun x tsc --noEmit` | PASS | [log](gate-base-typecheck-01M628S260SDW6XAJ8N1M68C8X.log) |
| base-web-typecheck | `sh -c 'cd web && bun x tsc --noEmit'` | PASS | [log](gate-base-web-typecheck-01M628S8VGG2WQFW6BR1JD5AMP.log) |
| base-retry-negative | `bun test test/framework-error-policy.test.ts` | Expected exit 1: **14 pass, 11 fail** | [log](gate-base-retry-negative-01M6295G4E294PZYP04NDESHPG.log) |
| retry-focused | `bun test test/framework-error-policy.test.ts test/membrane-retry.test.ts test/recipe-retry-and-mcp-timeout.test.ts` | **38 pass, 0 fail** on resolved tree | [log](gate-retry-focused-01M629H43PC37PG2HGAP98QKMK.log) |
| rebase-typecheck | `bun x tsc --noEmit` | PASS on resolved tree | [log](gate-rebase-typecheck-01M629HKG9FTDFQK9FCPNCH72D.log) |
| final-install | `bun install --frozen-lockfile` | PASS, no changes | [log](gate-final-install-01M629J736C5YDF0K60Z3AWWQD.log) |
| final-test | `bun test` | **4182 pass, 0 fail**, 251 files, 29707 assertions | [log](gate-final-test-01M629J7X5KBSA7598MF3QDF90.log) |
| final-typecheck | `bun x tsc --noEmit` | PASS | [log](gate-final-typecheck-01M629J78ST54P7AGA5XNG9FEK.log) |
| final-web-typecheck | `sh -c 'cd web && bun x tsc --noEmit'` | PASS | [log](gate-final-web-typecheck-01M629J7MGTD6METZCMX73E3SR.log) |

**Negative-control reproduction:** at the exact base, temporarily copy only `test/framework-error-policy.test.ts` and `test/capture-retry-create.preload.ts` from the original commit and run the negative command above. The 11 turn-policy assertions fail because the production create call lacks the policy, while all 14 controls pass. Both temporary files were removed before the successful clean base suite. The final suite passes all 25 added tests. Original test snapshots are preserved alongside this report.

**Production-path probe:** those tests spawn the real host entrypoint with isolated mock recipes; the preload intercepts the actual `AgentFramework.create` configuration before framework startup, inspecting both its error policy and the constructed Membrane settings. Retry decisions use the real locked framework/Membrane APIs. No provider requests or live service credentials are needed.

**Earlier environment failures, resolved:** the first base-install and base-typecheck invocations could not spawn `bun`/`bunx`; PATH and command spelling were corrected. The initial full suite started before the web install completed and reported missing `solid-js` in two web-importing files (4140 pass, 2 fail, 2 errors). After installation both files passed (17 tests), and the full clean base rerun passed all 4157. These are setup failures, not product regressions. Logs are retained as `base-install.console.log`, `base-typecheck.console.log`, `base-test.console.log`, and [web recheck](gate-base-web-tests-01M6295AGNRNGVJCC4PP6XTBX9.log).

`git diff --check 8f2bb103 HEAD` passed; final metadata, author, parent, commit count, clean status, and internal-name scan are recorded in `final-inspection.txt`. Read the supplied project baseline and repository CONTRIBUTING.md; supplied conventions/gotchas were explicitly absent, and no applicable AGENTS.md was found.

## Unmet Criteria

None. Required base and result gates are green; the regression tests fail without the fix; the local result is committed with the requested identity. Nothing was pushed, landed, or merged into the main checkout.

## Residual Risk

- No live-provider outage or production fleet exercise was performed; verification is offline and uses the real host configuration path and locked retry-policy implementations.
- Membrane still exposes no public partial-config resolver. The small mirrored resolver remains tied to its locked API, with constructor-oracle tests guarding drift.
