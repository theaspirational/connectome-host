# Compiler-core staging report — LOCAL ONLY

## Changed

- Local commits `15021c4dc4f6529ca8f3bd4b1516045588c9ca5a` (compiler-core/interim protection) and `9c6f6ea1de60f2ac749f9f3184adcc71f128253f` (per-item evidence seam/control), on frozen base `d7154d79b161e0827637e715b0952001b010b393`. Required author/committer and coauthor trailer verified on both; no push, merge, landing or main-checkout edits.
- `src/recipient-policy.ts`, recipe/file loaders: one loud refusal for **every configured production recipientPolicy**, including source definitions hidden by duplicate ids/maps. Reason `recipient_policy_integration_pending`, dynamic selector schema/qualified release missing. Unconfigured unknown-key tolerance retained. This is **INTERIM SCAFFOLDING, not a finished functional H.3 binder**.
- `src/mcpl-config.ts`: protected operator replacement/ownership guards, tombstone disable, no policy installation through overlays; ordinary enabledCapabilities remains drop-with-note. `src/recipient-binding.ts` uses actual AF binding resolution for the staged modern-family guard.
- Neutral attestation/presentation helpers; deterministic escaped separate account entries after normalized prompt; 256 UTF-8-byte identity limit; original resolved name/namespace/summary identity untouched. No grants/tools/commands/features enabled.
- `src/recipient-admission.ts`: local compiler-core interfaces, exact-key A closures, independent compiled read/wake predicates, original detached item supplied to predicates, lane-specific rules, closed intent. Omitted policy emits no callback; positive automatic replies refuse pending .4. `VerifiedScope` is opaque only, with a local trusted-read consumer seam: **no production constructor, fact representation, wire members or guessed AF-scope exports**.
- Historical static schema/validator/mapping are entirely in `test/stubs/f-host-3/v1`, explicitly superseded by T1 (§1:72), not a deployable recipe. The v1 A declarations contain only necessary exported contract/transitive lane types. Supplied A source SHA256SUMS verified before use; provenance/hashes retained in `a-contract-input-shas.txt`. Independent stub↔source review remains the reviewer's gate, not self-certified here.
- Focused tests, actual source mutants, matched production-function feature-off probe, scope compiler controls, staged documentation and changelog. 18 tracked paths changed; exact range: `git diff --stat d7154d79 HEAD`. No locks, CI, frozen fixtures/v0 stubs/checker, dependency versions, live config or stores changed.

### Split acceptance / dependency direction

**(a) Compiler-core scaffolding:** implemented and tested for bounded independent review now. T2 can consume `CompiledRecipientAdmissionPolicy`, `TrustedRecipientRead`, `compileRecipientAdmission`, opaque scope type and pure identity/overlay/binding primitives. The compiled operations take key/channel/opaque scope/**current detached item**, so later predicates can preserve provenance/author-access checks; author-access denial is tested to cap wake without denying readable storage.

**(b) Runtime-binding: PENDING.** Canonical selector schema/representation is an **INPUT GAP**, separate from INT-1 and reviewed S4Q8F. H's later recipe/runtime binding needs that schema and the qualified release; T2 needs the compiler-core interfaces, **not** a live H binding first. Thus (a) unblocks T2 without (b): no “H waits for T2 / T2 waits for H” cycle. No static-only Mapping X deployment or full H.3 completion is claimed.

Operator answers to `ask-ee0687553cb54f4fbfc01339beec25c7` and `ask-9795ca2a1650452785786c22c3f77088`, plus the programme-qualifier clarification, are the engineering boundary used here, not owner policy decisions.

## Verification Gates

### Declared closure / identity

- Isolated code-only base/head roots: `<worktree>/tmp-recipient/build/{base,head}`; separate independently installed trees, no borrowed compiler/node_modules or external symlinks. Final acceptance package processes use `env -i`, scratch HOME/TMPDIR/npm caches, separate empty npm config files and a Git discovery ceiling; see `cell.sh`.
- Command per root: `npm ci --ignore-scripts --no-audit --no-fund`, plus the same script-disabled command with `--prefix web` for the suite's separately locked web inputs. No lifecycle scripts executed. All 12 installed lifecycle declarations are listed in `qualified-lifecycle-not-run.json`, including esbuild postinstall; none was observed to require execution for assigned gates. No web asset build is claimed.
- Absolute Node `/opt/node-v22.23.3-linux-x64/bin/node`, **v22.23.3**, SHA256 `fde6a4bf8d0562f7751d1a2d6cb9b417c4cfe107bbcb0aa3e9a24e125e348f48`; npm **10.9.9**. Effective install config: registry `https://registry.npmjs.org/`, ignore-scripts=true, audit=false, fund=false; config/cache paths and npm identity in `qualified-closure-identity.txt` / `runner-identity.txt`.
- Host lock SHA256 `7f0e4849a8238b90e9d782ffef558a3e5b80fe6252154d2372f42f9a5c09e020`; web lock `0617247d4e5a2d86a86886ec98e3dd3464a9250ce8b8b14dd8d7f9fb856fb0c3`. Manifest/bun-lock hashes remain frozen. TypeScript **7.0.2**, launcher SHA256 `2219f428a7e55aaf1f7ad85b9b0f0cf5078aeb76ccc9a7c6036c92d48f492ffd`; selected native payload SHA256 `4f2de678286401759b3fb4475bafe35b8f32b4b3a07d92642bbf37eadc9b34a4`; package SRI in `compiler-package-sri.json`.
- Final installed-tree manifests: `installed-base.json` / `installed-head.json`, **16820 entries each, byte-identical**, SHA256 `6b42678559f175f926db4a39c1527f2415c37bab1392be6a74ae95446233ed64`. `source-{base,head}.json` and `inventory-summary.txt` verify every tracked source byte against the frozen base/final commit. No cross-tree symlinks. Inventories are small evidence; all build caches remain in the worktree.
- Bun **1.4.3**, official release `https://github.com/oven-sh/bun/releases/download/bun-v1.4.3/SHASUMS256.txt`; linux-x64 archive verified against that checksum before execution. Binary SHA256 `7ce7d6b654eddeec20afddf2396317fba6dc19ab52788a27e894e1806bf8f24b`; URLs/archive/binary hashes in `bun-identity.txt`.
- Separate `frozen-bun-route` acquisition: `bun install --frozen-lockfile --ignore-scripts`, followed by `node scripts/lockfiles-agree.mjs` (source script invoked from the separate root): 137 packages, TypeScript 7.0.2; unchanged manifest/locks and stack lock agreement PASS. `bun-route-installed.json` inventories that separate route. This is stack-lock agreement, **not** equality of all Bun/npm installed packages. Source/compiled AF entry identities are recorded, but the installed AF remains **0.30.0**, not INT-1.

### Final-commit gates

All below ran through `orgasmic dispatch gate` at final commit with a clean worktree. Template:

`orgasmic dispatch gate <name> -- bash "$ORGASMIC_EVIDENCE_DIR/bounded.sh" bash "$ORGASMIC_EVIDENCE_DIR/cell.sh" <base|head> <command>`

`bounded.sh`: independent systemd scope, **MemoryMax=16G**, swap=0, verified memory.max=17179869184; `timeout --signal=TERM --kill-after=60s 3000s`; **64 MiB cap**, PIPESTATUS retained; 124/137/141/cap = INCONCLUSIVE, never PASS/refusal. Actual wrapper/cell scripts and hashes retained. No final gate timed out or hit its cap. Suites used declared `bash scripts/test-on-tmpfs.sh` → `npm test -- --parallel=4` on verified Bun. Logs below are relative to this evidence directory; raw bounded logs and sizes are named in each receipt.

| Gate | Command after cell wrapper (or comparison wrapper) | Result | Log |
| --- | --- | --- | --- |
| final-base-types | `base: npx tsc --noEmit` | PASS | `gate-final-base-types-01M681EPQE9TGBVRQY1NVXR99T.log` |
| final-head-types | `head: npx tsc --noEmit` | PASS | `gate-final-head-types-01M681ER5F1B68S025AMPQG7BY.log` |
| final-base-suite | `base: bash scripts/test-on-tmpfs.sh` | PASS: 4579 tests | `gate-final-base-suite-01M681EQVGBX8SZ4HRXRHJ401B.log` |
| final-head-suite | `head: bash scripts/test-on-tmpfs.sh` | PASS: 4597 tests | `gate-final-head-suite-01M681JE8FTMDAPJ0KT3Y5SWK2.log` |
| final-base-v0-types | `base: npx tsc --noEmit -p test/stubs/f-host-2a/v0/tsconfig.json` | PASS | `gate-final-base-v0-types-01M681EQ9Y0WYY489SYQ0BG5QN.log` |
| final-head-v0-types | `head: same frozen v0 command` | PASS | `gate-final-head-v0-types-01M681ERVF95SPE2QK8PGZ71KT.log` |
| final-head-v1-types | `head: npx tsc --noEmit -p test/stubs/f-host-3/v1/tsconfig.json` | PASS | `gate-final-head-v1-types-01M681EST4X89D3981BK5SR0NF.log` |
| final-compiler-controls | `base: node <worktree>/test/stubs/f-host-3/check-compiler-controls.mjs` | PASS: four diagnostics; isolated TS2542; all six surfaces | `gate-final-compiler-controls-01M681ETA6Y86CC30XS5JXBSA9.log` |
| final-focused | `head: bun test test/recipient-policy.test.ts test/recipe-unknown-keys.test.ts test/mcpl-agent-overlay.test.ts` | PASS: 90 tests | `gate-final-focused-01M681EVZ67ZNE47GF7HE681TD.log` |
| final-mutants | `head: BUN_BIN=$(command -v bun) node test/stubs/f-host-3/run-mutants.mjs` | PASS: 21 mutants, named witnesses, source restored | `gate-final-mutants-01M681EWFBM4FP6G3N0YFJM166.log` |
| final-base-self-test | `base: node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: documentation oracle only | `gate-final-base-self-test-01M681EQS9FMBGDZ1HZEKGYSVY.log` |
| final-head-self-test | `head: same --self-test command` | PASS: documentation oracle only | `gate-final-head-self-test-01M681ESE39CVCRGTRFNPG92XH.log` |
| final-base-docs-contract | `base: node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | exit 0; PARTIAL naming | `gate-final-base-docs-contract-01M681EQZAKK3YMYT6ZKZGVHC7.log` |
| final-head-docs-contract | `head: same --docs-contract command` | exit 0; PARTIAL naming | `gate-final-head-docs-contract-01M681ESMD2JD2DFAC1HXNCN6T.log` |
| final-feature-off | `matched base/head: bun <worktree>/test/recipient-feature-off.probe.ts <isolated-root>; cmp outputs` | PASS: byte-identical JSON | `gate-final-feature-off-01M681F0VDP1DJC5MMXWTPKCRQ.log` |
| final-diff-check | `git diff --check d7154d79b161e0827637e715b0952001b010b393 HEAD` | PASS | `gate-final-diff-check-01M681F0N6560BZ9AB40Q2JEJ2.log` |
| local-diff-inspection | `python3 "$ORGASMIC_EVIDENCE_DIR/check-local-diff.py"` | PASS; targeted/partial naming only | `gate-local-diff-inspection-01M681F1DE4QXE3RSERGND61J7.log` |

The feature-off JSON hash on both sides is `685a9496a16c3f1e86573189bbdd5ff2ff049da8fa52dc4a1fa48a973e2154ec`: actual existing recipe/merge/overlay/agent-config production functions, masks and disabled prose preserved. It is not a real AF no-await/transport byte-equivalence receipt; U01/U02 D selected-field exceptions are explicitly excluded, not redefined.

### Earlier diagnostics / non-reusable exploratory cells

- Initial base suite: 4562 pass, 2 errors/failures because web tests could not resolve solid-js. Environment/closure incomplete, not a code regression. Raw `raw.3OcLJv.log`; receipt `gate-base-suite-01M680G1YFXHQRAD47H6NZ3W4H.log`. The own web lock was then acquired script-disabled before matched acceptance runs; no lock edits or lifecycle workaround.
- First focused and compiler-control launch commands used wrong relative executable/script paths (exit 127 / exit 1). Corrected to the verified local executable/absolute script; logs preserved. These were command construction failures, not security-refusal witnesses.
- First sanitized npm attempt failed **before installation** because npm refuses the same /dev/null path as both user/global config (two `gate-isolated-*-closure` logs). Corrected to distinct empty scratch files; qualified base/head acquisition succeeded. Early exploratory cells inherited the runner environment; only the subsequent clean `env -i` cells are acceptance evidence. No live config/history was copied into either source tree.
- CJS require.resolve cannot resolve this ESM-only AF export. Correct ESM Node and Bun entries are recorded separately; no package/API substitution occurred.
- Precommit exploratory gates and prior-commit passes are not reused as final-commit checks. Both matched suites were rerun after the per-item seam commit; no retry-until-green or timeout relabeling.

## Unmet Criteria

These remain explicitly **PENDING**, not satisfied by compiler/stub tests:

1. **Canonical selector schema/representation INPUT GAP:** needed for deployable recipe/file mapping. Historical exact lists are test-only; configured production policy loudly refuses. No T2 fields invented.
2. **INT-1** exact composed A+B+D release/source/tarball/manifest/integrities/exports and reviewed **S4Q8F** scope integration/rebuild. No actual A import against an unqualified release and no B/D behavior fabricated.
3. Trusted native verifier/projector and real opaque scope constructors/accessors, registration references/generations, parent/currentness/invalidation/post-await operations. Opaque type + trusted consumer seam is not that implementation. Adapter account/producer evidence packaging remains an input gap.
4. Real .2a-backed startup/API/transport integration: both modern zero-connect cases, whole-result attestation API non-data controls, terminal per-dial failure vs ordinary transport failure, attestation-before-bind, exact issued key/runtime reconnect, grant/epoch enforcement, timeout/abort/64-cap/late results and ordered zero-forbidden-effects. Stub objects cannot prove those.
5. .4 positive automatic replies/resolver/synchronous authorize, .5 D/profile/storage and persistence/compaction/restart; T2–T5 dynamic/native/parity and G qualification. No live operation/migration/activation.
6. Full private naming policy was not supplied. Docs-contract is **PARTIAL**, covering its unchanged nine inputs, not the brief or this new implementation/document. Separate targeted new-diff inspection is not full naming or ancestry qualification.

The requested bounded compiler-core/interim protection work is delivered for review; **the functional runtime-binding acceptance is not complete**.

## Residual Risk

- Core callback shape is checked against versioned pinned declarations, not a composed runtime release. A reviewer must verify stub↔source and the local host seams; no fake scope representation or unrestricted authority factory fills the missing integration.
- No static mapping is credited as Mapping X preservation. Read/wake are independent ceilings; later canonical predicates must enforce every T1 fact/lifetime check. No native access, storage parity, transport no-await, D wire or real effect-count qualification is claimed here.
- All descendants retain private ancestry. **LOCAL ONLY, no push.** Public submission still requires a fresh clean tree on a public base, privacy review of the whole range and retention of private refs; scrubbed additions do not clean history.
- Own processes reconciled by pid/start/cgroup; see `process-reconciliation.json` / `process-reconciliation.txt`. Build state is retained under the ignored assigned-worktree scratch directory for reproduction, not in evidence or a neighbouring worktree.
