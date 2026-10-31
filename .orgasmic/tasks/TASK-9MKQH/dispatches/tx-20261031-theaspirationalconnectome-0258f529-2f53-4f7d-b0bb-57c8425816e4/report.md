# T2 docs/stub PREP report

## Changed

Completed the **narrowed docs/stub PREP**, not production T2 implementation.
The blocking clarification `ask-007c1d48adec48c7a7c90a3026ef9293` confirmed:
there is no frozen H implementation on any branch and no concrete frozen
VerifiedScope representation. The task operator explicitly narrowed this round
to the brief, planned allocation/controls, opaque stub and bounded documentation
gates, and instructed `done` for that input deliverable. No owner decision was
requested or made.

Start: `8e92bfbe30d141140531b170e9ef0421c6a16cad`.
Final: `7e93298728393972fa5a967a7e21c5dda7b5aafd`, clean worktree.

Local commits, by concern:

1. `be202c5ee69ef3d0239a6dbb6cad2e9efb615324` — registration-view v0 alias/type tests.
2. `7e93298728393972fa5a967a7e21c5dda7b5aafd` — T2 input brief and docs scan inputs.

Both have the requested author/committer and co-author trailer. No push, merge,
landing, dependency acquisition or main-checkout edit occurred.

Files (5 files, 312 insertions):

- `docs/design/f-host-t2-brief.md`: verbatim T2 definition; four named INPUT GAPs;
  baseline absence with file:line; planned dynamic mapping and seven feature-off
  controls; all 57 scope-case ids mapped to planned test/mutant, including 49 host
  portions and 8 native-only exclusions, plus explicit T3/T4/T5 witness boundaries.
- `test/stubs/f-host-t2/v0/stubs.ts`: **STUB, not integration evidence**;
  `registration-view v0` aliases the unchanged 2a binder's registration argument.
  No new view fields/methods, concrete sidecar or AF packaging/export.
- `test/stubs/f-host-t2/v0/typecheck.ts`: compile-only positive/negative contracts,
  opaque K/R/VerifiedScope and synchronous currentness; not executed/typechecked.
- `test/stubs/f-host-t2/v0/tsconfig.json`: extends unchanged isolated 2a config.
- `test/fixtures/generic-recipient/check-scope-contract.mjs`: four text scan inputs
  only; no oracle/schema/neutrality logic changes.

Applicable instructions: project baseline read; supplied conventions/gotchas none;
no AGENTS.md found in applicable worktree/ancestors. CONTRIBUTING.md read.
Frozen documents, fixture JSON and existing 2a v0 stubs are unchanged. No src,
AF, CI, permission, dependency, lockfile, policy-shapes or T1 document changes.

## Verification Gates

All final gates ran at the clean final commit above through
`orgasmic dispatch gate NAME -- bash "$ORGASMIC_EVIDENCE_DIR/bounded-64m.sh" 60s COMMAND`.
The evidence-only wrapper owns producer/reader under
`timeout --signal=TERM --kill-after=60s 60s`, combined cap **67108864 bytes (64 MiB)**.
All final exits were 0; no timeout/kill/SIGPIPE/cap events; no assertion failures.
124/137/141/cap would be INCONCLUSIVE, not a pass. Runtime: Node v22.23.3.

Logs below are relative to this report's evidence directory. Each gate log also
records its exact raw bounded-output log path and command receipt.

| Gate | COMMAND | Result | Bytes | Receipt log |
| --- | --- | --- | ---: | --- |
| final-self-test | `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: 57 cases, 45 rejection controls, 4 existing oracle mutants killed; singleton/parser controls pass | 876 | `gate-final-self-test-01M67YX8Z5XQRM95PRMPGJ4BZZ.log` |
| final-docs-contract | `node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | PASS structural checks: 13 files, 11 rejected controls, 0 injected names; **PARTIAL naming** | 231 | `gate-final-docs-contract-01M67YX94FF2BVSTQS2C97Z1EH.log` |
| final-allocation-audit | `node "$ORGASMIC_EVIDENCE_DIR/audit-prep.mjs"` | PASS: 57 unique ids in fixture order, 49 host portions, 8 exclusions, 0 missing/extra; 9 frozen input files identical; boundaries unchanged; 0 src stub imports | 253 | `gate-final-allocation-audit-01M67YX99QE3KT13VV1CVFYN3Z.log` |
| final-input-preflight | `bash "$ORGASMIC_EVIDENCE_DIR/input-preflight.sh"` | PASS inventory only: hashes match; src identical to upstream; 0 recipient baseline matches; missing toolchain recorded | 1502 | `gate-final-input-preflight-01M67YX9EW487B6CHH39SBPYKB.log` |
| final-diff-check | `git diff --check 8e92bfbe30d141140531b170e9ef0421c6a16cad HEAD` | PASS: no whitespace errors | 0 | `gate-final-diff-check-01M67YX9M53XCC3ZZM9AD4B13A.log` |

Earlier input/preparation receipts are also retained but final receipts above
supersede them; dirty-tree preparation receipts are not claimed reusable.

Existing documentation-oracle mutant kill witnesses (not T2 host mutants):

- wake selector bypass → `dynamic-addressed-join-read-only`;
- agreement conjunct bypass → `dynamic-parent-claim-native-disagreement`;
- trust-the-claim native identity substitution → same disagreement case;
- read sub-conversation selector bypass → `dynamic-read-sub-conversations-denied`.

Documentation-oracle survivors: **0 of 4**. T2 selector-ignoring, feature-off
leakage, wrong-epoch/wrong-registration mutants: **NOT RUN**, no kill or survivor
count claimed. There is no production T2 code to mutate in this narrowed round.

Host's own suite: **PENDING, environment-blocked**. Declared command is
`bun test` (`package.json:23`); CI uses `bash scripts/test-on-tmpfs.sh`
(`.github/workflows/ci.yml:55–57`), which calls `npm test -- --parallel=4`
(`scripts/test-on-tmpfs.sh:26`). Bun and local node_modules are absent. Executed
host tests: 0; no test failure names exist because the suite was not launched.

Stub typecheck: **PENDING, environment-blocked**. No local tsc/dependency closure;
required declaration is TypeScript `^7.0.2`. No compiler/dependency acquisition,
borrowing or exploratory compiler substitution. The preflight's exit 0 is not a
suite/typecheck pass. Type-test diagnostic locations remain unqualified.

## Unmet Criteria

None for the operator-narrowed docs/stub input round. Original production T2
acceptance is deliberately **not delivered**:

1. **H static policy/binder pin missing.** Exact insertion points:
   `src/recipe.ts:506–620`, `src/mcpl-config.ts:33–63,71–112`,
   `src/index.ts:470–496,508–533`; none contains the recipient baseline.
   Entire src tree is byte-identical to upstream `2c396622`; source search for
   recipientPolicy/inboundAdmission/replyAuthority/joinedReadable/VerifiedScope
   returns 0 lines. H is design only, so no valid static binder file:line exists.
   T2 is incremental per host design `:703`; H was not rebuilt here.
2. **Concrete VerifiedScope representation missing.** T1 `:324–326` names facts
   but no concrete fields/types/kind encoding; `:336–338` leaves spelling/packaging
   open. Existing 2a `stubs.ts:11` is opaque. Host evaluation needs account, kind,
   readability/joined-or-DM-user, exact parent R and immutable mapping generation;
   producer attestation/extraction and projector-denial/configuration contract
   remain open (T1 `:94–123,311–322`). No invented fields/decoder/metadata authority.
3. **Reviewed S4Q8F view integration gate: OPEN.** No reviewed head consumed.
   Need exact K/R, accepted-scope readiness, sync currentness and descendant
   invalidation after replacements/revocations/epochs, including post-await push
   checks (T1 `:235–268,298–334`). Stub tests are not integration evidence.
4. **D / A+B+D composed integration release missing.** Need one reviewed composed
   SHA/tarball/hash/manifest/source+compiled identities per host `:43–76` and
   T1 `:336–342`; current ^0.30.0 is not an approved substitute.
5. Actual config compile/bind, byte-identical H feature-off behavior, executed
   T2 mutants, native fake API and old/new runtime parity all remain unqualified.
   T3/T4/T5 exclusions and shared host-only eligibility portions are explicit in
   the brief; a plan is not a test result.
6. Full private naming policy is absent: docs-contract is PARTIAL, not full
   naming/privacy qualification. No internal names were added to tracked files.

## Residual Risk

- Compile-only alias/type tests await the declared local TypeScript toolchain;
  no runtime view, sidecar projector or real host path is implemented or tested.
- 57 fixture snapshots and four oracle kills cannot qualify native parent truth,
  lifecycle races, exact-thread sends, recursive/cycle behavior or actual storage.
- The reviewed S4Q8F head integration gate stays explicitly **OPEN**. This work
  neither consumes an unreviewed head nor authorizes AF/runtime support.
- **LOCAL ONLY**: private F-host/T1 ancestry remains intact. No push. A future
  public submission requires a fresh clean commit/tree on a public base and a
  privacy check of the entire publication range, retaining private refs. Scrubbing
  new files and a general topic-push go do not lift this hold.
- No permission, recovery, cutover, typing, deployment or owner-policy changes.
