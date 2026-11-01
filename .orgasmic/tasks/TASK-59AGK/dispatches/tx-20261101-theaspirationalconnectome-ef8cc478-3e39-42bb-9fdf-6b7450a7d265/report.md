# TASK-59AGK implementation report

## Changed

Base: `925e4f70758a8d6e738bc70f0f3c1eb773be0a27`.
Head: `b51f2c84f0397b687feb3871b216ad18e4b6b5ae`. Two new local commits; no history rewrite, push, or GitHub writes.

| Finding | Status | Evidence |
|---|---|---|
| [Provider-stated-wait coverage](https://github.com/anima-research/connectome-host/pull/523#discussion_r4408961777) | Fixed with regression tests | The preload now supplies `retryAfterMs`. Create-call tests cover below/at/above ceilings for 503/429/529 with default, empty, partial, larger-cap, and overloaded-disabled recipes. Short waits must not shorten backoff. |
| [Partial overrides replace turn defaults](https://github.com/anima-research/connectome-host/pull/523#discussion_r4408961785) | Implementer proposal documented; **not decided** | Per the policy-rationale allowance, `replies.md` proposes retaining Membrane-default completion for consistent recipe semantics across call paths. It explicitly requires acceptance of the breaking reduction before release. README/changelog warn that partial overrides replace omitted turn defaults. No runtime policy change or policy approval is claimed. |
| [Migration workaround changes non-turn retries](https://github.com/anima-research/connectome-host/pull/523#discussion_r4408961791) | Fixed documentation with regression test | README/changelog no longer claim exact default restoration. They explain raised backoff caps, enabled non-turn overloaded retries, and separate hold/admission rules. A create-call test checks the example's actual non-turn settings and changed retry decision. |

Commits:
- `7919c9dc84c2616c81416e76a757cea4c7fee82a` — test(retry): cover stated-wait ceilings and clarify migration limits
- `b51f2c84f0397b687feb3871b216ad18e4b6b5ae` — docs(review): key addressed replies to the retry follow-up commit

Reply drafts: committed **`replies.md`**, keyed by all three thread URLs, in English, each beginning “Addressed in 7919c9dc84c2616c81416e76a757cea4c7fee82a.” Nothing posted.

Changed paths:
- `test/capture-retry-create.preload.ts`
- `test/framework-error-policy.test.ts`
- `README.md`
- `changelog.d/470-turn-retry.changed.md`
- `replies.md`

No source behavior, dependencies, lockfiles, or generated assets changed. Test-generated `logs/failures.log` artifacts were moved into evidence, not committed. Dependency caches remain in ignored worktree directories.

Instructions read: supplied review report in full, supplied project baseline, applicable skill and command-ownership instructions. No AGENTS.md was found on the applicable ancestor path or in the worktree; conventions/gotchas were explicitly supplied as none.

## Verification Gates

All checks ran through `orgasmic dispatch gate <name> -- <command>` with
`PATH=/srv/ci-fixtures/bun-1.4.4-linux-x64:/opt/node-v22.23.3-linux-x64/bin:$PATH`.
Toolchain: Bun 1.4.4 and Node v22.23.3; see `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/environment.log`.

| Check | Base | Head |
|---|---|---|
| Full suite, `bun test` | `baseline-suite-ready`: PASS, 4290 tests / 257 files | `head-suite`: PASS, 4296 tests / 257 files |
| Host typecheck, `node node_modules/typescript/bin/tsc --noEmit` | `baseline-typecheck`: PASS | `head-typecheck`: PASS |
| Web typecheck, `node node_modules/typescript/bin/tsc --noEmit --project web/tsconfig.json` | `baseline-web-typecheck`: PASS | `head-web-typecheck`: PASS |
| Focused create-call/non-turn probes, `bun test test/framework-error-policy.test.ts test/membrane-retry.test.ts` | Covered by full baseline | `head-retry-focused`: PASS, 34 tests |

The exact full-suite command at both commits:
```sh
sh -c 'bun test; result=$?; if [ -d logs ]; then mv logs "$ORGASMIC_EVIDENCE_DIR/generated-logs-$(date -u +%Y%m%dT%H%M%S)-$$"; fi; exit "$result"'
```
This preserves Bun's exit status and moves generated log fixtures before the gate ends.

Baseline chronology: the initial `baseline-suite` run, before any edits, had 4273 passes and two module-load errors for `solid-js` in `test/web-wire.test.ts` and `test/web-health-without-ops.test.ts`. Web dependencies had been installed only after Bun started. Classified as environment/setup, not code diagnostics: both files passed 17/17 in a fresh `baseline-web-tests` process, then the dependency-ready full suite passed 4290/4290 at the unchanged base. Initial logs were preserved; no tests were excluded.

The pre-commit `retry-focused` gate passed 34/34 on a dirty tree; final-commit gates are reported separately. `git diff --check` passed; ancestry from the requested base was verified.

Gate logs (absolute paths):

- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-baseline-suite-01M6B9BHPV1RDV2ZB2S39CM72T.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-baseline-suite-ready-01M6B9T0ZD5ARPGMY6JZKQN8GE.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-baseline-typecheck-01M6B9BNEWXK6XG19EBBBV9TE5.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-baseline-web-tests-01M6B9SGTB7ZENCWWYE3G6XYQF.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-baseline-web-typecheck-01M6B9C8G93M7BXN5D2278FZRC.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-head-retry-focused-01M6BA8X8ZRD7J2SA5GHV5P67G.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-head-suite-01M6BA876M4ZJGZHZZQC3AH34M.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-head-typecheck-01M6BA8BPVSDV2EGQ98TSP2F3J.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-head-web-typecheck-01M6BA8C3KBA2R49EJEF0QSREQ.log`
- `/home/orgasmic/.orgasmic/runs/run-01M6B9B2A6PF2RRCTDSFA6HZ5R/evidence/gate-retry-focused-01M6BA7CBVWQMCTG9X9YRAFSS6.log`

All required final gates ran at the reported head on a clean tree and exited 0. The final full suite adds six passing regression cases versus the dependency-ready base; no failures or test exclusions. Baseline and head full-suite receipts both ended clean. Final repository state and ancestry are recorded in `final-state.log`. A durable copy of the committed drafts is also in the evidence directory as `replies.md`.

## Unmet Criteria

None under the assignment's explicit policy-rationale allowance. The second finding is addressed by a documented implementer's proposal, not by deciding or changing runtime policy. Approval of that breaking policy is not claimed.

## Residual Risk

- Membrane-default completion and its reduced turn tolerance remain unchanged. Before release, the proposed policy still needs explicit acceptance, or a follow-up implementation preserving framework defaults.
- The production-call-site probe stops at `AgentFramework.create` and tests actual policy decisions without provider calls or waits. It does not simulate full framework startup, resident provider holds, admission, or live provider responses; no hold-equivalence claim is made.
- Verification used local Linux and the requested toolchain, not the entire multi-OS CI matrix.
