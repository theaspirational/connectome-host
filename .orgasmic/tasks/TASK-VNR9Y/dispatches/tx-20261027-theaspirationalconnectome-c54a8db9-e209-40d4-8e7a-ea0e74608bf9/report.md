# TASK-VNR9Y implementation report

## Changed

- Local branch `task-vnr9y-impl`; starting HEAD `3e4cf9b39df97f00f9e62e09571201966142c0f3` (required base); implementation commit `cc0d1f7e47c438ff9cdb7542a43492ef68e42643`. No push, PR, tag, version bump, publish, framework changes or ledger hand edits.
- Seven files: `src/index.ts`, `src/framework-error-policy.ts`, `src/recipe.ts`, `test/framework-error-policy.test.ts`, `test/capture-retry-create.preload.ts`, `README.md`, `changelog.d/470-turn-retry.changed.md`. 243 insertions / 11 deletions. No manifests, lockfiles, generated files or fixtures changed.
- Ordinary tier, priority trigger (P1). No coupling, safety-path or breadth trigger; final scope seven files / 232 net lines. Project constraints and task read through the prescribed routes. No gotcha or convention files were present; no convention matched. Repository conventions read: `CONTRIBUTING.md` (evidence, attribution, changelog, testing), `changelog.d/README.md`, `bunfig.toml`, `tsconfig.json`, `.github/workflows/ci.yml`.

### Environment and acquisition

| Item | Result |
|---|---|
| Starting HEAD | `3e4cf9b39df97f00f9e62e09571201966142c0f3` |
| Bun | `bun-1.4.3 --version`: `1.4.3` |
| Bun binary SHA-256 | `7ce7d6b654eddeec20afddf2396317fba6dc19ab52788a27e894e1806bf8f24b` |
| Node | `node --version`: `v22.23.3` |
| Installed agent-framework | `0.27.0` |
| Installed membrane | `0.7.2` |
| `bun.lock` before / after acquisition / after change | `d8f0a118fd6631de88e7d36df7d2c41fa329df6ca173d879e04d7943f2943128` (all identical) |

Frozen install command: `bun-1.4.3 install --frozen-lockfile --ignore-scripts --registry https://registry.npmjs.org/`. It ran under `env -i` with fresh empty HOME, XDG config and cache directories, from an isolated copy of the committed root manifest and lock, then the installed `node_modules` was relocated to the worktree. No user/project/global credential configuration was exposed; no auth variables or lifecycle scripts were used. All 180 lock entries were checked for public-registry source form before acquisition. 137 packages installed; no reinstall or dependency-tree change. Optional root postinstall/Web UI build was not run.

### Requirement evidence and findings

| Requirement | Evidence |
|---|---|
| R1, no policy for silent recipe | `src/framework-error-policy.ts:38-40`, spread at `src/index.ts:657`; production create-argument control at `test/framework-error-policy.test.ts:50-52`. Property absence, not an undefined property, is asserted. |
| R2, complete defaults and actual DefaultErrorPolicy | `src/framework-error-policy.ts:13-40`; constructor-oracle parity cases at `test/framework-error-policy.test.ts:63-78`, capture at `test/capture-retry-create.preload.ts:7-27`. |
| R3, no host overloaded default in turn policy | Helper uses `DEFAULT_RETRY_CONFIG`, not `hostMembraneRetry`. `src/membrane-retry.ts` is byte-for-byte unchanged; Membrane call remains `src/index.ts:1352`. Exact config and resolved settings controls at `test/framework-error-policy.test.ts:53-58`. |
| R4, validation unchanged and first | Only the retry field's documentation changed in `src/recipe.ts:267-280`; validation remains at `src/recipe.ts:2282-2314`. `loadRecipeDetailed` validates at `src/recipe.ts:3514`, called at `src/index.ts:200`, before framework creation at `src/index.ts:654`. Nine new invalid-input controls at `test/framework-error-policy.test.ts:113-118`; existing validation tests also pass. |
| R5, framework-wide scope | Documented at `README.md:269-273` and `src/recipe.ts:270-272`; pinned source findings below. |
| R6, docs and behavior-change entry | `README.md:261-295`, `src/recipe.ts:267-280`, `changelog.d/470-turn-retry.changed.md:1-4`, following the fragment convention. |
| R7, no warning fallback | Non-empty input always constructs and supplies the policy. No warnings or fallback added. |

**R2 finding:** membrane 0.7.2's `resolveRetryConfig` is a non-exported function (`node_modules/@animalabs/membrane/src/membrane.ts:430-457`). Public package exports (`node_modules/@animalabs/membrane/package.json`, `src/index.ts`, `src/types/index.ts:195-207`, `src/retry.ts:245-268`) expose defaults, types and `membraneRetrySettings`, but no partial-config resolver. Implemented a small documented field-by-field completion/validation helper matching that function, including undefined-as-omitted and nested defaults. Tests use Membrane's actual constructor and read its private cached settings as an independent oracle, then compare the captured policy against `new DefaultErrorPolicy(oracleSettings)`. This accounts for the framework constructor's intentional stated-wait-ceiling adjustment. A future divergence fails the parity tests.

**Empty object decision:** only a non-empty object counts as set. `{}` and omission both produce no `errorPolicy` property; `{overloaded:{}}` is non-empty and installs the fully defaulted turn policy. The empty-object test is correctly classified as a control, green on the base, not a manufactured red.

**R5 pinned source evidence:** `node_modules/@animalabs/agent-framework/src/framework.ts:2854` stores one `errorPolicy`; `:3739` chooses it once at framework creation. Resident and conversation-fork turns consult that shared member at `:19666` (conversation agents are created within the same framework at `:13702`); ephemeral/subagent turns consult it at `:21361`. Host subagents use that same framework's `createEphemeralAgent` and `runEphemeralToCompletion` (`src/modules/subagent-module.ts:3395,3460,3620,3782`). Therefore the policy covers the resident, forks, subagents, and any child using that framework instance. The host's FleetModule actually launches separate processes, passing each child's recipe to its own host entry (`src/modules/fleet-module.ts:1959-1960`). Each child loads/validates its own recipe (`src/index.ts:200,1049`) and creates its own policy (`:657`), not the parent's policy.

### Retry counts from the pinned APIs

Source: `node_modules/@animalabs/membrane/src/retry.ts:205-228,245-268`, `src/types/config.ts:288-305`, and `node_modules/@animalabs/agent-framework/src/framework.ts:2036-2076`. Framework argument `attempt` is zero-based earlier failures; failure number N is passed as N-1 and becomes N for `retryDecision`.

`maxRetries` is a total-send bound including the first send, despite its name. Stop below means no resend after that numbered failure. The first send still happens with zero. Overloaded positive bound is `max(base, overloaded)`; rate limits have at least five sends. The policy has no effect on other provider admission/hold rules or subagent orchestration's outer retries.

| Config / class | Failure-number -> decision / delay (ms) | Total sends |
|---|---|---|
| Base default 3, ordinary retryable | 1 -> retry 1000; 2 -> retry 2000; 3 -> stop | 3 |
| maxRetries 4, ordinary retryable | 1 -> retry 1000; 2 -> retry 2000; 3 -> retry 4000; 4 -> stop | 4 |
| maxRetries 4 or 0, rate_limited | 1 -> 1000; 2 -> 2000; 3 -> 4000; 4 -> 8000; 5 -> stop | 5 |
| maxRetries 4 or 0, default overloaded | 1 -> 5000; 2 -> 10000; 3 -> 20000; 4 -> 40000; 5 -> 80000; 6 -> 150000; 7 -> stop (jitter draw pinned to 0) | 7 |
| maxRetries 4, overloaded {maxRetries:3, retryDelayMs:20, backoffMultiplier:2, maxRetryDelayMs:50} | 1 -> 10; 2 -> 20; 3 -> 25; 4 -> stop (jitter draw 0; larger base bound wins) | 4 |
| maxRetries 4, overloaded.maxRetries 0 | 1 -> 1000; 2 -> 2000; 3 -> 4000; 4 -> stop (base schedule, no jitter) | 4 |
| maxRetries 0, ordinary retryable | 1 -> stop | 1 initial send, no resends |
| final / retryable:false | 1 -> stop | 1 |
| deadline in Membrane retryDecision | 1 -> stop | 1 |
| stream deadline in framework policy, maxRetries 4 | 1 -> 1000; 2 -> 2000; 3 -> 4000; 4 -> stop | 4 |
| stream deadline in framework policy, maxRetries 0 | 1 -> stop | 1 |

All subsequent failure slots through 8 are asserted to stop. Real overloaded waits use equal jitter in `[floor(step/2), step)`, with nominal steps 10000, 20000, 40000, 80000, 160000, 300000 ms. The framework deliberately does NOT forward Membrane's deadline fact: its existing DefaultErrorPolicy treats a stream deadline as a retryable timeout. Tests explicitly pin both outcomes instead of asserting false equality; no framework behavior was changed. The constructor also raises stated-wait ceilings to its own provider-acceleration cap; this remains unchanged.

## Verification Gates

All gate names below were run through `orgasmic dispatch gate`. Evidence is in this run's `evidence/` directory, one `gate-<name>-<invocation>.log` per receipt. Full source documents are referenced by path, not pasted.

The test wrapper `/tmp/task-vnr9y-test-env` runs with `env -i`, an empty HOME/XDG config, only the pinned tools on its explicit PATH and update checks off. `/tmp/task-vnr9y-no-network` installs an inherited seccomp filter denying `socket`, `connect`, and `sendto` with EPERM; local files/in-process objects only. No real credentials, provider requests or external traffic were permitted. The probe stops at create(), before framework startup or any adapter method is invoked. `unshare -n` was unavailable, so seccomp was used instead. Wrapper SHA-256 `d2fa043edd9803d61ca9dd230e818e776328510038f0371701c5120ed8533d9f`; filter source SHA-256 `e9ba15fe09a8bac88fbd38ea2332688e0b762d981bb4471953064914fe38aae9`; compiled filter SHA-256 `7bff825064a24744bfd62a09765046936a01cbce971f41f645057d989b6a2d87`.

| Gate | Command (after the gate wrapper) | Result |
|---|---|---|
| environment | shell reporting HEAD, Bun/Node versions, binary/lock digests | Pass; expected base/tools/digests |
| acquisition | isolated frozen public-registry install described above | Pass |
| baseline-typecheck | `/tmp/task-vnr9y-test-env node node_modules/typescript/bin/tsc --noEmit` | Pass, base clean |
| baseline-suite | `/tmp/task-vnr9y-test-env bun-1.4.3 test` | Exit 1: **3280 pass / 413 fail / 6 errors**, 17415 assertions, 3693 tests / 234 files, 1346.63 s. Base source unmodified; checkout clean at gate start/end. |
| baseline-retry | `/tmp/task-vnr9y-test-env bun-1.4.3 test test/framework-error-policy.test.ts` | Exit 1: **14 pass / 11 fail**, 48 assertions, 25 tests. New tests on base production code. |
| retry | `/tmp/task-vnr9y-test-env bun-1.4.3 test test/framework-error-policy.test.ts test/membrane-retry.test.ts test/recipe-retry-and-mcp-timeout.test.ts` | Pass at clean implementation commit: **38 pass / 0 fail**, 84 assertions, 3 files, 3.95 s |
| typecheck | `/tmp/task-vnr9y-test-env node node_modules/typescript/bin/tsc --noEmit` | Pass at clean implementation commit |
| diff-check | `git diff 3e4cf9b39df97f00f9e62e09571201966142c0f3 --check` | Pass at clean implementation commit |
| suite | `/tmp/task-vnr9y-test-env bun-1.4.3 test` | Exit 1 at clean implementation commit: **3304 pass / 414 fail / 7 errors**, 17473 assertions, 3718 tests / 235 files, 1352.76 s. All 25 added tests pass in the full suite. |

There is no root typecheck script in `package.json`; used the repository's documented/CI host command `tsc --noEmit` (`CONTRIBUTING.md:229`, `.github/workflows/ci.yml:118`) through the installed compiler directly, avoiding any network-capable package exec. Web sources were not changed or typechecked. Initial baseline-suite/typecheck launch receipts exited 127 because the isolated PATH lacked the tool links; corrected before either actual baseline ran. Earlier dirty narrow/typecheck gates also passed; the final clean-commit receipts above supersede them. No hooks were bypassed. Additional investigation gates: `baseline-fleet-race` ran the single fleet case ten times against a `git archive` of the exact base, sharing the same installed node_modules (no acquisition/tree change), all pass; `fleet-race` runs it on the final commit, pass. The archive probe's gate receipt stands in the implementation worktree, so it is supplemental evidence, not a reusable baseline checkout receipt. Test sessions were owned by recorded PIDs in `/tmp/task-vnr9y-baseline-owner.pid` and `/tmp/task-vnr9y-final-owner.pid`; logs were durable. After each full suite ended, two fixture interval processes were found to have outlived the runner; their recorded provenance and exact command lines were checked before SIGTERM of only those PIDs (2776216, 2776217; 2849174, 2849175). No test runner was interrupted or duplicated.

Key receipt logs (relative to this run's `evidence/`):
- `gate-environment-01M5XJ42Z03KZPZ6DJCT8XQE38.log`
- `gate-acquisition-01M5XHTRGQAG731DJMHE024YVX.log`
- `gate-baseline-typecheck-01M5XHVVM1RGXCKG1XJ4FJWWS5.log`
- `gate-baseline-suite-01M5XHVVW1GQ61NQWCAGQG2D1B.log`
- `gate-baseline-retry-01M5XK8TSQC8TKN38PB7G7N2RP.log`
- `gate-typecheck-01M5XKBKVC078E6TFB8BYDAJP7.log`
- `gate-retry-01M5XKBM7DV2HAX7J1MNWC2RR9.log`
- `gate-diff-check-01M5XKBR6RXJ35QGFD34GZ35QN.log`
- `gate-suite-01M5XKBJPVSGXNJHGDHCJ4ACTP.log`
- `gate-baseline-fleet-race-01M5XMQ2A00J80FACAP4W8RNSX.log`
- `gate-fleet-race-01M5XMQ8E6CMG95KJJ3ER5VMPK.log`
- `gate-baseline-fleet-file-01M5XMQTRD9HGWKP7DZ81TS0N1.log`

### Baseline outcome of every added test

Every test below is in `test/framework-error-policy.test.ts`; all 25 pass on the implementation. The 14 green-on-base cases are controls, and all 11 genuinely new cases fail naturally because the captured base create() argument has no policy. No missing helper imports or artificial assertions were used to create red results. The probe is subprocess-isolated and captures the actual `src/index.ts` call rather than reconstructing it in a test.

- **PASS (control):** `control: a silent recipe omits errorPolicy entirely`.
- **PASS (control):** `control: Membrane-side settings unchanged for undefined`.
- **PASS (control):** `control: Membrane-side settings unchanged for {"maxRetries":4}`.
- **PASS (control):** `control: Membrane-side settings unchanged for {"overloaded":{"maxRetries":9,"retryDelayMs":123}}`.
- **PASS (control):** `control: an empty object counts as unset, omitting errorPolicy`.
- **FAIL -> PASS (new behavior):** `turn settings match Membrane completion for {"maxRetries":4}`.
- **FAIL -> PASS (new behavior):** `turn settings match Membrane completion for {"maxRetries":0}`.
- **FAIL -> PASS (new behavior):** `turn settings match Membrane completion for {"retryDelayMs":23,"backoffMultiplier":1.5,"maxRetryDelayMs":40}`.
- **FAIL -> PASS (new behavior):** `turn settings match Membrane completion for {"overloaded":{}}`.
- **FAIL -> PASS (new behavior):** `turn settings match Membrane completion for {"maxRetries":9,"overloaded":{"maxRetries":2}}`.
- **FAIL -> PASS (new behavior):** `turn settings match Membrane completion for {"overloaded":{"maxRetries":0}}`.
- **FAIL -> PASS (new behavior):** `turn settings match Membrane completion for {"overloaded":{"maxRetries":4,"retryDelayMs":80,"backoffMultiplier":3,"maxRetryDelayMs":100}}`.
- **FAIL -> PASS (new behavior):** `maxRetries:4 has explicit failure-number decisions for every error class`.
- **FAIL -> PASS (new behavior):** `explicit overloaded values replace the default turn schedule`.
- **FAIL -> PASS (new behavior):** `overloaded maxRetries:0 falls back to the base turn schedule without jitter`.
- **FAIL -> PASS (new behavior):** `maxRetries:0 stops ordinary retries but retains rate-limit and overloaded floors`.
- **PASS (control):** `control: existing recipe validation rejects null`.
- **PASS (control):** `control: existing recipe validation rejects []`.
- **PASS (control):** `control: existing recipe validation rejects {"maxRetires":4}`.
- **PASS (control):** `control: existing recipe validation rejects {"maxRetries":-1}`.
- **PASS (control):** `control: existing recipe validation rejects {"maxRetries":null}`.
- **PASS (control):** `control: existing recipe validation rejects {"retryDelayMs":"100"}`.
- **PASS (control):** `control: existing recipe validation rejects {"overloaded":null}`.
- **PASS (control):** `control: existing recipe validation rejects {"overloaded":{"typo":1}}`.
- **PASS (control):** `control: existing recipe validation rejects {"overloaded":{"maxRetries":-1}}`.

The JSON label for the Infinity validation case prints `null`; its source input is `Infinity`.

### Existing full-suite failures under the required harness

These are failures of existing tests on the base in this restricted environment, not claimed to be upstream failures in an unrestricted development setup. Socket-based suites necessarily fail with EPERM/timeouts. Skipping lifecycle scripts also leaves Web UI dependencies uninstalled (`solid-js` is missing), and the isolated tool PATH does not expose npm, affecting packaging/release tests. These pre-existing restricted-harness failures were not repaired outside scope. Final comparison: all 411 named base failures remain, with one additional named timeout in `test/fleet-children-file.test.ts`: “the fleet's children file > a child is in the record, synced, and then in the file, from the moment its process exists, before it is ready” (`:264-283`). It timed out at 5000 ms, then reported an unhandled assertion at `:272` (expected starting, received crashed). This accounts for the one extra failure/error and one fewer pass than base+25. A focused final-commit rerun passes (1 pass / 44 filtered / 0 fail, 210 ms), as do ten isolated samples of the unchanged base test (194–210 ms scale). The test and its production/fixture dependencies (`src/modules/fleet-module.ts`, `src/fleet-begin.ts`, `test/mock-fleet-lifecycle-child.ts`) have no changes in this diff. Classified as a likely suite-order/environment timing flake, not a proven pre-existing failure; its full-suite-only drift is reported rather than hidden. `baseline-fleet-file` also ran the unchanged whole file from the base archive: 12 pass / 33 fail / 1 error, 45 tests, 94 assertions, 27.33 s; the disputed case passed there too. Thus the extra full-suite timing failure is not reproduced on the base and remains a qualification concern.

Runner totals include 411 named failing test entries plus two module-load failures (`test/web-wire.test.ts` and `test/web-health-without-ops.test.ts`, missing `solid-js`). Six unhandled-error reports comprise those two load errors, a missing fleet launch-artifact file and three update-audit assertion errors. Raw logs are authoritative for totals.

Named base failures (grouped by file):

#### `test/fleet-orchestration.test.ts`

- lifecycle:idle + inference:speech from mock child > (unnamed)
- fleet--relay > (unnamed)
- fleet--await > returns immediately when all named children are already idle
- fleet--await > blocks until all named children emit idle
- fleet--await > returns on any idle when requireAll is false
- fleet--await > times out returning partial set
- fleet--await > fails fast when waited-for child crashes

#### `test/hand-written-json-bom.test.ts`

- a recipe or credentials file that isn't JSON > fetched by URL, is refused in words that name the URL and never repeat the parser's message

#### `test/headless-self-restart.test.ts`

- an armed self-restart exits 75 once the turn has ended, and records the exit
- another shutdown that comes first exits as it would, and the request records it
- under --exit-when-idle, an armed self-restart still exits 75
- a turn the framework still counts alive holds the restart back, though every agent reads idle
- a gated wake holds the restart back, though every agent reads idle
- a running background script holds the restart back, though every agent reads idle
- an event not yet taken off the queue holds the restart back, though every agent reads idle
- a subagent run holding a slot holds the restart back, though every agent reads idle
- a queued subagent run holds the restart back, though every agent reads idle
- a subagent's end, with its wake a loop pass behind, holds the restart back, though every agent reads idle
- under --exit-when-idle, a restart cancelled while it waits gives the exit back: the host exits 0
- a restart armed after the host has gone quiet still goes
- a restart armed while already quiet waits out its own confirm, so work that starts just after holds it
- a restart armed in a turn the host never saw start still goes

#### `test/fleet-lifecycle.test.ts`

- FleetModule lifecycle > manual restart preserves trusted env and autoRestart=true
- FleetModule lifecycle > manual restart preserves trusted env and autoRestart=false
- FleetModule lifecycle > manual restart of an adopted child confirms its death and starts a replacement
- FleetModule lifecycle > a child's exit ends the helpers it left in its process group
- FleetModule lifecycle > stopping an adopted child ends the helpers it left in its process group
- FleetModule lifecycle > an adopted child whose connection has closed, though its process lives, has its group signalled neither by its stop nor by the host's exit
- FleetModule lifecycle > stopping an adopted child whose process had already ended signals nothing of its group, and says a process of it is left
- FleetModule lifecycle > a host's exit ends an adopted child's process group while its connection to the child is open
- FleetModule lifecycle > the host's entry, started as a fleet's child told to wait, does nothing in its data directory until told, and ends when its stdin does
- FleetModule lifecycle > real headless relaunch survives the stale socket left after ready failure
- FleetModule lifecycle > real headless relaunch survives the stale socket left after subscribe failure
- FleetModule lifecycle > a natural nonzero exit reported during launch cleanup keeps its automatic retry
- FleetModule lifecycle > ready timeout returns only after the owned child and connection are stopped
- FleetModule lifecycle > subscribe-send failure terminates a child even if ready already arrived
- FleetModule lifecycle > unconfirmed termination keeps the child blocked and reports the cleanup failure
- FleetModule lifecycle > real replacements retain attempt history, advance backoff, and stop at the cap
- FleetModule lifecycle > expired history starts a new first attempt
- FleetModule lifecycle > kill after the retry callback starts cancels across recipe-loading await
- FleetModule lifecycle > kill during asynchronous ownership reconciliation cancels automatic replacement
- FleetModule lifecycle > stop during asynchronous ownership reconciliation cancels automatic replacement
- FleetModule lifecycle > kill during an automatic retry's adoption cancels it and closes the adopted connection
- FleetModule lifecycle > stop during an automatic retry's adoption cancels it and closes the adopted connection
- FleetModule lifecycle > kill during backoff cancels stale automatic replacement
- FleetModule lifecycle > restart during backoff cancels stale automatic replacement
- FleetModule lifecycle > stop during backoff cancels stale automatic replacement
- FleetModule lifecycle > replace during backoff cancels stale automatic replacement
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > stop during a manual launch's await refuses it, and nothing is spawned
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a kill acknowledged during a restart's relaunch stops the replacement
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a kill acknowledged during a restart's relaunch stops the replacement after the old generation left its pid file
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a manual launch cancels a pending automatic replacement before its awaits
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > kill clears the pending automatic replacement on the record
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > restart clears the pending automatic replacement on the record
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > stop clears the pending automatic replacement on the record
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > an automatic retry yields to a manual launch that replaced its generation meanwhile
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a manual launch wins over an automatic launch already in its awaits
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a child that exits after a refused connect is reported with the refusal
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a kill acknowledged during a restart's own kill wait refuses the relaunch
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a manual launch refused after taking over says automatic restarts stopped
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > an automatic replacement refused before spawning records why restarts stopped
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a kill that fails is not counted against a launch
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > an adoption refused by kill stops the child it confirmed
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > an adoption refused by stop stops the child it confirmed
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > an adoption refused by detach leaves the child it confirmed
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > an adoption refused by a kill whose stop is unconfirmed says the process may still be running
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a kill during backoff records that it ended automatic restarts
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a signal exit from outside records that it is not retried
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > an automatic replacement that throws records why restarts stopped
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a detaching stop during a launch's ready wait leaves the child running, told to begin where its host keeps the children file
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a detaching stop during a launch whose child then exits says it has exited
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a stop during a launch's ready wait says there is nothing to retry
- FleetModule lifecycle: every launch answers to stop, kill and newer launches > a launch while another adopts the child is refused

#### `test/web-ui-bind.test.ts`

- WebUiModule bind safety > loopback bind without basicAuth is allowed (local dev)
- WebUiModule bind safety > non-loopback bind WITH basicAuth is allowed

#### `test/logging-adapter.test.ts`

- LoggingAnthropicAdapter initial recipe effort > first stream builds medium effort on the wire without a settings update
- LoggingAnthropicAdapter cache lineage > a stream call's row and its live ledger record name the wire request's cache lineage

#### `test/web-ui-welcome-snapshot.test.ts`

- (unnamed)

#### `test/workspace-viewers-read-result.test.ts`

- a fleet child's file viewer > answers request-workspace-file with the page when the read's fields are in data
- a fleet child's file viewer > answers request-workspace-file with the page when the read's fields are in structured

#### `test/placed-reaction-composition.test.ts`

- host and AF compose the child reaction baseline: undefined
- host and AF compose the child reaction baseline: "🟪"
- host and AF compose the child reaction baseline: ""
- host and AF compose the child reaction baseline: " "
- host and AF compose the child reaction baseline: ","
- host and AF compose the child reaction baseline: ":"
- host and AF compose the child reaction baseline: null
- host and AF compose the child reaction baseline: 0
- host and AF compose the child reaction baseline: false

#### `test/fleet-env-state.test.ts`

- Fleet child environment stays outside Chronicle state > live adoption keeps the PID, refreshes runtime env, and persists no env values

#### `test/fleet-subscribe-union-e2e.test.ts`

- FleetModule subscribe union e2e > narrow recipe subscription gets reducer-required events forced in by FleetModule

#### `test/fleet-children-file.test.ts`

- the fleet's children file > lists each child with its data directory, and the process of each that may be running, with the machine's boot
- the fleet's children file > a child's boot is null where the machine gives none
- the fleet's children file > a fleet started on a record taken while a child starts keeps the child, lists it, and starts no second copy
- the fleet's children file > a fleet adopting its living children writes its record and the file once, holding them all
- the fleet's children file > a write keeps what it doesn't replace: another directory's entry, with its pid only while it may run, another generation still running in its own child's directory, and an entry it can't read as a child, on a machine that gives a boot id and on one that gives none
- the fleet's children file > the pid a child the fleet has seen exit had can't stand for a live process that holds it now
- the fleet's children file > a child that ended while no host ran stays listed while a helper remains in its group, which isn't signalled, and its pid goes once the group has ended
- the fleet's children file > an ended child whose record doesn't say its group has ended, as an earlier version's doesn't, is retired once a live process holds its pid, whatever group that process leads
- the fleet's children file > an earlier entry in a listed child's own directory whose process has ended is kept while the group it led has a member, and dropped once that group is empty
- the fleet's children file > a child whose group the fleet ended stays unlisted once its host restarts, though a group holds its pid's id now, its leader live or ended
- the fleet's children file > a child of this fleet's from an earlier boot isn't listed by a pid a live group holds now, told by its boot id or by its start against the machine's uptime
- the fleet's children file > a record whose group has ended isn't adopted again, though a live process holds its pid now
- the fleet's children file > a group the fleet may not signal, or probe, stays listed, and the host says why
- the fleet's children file > a child's entry keeps its pid while what it left in its group is still there, though the group was signalled, and loses it at the fleet's own write once that has gone, with no other event
- the fleet's children file > a child whose group is found empty at its exit's write is retired then, and the fleet arms no later write for it
- the fleet's children file > the fleet's own write after a child's exit, should the store refuse it, is logged, not thrown
- the fleet's children file > a stop returns once the group it signalled is found empty, listing no pid then, a stop of a child that has just exited and the host's stop included
- the fleet's children file > a stop whose group outlasts its wait returns after it, keeps its pid, says so, and a later write retires it
- the fleet's children file > a stop whose write of its group's end fails says so, and goes on
- the fleet's children file > a failed launch whose child left a process in its group says so, and that the group was sent SIGKILL
- the fleet's children file > at the write that follows a child's exit, what it left in its group has been signalled already
- the fleet's children file > a host's exit ends each running child's process group, not only the child
- the fleet's children file > another writer's entry whose process has ended stays listed while the group it led has a member
- the fleet's children file > a fleet that can't read its record keeps the file's children, rather than writing none
- the fleet's children file > a fleet that keeps the file tells its child to begin only once the file lists it
- the fleet's children file > a child that's never told waits, doing nothing in its data directory, and ends when its stdin does, a line without its end included
- the fleet's children file > a child whose line comes in pieces begins at its newline, with the begin variable gone from its env
- the fleet's children file > a told child goes on once its stdin has ended
- the fleet's children file > a fleet whose child's end of the pipe is gone before its line fails that launch, and goes on
- the fleet's children file > a launch while the fleet's record isn't written, as while it adopts, still lists its child before telling it to begin
- the fleet's children file > a later write that fails is logged, the lifecycle goes on, and the file keeps the child's pid
- the fleet's children file > a fleet the host gave no children file writes and reads none
- a fleet child and the install's root > a child never gets CONNECTOME_INSTALL_ROOT, from the parent or from its own env

#### `test/web-ui-files-route.test.ts`

- the web UI's /files/ route serves only what the mount's own read gives > (unnamed)

#### `test/headless-smoke.test.ts`

- headless daemon — Phase 1 > (unnamed)

#### `test/postinstall-web-build.test.ts`

- postinstall Web UI build > a working build reports nothing

#### `test/bun-floor.test.ts`

- a TUI start > under a Bun below the floor: the warning once as the check loads, and in the TUI's view below the history
- a TUI start > under a Bun at the floor: no warning, and nothing added after the history

#### `test/tui-shutdown.test.ts`

- TUI ctrl-c shutdown healthy settles after terminal cleanup
- TUI ctrl-c shutdown failure settles after terminal cleanup
- TUI ctrl-c shutdown delayed-failure settles after terminal cleanup
- TUI quit shutdown healthy settles after terminal cleanup
- TUI quit shutdown failure settles after terminal cleanup
- TUI quit shutdown delayed-failure settles after terminal cleanup
- TUI SIGTERM shutdown healthy settles after terminal cleanup
- TUI SIGTERM shutdown failure settles after terminal cleanup
- TUI SIGTERM shutdown delayed-failure settles after terminal cleanup
- TUI SIGINT shutdown healthy settles after terminal cleanup
- TUI SIGINT shutdown failure settles after terminal cleanup
- TUI SIGINT shutdown delayed-failure settles after terminal cleanup
- TUI SIGHUP shutdown healthy settles after terminal cleanup
- TUI SIGHUP shutdown failure settles after terminal cleanup
- TUI SIGHUP shutdown delayed-failure settles after terminal cleanup
- TUI stdout-error stops the framework once, and the log names the first error
- TUI stdin-error stops the framework once, and the log names the first error
- TUI two-errors stops the framework once, and the log names the first error
- TUI title-throws logs the failed step, and still stops the framework once
- TUI detach-throws logs the failed step, and still stops the framework once
- a terminal error once the stop has settled, after the log has ended, is swallowed and writes nothing
- the TUI settles only once its log has its last lines, so a host that exits at once after a failed stop keeps them
- a terminal error that comes once a signal has started the stop gets its line too
- a second stop signal, SIGTERM, while the framework stops joins the stop
- a second stop signal, SIGINT, while the framework stops joins the stop
- a second stop signal, SIGHUP, while the framework stops joins the stop
- a command whose work answers once the stop has begun draws nothing, and nothing throws
- once a clean stop has settled, a stop signal ends a host that something else keeps running
- once a failed stop has settled, a stop signal ends a host that something else keeps running
- a stop during a session switch doesn't wait for it, and the switch stops what it made

#### `test/web-ui-module.test.ts`

- (unnamed)
- (unnamed)

#### `test/web-ui-observer-frames.test.ts`

- what each client is sent > (unnamed)
- what each client is sent > (unnamed)

#### `test/logging-adapter-anomaly.test.ts`

- llm-calls rows for anomalous responses > stream: a tool_use under end_turn keeps the request, names the anomaly, and prints one stamped line
- llm-calls rows for anomalous responses > stream: a provider that reports no stop reason reads as provider: none
- llm-calls rows for anomalous responses > a missing stop reason records the same stopReason on complete() and stream()
- llm-calls rows for anomalous responses > complete: same, and the row keeps the message id and the request id
- llm-calls rows for anomalous responses > normal rows are unchanged: tool_use under tool_use, and text under end_turn, keep no request and name no anomaly
- llm-calls rows for anomalous responses > a refusal keeps its request as before, and is not an anomaly without a tool call
- an anomalous row the log could not keep whole > a failed write: the alert says the request couldn't be saved, not that it was kept
- an anomalous row the log could not keep whole > over the size guard: the request is dropped and said so; the anomaly and ids stay on the line
- failed calls > a failed call's row keeps the provider's request id from its error

#### `test/fleet-error-wording.test.ts`

- fleet refusals name what the agent can do > an unknown child name is answered with the names this host has
- fleet refusals name what the agent can do > a child that has stopped is named as stopped, with how it stopped and how to start it again
- fleet refusals name what the agent can do > a child this host has no connection to is described by its state
- fleet refusals name what the agent can do > an await on a child that has stopped, before the wait or during it, says so, and which children are idle
- fleet refusals name what the agent can do > a relay from a child whose subscription leaves out inference:speech says so
- fleet refusals name what the agent can do > a relay from a child started with the recipe's env points to the recipe, not to a relaunch
- fleet refusals name what the agent can do > a relay from a child with no finished reply says what would bring one
- fleet refusals name what the agent can do > a launch into a ready child's data directory is refused
- fleet refusals name what the agent can do > a restored record whose process has since gone no longer holds its data directory
- fleet refusals name what the agent can do > a record that has exited, or that has no usable pid, holds neither its name nor its directory
- fleet refusals name what the agent can do > a record from an earlier boot holds neither its name nor its directory
- fleet refusals name what the agent can do > a failed reattach to a child that has gone, and a host with no boot id, read as no process and the clock
- fleet refusals name what the agent can do > a launch of a restored child that may still be starting is refused while its pid is in use
- fleet refusals name what the agent can do > a launch refused for another child's data directory doesn't connect to that child
- fleet refusals name what the agent can do > files another child left in its data directory name that child

#### `test/headless-socket-stream.test.ts`

- production headless receiver preserves Unicode commands at every byte boundary

#### `test/headless-describe.test.ts`

- headless daemon — describe / snapshot > (unnamed)

#### `test/ipc-connect.test.ts`

- a connect refused, then failing on a path not there yet, is tried again until the server accepts
- a connect still refused at its deadline fails with the last error, trying again after a pause
- with no deadline given, a connect still refused fails after about 3 s
- the socket it returns has the allowHalfOpen it was given, and ignores a later error

#### `test/update-data-swap.test.ts`

- copyTree > copies files, directories and links as they are, with their modes, and leaves out the update directory at any depth and anything else

#### `test/fleet-panel-request.test.ts`

- FleetModule.requestPanel > (unnamed)
- the children a switch's quiesce reaches > are those that may be running: one that has exited isn't asked, and one whose connection has closed is, and says why it can't answer

#### `test/update-quiesce-e2e.test.ts`

- the switch's quiesce of a real fleet child > waits on its turn, says who holds it, persists until the resume, and leaves the child serving

#### `test/fleet-command-output.test.ts`

- command replies survive fleet narrowing and an empty direct-client subscription

#### `test/tui-turns.test.ts`

- the TUI's Escape, through runTui > in each state of the root turn: what it asks the framework, the line it shows, and the status bar
- the TUI's status line, through runTui > a turn whose start the TUI didn't follow shows its own end, and its failure
- the TUI's session switch, through runTui > a switch: Escape does nothing while it's under way, and the status line follows the framework the host holds once it ends
- the TUI's session switch, through runTui > a failed switch: Escape does nothing while it's under way, and the status line follows the framework the host holds once it ends
- the TUI's Escape with a subagent running, through runTui > takes the subagent's turn to the follower before its run is stopped, and aborts it once its call goes out
- the TUI's subagent exhaustion, through runTui > shows lone failures, deduplicates paired ends and tracks the failed phase

#### `test/fleet-child-socket-reset.test.ts`

- a fleet parent lets go of a child's socket that resets

#### `test/batch-mode.test.ts`

- batch mode end to end > announces the MCPL teardown and exits once the input is done
- batch mode end to end > a /auth token line is echoed without its credential
- batch mode end to end > after a session switch, a line waits for the new session's turn rather than the cap
- batch mode end to end > says the webui stays up with the agent stopped

#### `test/release-bump.test.ts`

- this repository's preversion stops npm version patch, prepatch and prerelease before anything is written
- this repository's preversion lets npm version minor, and preminor, take a breaking change

#### `test/pack-installs-alone.test.ts`

- a package that carries its Web UI, installs alone, resolves and gives its queued reply with the Web UI off passes, naming the npm
- npm pack's JSON is read in npm 12's shape too, and a prepack's output doesn't break it
- a package whose files leave the Web UI out fails: npm 12 and bun installs would have none
- a package that carries the Web UI's index.html without the files it names fails, naming them
- a package whose files leave tsconfig.json out fails: a consumer typechecks the source with it
- the install runs no install scripts, the package's own included
- a dependency that can't be installed fails the install after three attempts, saying what does that
- a file left out of the package fails the resolve, naming the entry
- a dependency only devDependencies supplied fails the resolve
- a smoke recipe left out of the package fails, naming it
- a smoke run that exits non-zero fails, with its exit code
- a smoke run whose queued reply doesn't follow the message it took fails: none, one before it, another text, or no '> line'
- a working tree that differs from HEAD gets a warning, and is checked as it stands
- @opentui/core must load in the installed tree, its native package with it
- a smoke run that doesn't finish is stopped at its limit, even one that ignores SIGTERM, and the limit must be a number
- an install that fails once and then succeeds passes on its second attempt
- an install that runs out of time, even one that ignores SIGTERM, fails at once, saying so, without another attempt
- npm pack that runs out of the install's time, even ignoring SIGTERM, fails, saying so
- npm pack that fails, or prints anything but one record with a filename and its files, fails by name

#### `test/update-audit-stores.test.ts`

- the store audit > a fleet child's load repair reaches its parent's audit, which says it and hands it on as its own
- the store audit > a directory's fleet children are audited as the fleet launches them, under the recipe each entry names and in the environment the fleet starts it with, and a directory no entry names is said to be read in this one's
- the store audit > through the host's entry, with no provider credential: every line on stderr and the exit status, even when an extension keeps a timer
- the store audit > through the host's entry, a directory whose stores open exits 0, and none given exits 1 with the usage line
- the store audit > through the host's entry, a line reaches stderr whole, though it's longer than a pipe holds and its reader starts late
- a fleet child's audit, in a process of its own > runs with the environment the fleet gives the child, and its exit status, a signal included, decides whether it failed
- a fleet child's audit, in a process of its own > what a child's audit prints on stdout reaches no one, and its lines still come on stderr
- a fleet child's audit, in a process of its own > a child's audit that doesn't end within its limit is ended, with its group, and holds the commit
- a fleet child's audit, in a process of its own > a child's audit ended after it said it had finished, by a signal or a status other than the one it said, a refusal's made 0 included, gives no answer to take
- a fleet child's audit, in a process of its own > a line that only resembles the finished line, prefixed or with a status no audit gives, doesn't change a child's answer
- a fleet child's audit, in a process of its own > a child's audit that finishes with 1 and says of no store why isn't taken as clean
- a fleet child's audit, in a process of its own > ends once the child audit has exited, though a helper it started still holds its stderr
- a fleet child's audit and what outlasts it > a child's audit that writes after its finished line, as an extension's exit can, is still an answer
- a fleet child's audit and what outlasts it > on a trial, a child's audit whose group outlasts its SIGKILL stays listed, and the host says how to end it
- a fleet child's audit and what outlasts it > a child's audit that said it finished but didn't end within its limit had run to its end, and its line says so
- a fleet child's audit and what outlasts it > a child's audit has the minutes the audit's environment gives it, and 5 where it gives none it can use
- a fleet child's audit and what outlasts it > a child's audit past its limit whose SIGKILL ends nothing is settled without it, and stays listed
- which fleet children an audit follows, and how > a child's own fleet entries aren't followed, so no audit reaches past one level, and a cycle ends
- which fleet children an audit follows, and how > each entry naming a directory is audited under its own recipe, and a directory given that an entry names is that child, whatever its own recipe declares
- which fleet children an audit follows, and how > a parent whose recipe names its own directory as a child is audited as itself, and its other children are followed
- which fleet children an audit follows, and how > a child's recipe reads the audit's environment as it began, the compression log's variable included
- which fleet children an audit follows, and how > a child's opens line isn't a failure whatever its path holds, though another of its stores was refused
- which fleet children an audit follows, and how > a fleet child's audit ends when the audit that started it does, since its stdin closes
- which fleet children an audit follows, and how > on a trial, a child's audit is listed in the children file while it runs, and taken out after, the file's other entries kept
- which fleet children an audit follows, and how > on a trial, a child's audit is listed with no boot id where the machine gives none, and goes on
- which fleet children an audit follows, and how > on a trial, a child's audit stays listed while it runs on after its host ends mid-open, and ends once that open does
- which fleet children an audit follows, and how > a child's audit whose host ended mid-open ends its process group as it ends, so a helper its extension started there writes nothing
- which fleet children an audit follows, and how > a fleet child's audit does nothing until the audit that started it says to begin, and ends if that one ends first
- which fleet children an audit follows, and how > on a trial, a child's audit holds its stores to the trial's places, not its own directory alone
- which fleet children an audit follows, and how > on a trial, a child's audit offers pointing its directory at the other disk only where the trial took that directory as a place of its own
- which fleet children an audit follows, and how > a child's audit that exits 0 before it finished, as an extension's import can make it, can't be audited
- which fleet children an audit follows, and how > a fleet child's audit opens no store once the audit that started it has ended, though its event loop was held up
- which fleet children an audit follows, and how > ends once the child's audit has exited, though a helper outside its group still holds its stderr
- a host on trial, before its commit > audits every store it would open only after its commit under a place its fallback undoes, leaves its active session to its own start, and a store it can't open ends the start with `current` as it was
- a host on trial, before its commit > a child's audit that exits 1 before it said it had finished holds the commit, though the only line it left wouldn't
- a host on trial, before its commit > lists each child's audit in the host's children file while it runs, and takes it out after

#### `test/update-commit-e2e.test.ts`

- a host started from an install > commits its release on a real start, and lists its fleet child in the children file until it stops it
- a host started from an install > a trial whose MCPL server connects commits, so a working server reads as connected when the commit runs
- a host started from an install > a host started on its own keeps no children file, though its fleet starts a child
- a host the switch quiesced before its stop > boots quiesced, commits, and ends the switch's quiesce once framework.start() has run, in its fleet child too
- a host the switch quiesced before its stop > an operator's quiesce stays through the switch's start, in its fleet child too
- a host's update, on a real start > reads the registry at start, logs a newer release once with its cost, and /update says the same over its socket
- a host's update, on a real start > /update restart quiesces a headless host, writes the mark and exits 75, for the next start to switch
- a host's update, on a real start > a Restart the Web UI sends, naming its release, brings the page the farewell, which the host sent before it exited 75
- a host's update, on a real start > /update restart in readline mode, with the Web UI up, stops the process and leaves the mark (Linux only)
- a slash command in batch mode > prints a follow-up before the next line and before the batch stops, and reads no registry on its own

#### `test/fleet-autostart-host.test.ts`

- a host with autoStart children > launches them at its start, and again after a session switch

#### `test/lessons-stored-shape.test.ts`

- readers in other modules > the web viewer's lessons list sends [] for a lesson stored without tags, and 1 for one stored at 85

#### `test/tts-relay-turn-end.test.ts`

- a turn a tool ended > ends its activation as complete, and the next turn opens its own
- a turn a tool ended > another agent's turn a tool ended leaves this agent's activation open
- a turn a tool ended > a turn that sends both ends, in either order, ends its activation once

#### `test/shipped-recipes.test.ts`

- webui-test.json starts an unauthenticated loopback WebUI
- webui-fleet-test.json starts an unauthenticated loopback WebUI

#### `test/fleet-launch-output.test.ts`

- a failed launch quotes the child's own output > a launch that fails after its child was ready quotes only what its child wrote before it exited
- a failed launch quotes the child's own output > an exit after a launch that succeeded reads nothing
- a failed launch quotes the child's own output > a child stopped for a ready timeout has what it wrote quoted
- a failed launch quotes the child's own output > a launch that runs without the file says so in the host's log
- a failed launch quotes the child's own output > an unconfirmed termination quotes the output too
- a launch whose spawn starts no process > a runtime that isn't there ends automatic restarts, saying whose it is to fix, and fleet--restart starts it once it is
- a launch whose spawn starts no process > a name, recipe or data directory holding a NUL byte is refused before anything starts, naming the agent's field
- a launch whose spawn starts no process > an error reported for a running child is logged and leaves it running

#### `test/web-ui-bundle-missing.test.ts`

- WebUiModule with a missing bundle > warns once at start, naming the bundle path and the remedy; the page still answers 503 with it
- WebUiModule with a missing bundle > says nothing when the bundle is there

#### `test/logging-keepalive.test.ts`

- keepalive calls reach the normal logs and spend ledger > a real request seeds a timer poke with exact payload and cache-read usage
- keepalive calls reach the normal logs and spend ledger > ineffective writes retain split cache buckets and contribute their actual cost
- keepalive calls reach the normal logs and spend ledger > failed pokes emit an error row without inventing usage or cost
- keepalive calls reach the normal logs and spend ledger > a failed poke's error is bounded as a complete or stream call's is, in the row and the ledger alike
- keepalive calls reach the normal logs and spend ledger > a refused poke's row records the refusal and leaves the request out, and no alert fires
- keepalive calls reach the normal logs and spend ledger > with full payloads on, every poke's row keeps its request, failed and refused ones too
- keepalive calls reach the normal logs and spend ledger > a poke whose credential is rejected reaches the credential monitor as an auth failure
- keepalive calls reach the normal logs and spend ledger > a poke whose row can't be written still reaches the ledger and the credential monitor
- keepalive calls reach the normal logs and spend ledger > caller mutation and observer failures cannot change logged usage, the ledger or the next poke
- keepalive calls reach the normal logs and spend ledger > with full payloads on, a row's request is the bytes sent, whatever the caller's observer does to its copy
- keepalive calls reach the normal logs and spend ledger > the caller's onCall runs as membrane's method, with its config as this
- keepalive calls reach the normal logs and spend ledger > caller async rejection remains isolated from the keepalive loop

#### `test/headless-command-owner.test.ts`

- pending command result belongs to its caller: same-client
- pending command result belongs to its caller: replacement
- pending command result belongs to its caller: disconnect-and-reconnect

#### `test/retrieval-auth-loopback.test.ts`

- retrieval operator authentication on credential-free loopback > (unnamed)

#### `test/fleet-tree-aggregator-e2e.test.ts`

- FleetTreeAggregator — e2e against headless child > snapshot round-trips through aggregator after launch

#### `test/mock-provider.test.ts`

- the host builds its mock from agent.mock > a queued reply comes back from a batch run

#### `test/web-ui-observers.test.ts`

- WebUiModule observer flow (e2e) > (unnamed)

#### `test/tui-host-exit.test.ts`

- a TUI host with the Web UI ends with code 0 after /quit
- a TUI host with the Web UI ends with code 0 after SIGTERM
- a TUI host with the Web UI ends with code 0 after SIGINT
- a TUI host with the Web UI ends with code 0 after SIGHUP
- a TUI host with the Web UI ends with code 0 after its terminal going away

#### `test/headless-fleet-shutdown.test.ts`

- real headless Fleet shutdown caller-deadline preserves child ownership
- real headless Fleet shutdown detach preserves child ownership

#### `test/fleet-socket-stream.test.ts`

- Fleet preserves Unicode JSONL at every byte boundary
- Fleet reconnect starts a fresh line and Unicode stream
- Fleet ignores data from a superseded socket

#### `test/release-holds.test.ts`

- bun's version and publish commands run this repository's hooks, so a hold stops them too
- this repository's preversion stops npm version before the bump while a hold is open
- with no hold open, this repository's preversion lets npm version bump
- this repository's prepublishOnly stops npm publish while a hold is open
- with no hold open, this repository's prepublishOnly lets npm publish go ahead
- this repository's own package.json sends the check to anima-research/connectome-host

#### `test/headless-panel-request.test.ts`

- headless daemon — panel-request / panel-response > (unnamed)

#### `test/update-trial-log.test.ts`

- a release on trial > that ends before its commit keeps what it wrote, which still reaches its stdout and stderr, readable by this user alone
- a release on trial > keeps each of console's five methods it wraps, a subarray's own bytes, and passes a write's encoding and callback on
- a release on trial > keeps nothing once it commits, and still passes what it writes on, formatting and decoding nothing

#### `test/headless-current-client.test.ts`

- a client that ends cleanly stops being current, and its disconnect is logged once
- a superseded client's disconnect leaves the newer client current
- a superseded client's later commands are dropped, not run
- a client whose socket is reset stops being current, and its disconnect is logged once

#### `test/evacuator-agent-name.test.ts`

- evacuator participant naming > native startup then imported-session switch keeps Claude and its Membrane role anchor
- evacuator participant naming > native startup then imported-session switch keeps Custom Import and its Membrane role anchor

#### `test/update-install.test.ts`

- the machine's package manager > runs the Bun running this host in the directory given, and gives its exit status and the end of both streams

#### `test/headless-shutdown.test.ts`

- headless shutdown healthy reports exit status and cleans IPC artifacts
- headless shutdown failure reports exit status and cleans IPC artifacts
- headless shutdown delayed-failure reports exit status and cleans IPC artifacts
- headless shutdown ready-window reports exit status and cleans IPC artifacts
- headless shutdown slow-log-failure reports exit status and cleans IPC artifacts
- headless shutdown late-write-failure reports exit status and cleans IPC artifacts
- headless shutdown stuck-log reports exit status and cleans IPC artifacts

#### `test/logging-provider-images.test.ts`

- real Gemini wrapper matches direct complete history (image/heic)
- real Gemini wrapper matches direct complete history (image/heif)
- real Gemini wrapper matches direct stream history (image/heic)
- real Gemini wrapper matches direct stream history (image/heif)

#### `test/fleet-smoke.test.ts`

- FleetModule — unresolved launch artifacts > fresh parent refuses an occupied dataDir and preserves the owner connection
- FleetModule — unresolved launch artifacts > persisted ready child killed by parent exit recovers through second-parent autoStart
- FleetModule — unresolved launch artifacts > historical crashed record recovers through explicit launch
- FleetModule — unresolved launch artifacts > confirmed exited record never contacts a live reused PID on boot or launch
- FleetModule — unresolved launch artifacts > confirmed crashed record never contacts a live reused PID on boot or launch
- FleetModule — unresolved launch artifacts > transient adoption failure re-probes the same live PID via boot
- FleetModule — unresolved launch artifacts > transient adoption failure re-probes the same live PID via launch requested
- FleetModule — unresolved launch artifacts > transient adoption failure re-probes the same live PID via launch omitted
- FleetModule — unresolved launch artifacts > transient adoption failure re-probes the same live PID via launch empty
- FleetModule — unresolved launch artifacts > transient adoption failure re-probes the same live PID via launch send error
- FleetModule — unresolved launch artifacts > transient adoption failure re-probes the same live PID via restart
- FleetModule — unresolved launch artifacts > a relaunch adopts a restored child whose dataDir an earlier version kept with a trailing slash
- FleetModule — unresolved launch artifacts > a relaunch adopts its own child beside another record the host is not attached to
- FleetModule — unresolved launch artifacts > adopted-child restart records confirmed death, escalation=false
- FleetModule — unresolved launch artifacts > adopted-child restart records confirmed death, escalation=true
- FleetModule — unresolved launch artifacts > resource exhaustion cannot turn a live Unix listener into stale evidence
- FleetModule — unresolved launch artifacts > tracked stale recovery preserves artifacts after unknown probe
- FleetModule — unresolved launch artifacts > tracked stale recovery preserves artifacts after PID metadata
- FleetModule — unresolved launch artifacts > tracked stale recovery preserves artifacts after socket
- FleetModule — unresolved launch artifacts > tracked stale recovery preserves artifacts after record path
- FleetModule — unresolved launch artifacts > tracked stale recovery preserves artifacts after record generation
- FleetModule — unresolved launch artifacts > reaped handle cannot clean artifacts from a different generation
- FleetModule — unresolved launch artifacts > reaped handle preserves a replacement live socket even with the old PID file
- FleetModule — unresolved launch artifacts > restored parent preserves replacement artifacts after dead persisted PID
- FleetModule — unresolved launch artifacts > restored parent preserves replacement artifacts after adoption PID mismatch
- FleetModule — unresolved launch artifacts > reaped same-owner crash supports manual launch
- FleetModule — unresolved launch artifacts > reaped same-owner crash supports restart
- FleetModule — unresolved launch artifacts > reaped same-owner crash supports autoRestart
- FleetModule — unresolved launch artifacts > confirmed graceful stop reconciles the tracked generation's stale artifacts
- FleetModule — unresolved launch artifacts > confirmed forced kill tool reconciles the tracked generation's stale artifacts
- FleetModule — unresolved launch artifacts > confirmed forced stop reconciles the tracked generation's stale artifacts
- FleetModule — unresolved launch artifacts > stop reconciles an already crashed tracked generation
- FleetModule — unresolved launch artifacts > stop reconciles an already exited tracked generation
- FleetModule — unresolved launch artifacts > unconfirmed death preserves artifacts and never enters reconciliation
- FleetModule — unresolved launch artifacts > detach stop leaves a live tracked child and its artifacts untouched
- FleetModule — unresolved launch artifacts > stop preserves replaced PID metadata after a reaped crash
- FleetModule — unresolved launch artifacts > stop preserves a replacement live socket even with the old PID file
- FleetModule — unresolved launch artifacts > confirmed forced death of an adopted generation reconciles its stale artifacts
- FleetModule — unresolved launch artifacts > stop retries adopted terminal cleanup after an inconclusive socket probe
- FleetModule — Phase 2 > launch → list → status → command/peek → kill round trip
- FleetModule — Phase 2 > launch rejects duplicate name while child is running
- FleetModule — Phase 2 > onChildEvent fans out wire events live (no buffer poll needed)
- FleetModule — Phase 4 autoStart + allowlist > autoStart children launch at launchAutoStart() and reach ready
- FleetModule — Phase 4 autoStart + allowlist > allowlist rejects recipes outside the list (children recipes are implicitly allowed)
- FleetModule — Phase 4 autoStart + allowlist > subscription filter narrows per-subscriber event stream
- FleetModule — Phase 4 autoStart + allowlist > adopt-on-restart: second FleetModule with shared state reattaches to running child
- FleetModule — Phase 4 autoStart + allowlist > relative launch path matches implicit absolute-path allowlist via CWD resolve
- FleetModule — Phase 4 autoStart + allowlist > allowlist prefix wildcard works

#### `test/pack-contents.test.ts`

- a tree with the Web UI built, and nothing git ignores outside it, packs, the built Web UI included
- a tree where git ignores none of the files to pack packs without a word
- a local file git ignores, in a directory files lists, stops npm pack, named
- a local file git ignores, in a directory files lists, stops npm publish, named
- a local file git ignores, in a directory files lists, stops bun pm pack, named
- a file git tracks isn't a local one, whatever .gitignore says: a tree whose only such file is tracked packs
- a file git tracks isn't named beside a local file that stops the pack
- the npm that bun pm pack's prepack runs makes no request
- a file git ignores in a listed directory outside dist/web stops the pack, even beside the built Web UI
- a tree without the built Web UI doesn't pack
- outside a git checkout a pack goes ahead, saying it can't tell what git ignores
- outside a git checkout an npm publish stops, saying why
- in a git checkout git can't read, the pack stops, saying why

## Unmet Criteria

Implementation requirements R1–R7 are covered. A green full-suite gate and identical baseline/final failure counts were not achieved under the required no-network/no-scripts harness: see exact results and the one additional fleet timing failure above. No claim of unrestricted-suite correctness is made. Independent review and separate qualification remain downstream stages and were not performed by this implementer.

## Residual Risk

- No live provider/model calls were made and no network-backed end-to-end turn retry was attempted, as required. Synthetic onInferenceError sequences and the production create-call capture prove the wiring, not provider availability.
- Partial-config completion mirrors a private dependency implementation; constructor parity tests intentionally fail if future dependency behavior or private cached-settings layout changes.
- The full suite is not green in the required no-network/no-scripts environment. An independent reviewer and later qualifier must assess the supplied failure comparison; no review or landing was performed by this implementer.
- Empty `{}` intentionally means unset; any non-empty retry object now changes turns framework-wide, including default patient overloaded retries. This is explicitly documented as a behavior change.
