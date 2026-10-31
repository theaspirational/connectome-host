# Review — TASK-YXASE (T2 core, compiler-core selector slice)

Range `aecd4a8cafe470ad2bbe47b5087c1ec454eef62f..8df256ccda356d9235f459b4d504a82760d7ed05`.
Brief `/srv/review-drop/tarn-rtaln-20261030/T2CORE-BRIEF-v4.3.md` (sha256 `6482ad06…1e69730`, re-verified).
Report `/srv/review-drop/TASK-YXASE-IMPL1.md` (sha256 `a0fc2f39…12b1`, re-verified).
Worktree left exactly as found: `git status --porcelain` empty, no commits, no edits; all four reviewed file
hashes still match `checker-inputs.json`/evidence, so no post-gate drift.

## Verdict

**APPROVE — MEDIUM confidence.**

The compiler-core slice does what the brief specifies, the mechanisms are checked where the brief says
they must be, and the report is unusually candid about what is stub coverage. Confidence is MEDIUM, not
HIGH, for two reasons that are documentation/evidence-strength issues rather than defects: the 49-row
class accounting over-reports the "independent host witness" class by 2 rows (F1), and one of the 13
mandated mutants is pinned by a source-text assertion rather than a behavioral verdict (F2). Neither is
a correctness bug, an unmet §6 stop, or a production-surface violation.

## Findings

### MEDIUM

**F1 — `docs/design/dynamic-selector-core.md:25,38,54,62`: two rows are labelled `independent host
witness` although their single witness is shared with a row labelled `shared witness`, so the published
per-class counts (21/9/19 at `:17–19`, repeated in the report's row-accounting table) overstate the
independent class by 2.**

Failure scenario: a manager reads `independent host witness: 21` and concludes 21 rows each have their
own host control. Two of them do not:

- `future joined facts` (one test, `test/stubs/f-host-t2/v1/dynamic-policy.test.ts:71`) backs
  `a04-joined-after-startup` (`:25`, class *independent*) **and** `dynamic-fresh-epoch-readmission`
  (`:62`, class *shared*).
- `unreadable facts deny` (one test, `dynamic-policy.test.ts:127`) backs `dynamic-unreadable-join`
  (`:54`, class *independent*) **and** `t03-root-access-revoked` (`:38`, class *shared*).

By the brief's own definition (§3.6: a shared witness is "implemented once, mapped to each row, never
counted as 4"), a witness mapped to two rows makes **both** rows shared. The honest split is 19
independent / 11 shared / 19 witness-only. Verified by extracting the witness column and grouping:
duplicate witness strings are `child denied without parent-authority fact` (11 rows, all witness-only —
correct), `none in this slice` (8, all witness-only — correct), `lookup absent` (6, all shared —
correct), and the two mixed-class pairs above.

Fix direction: reclassify `a04-joined-after-startup` and `dynamic-unreadable-join` as `shared witness`
and restate the counts as 19/11/19 (the shared-family count then becomes four families over 11 rows), or
give each of those two rows its own distinct fixture. Row ids, responsible slices and the 19
witness-only rows are correct and need no change.

### LOW

**F2 — `test/stubs/f-host-t2/v1/dynamic-policy.test.ts:168–174`: the `reply skips full read conjunction`
mutant (`run-mutants.mjs:15`) is killed by asserting the mutated source string, not by a verdict.**

The witness reads `dynamic-policy.ts` and asserts it `toContain('reply(key, channel, scope, input) &&
read(key, channel, scope, input)')`. The mutant deletes exactly that substring, so the kill is
tautologically coupled to the mutation text: it survives nothing, but it also proves nothing about
behaviour and would fire on any semantically-identical restructuring of `dynamic-policy.ts:106`.

I confirmed the implementer's stated reason is correct rather than an excuse: `subset()`
(`dynamic-policy.ts:57–62`) is a sound over-approximation on this fact domain — for every selector pair
that compiles, `reply ⇒ read` holds on the `joined`, `dm` and `sub-conversation` branches
(`:95–99`), so no behavioral discriminator is constructible without an impure spy, which §3.2 forbids.
The structural requirement itself is genuinely met: `src/recipient-admission.ts:107–128` shows
`replyChannel` calling `reply(...)` and never `read(...)`, and `dynamic-policy.ts:106` supplies the
missing conjunct. The residual exposure is that if `subset()` is ever loosened, nothing behavioral would
catch a dropped conjunction. The report, the design doc (`:101–106`) and the test comment all disclose
this, so it is a disclosed weakness, not a misrepresented kill. **The other 12 T2 mutants are genuine
behavioral kills** (each traced below).

Fix direction (later slice, not a blocker): once `subset()` stops proving `reply ⊆ read` for some member
— e.g. when the projector adds a child conjunct read does not carry — replace the text assertion with a
verdict fixture.

**F3 — `test/fixtures/generic-recipient/check-scope-contract.mjs:336–345`: adding the 10 new files to the
docs-contract input list buys zero literal scanning at this head, because `texts.forEach(neutral)`
(`:367`) aborts on input #1.**

`neutral()` uses bare `assert`, and the first listed input is the checker's own source, which contains
the declared `run-mutants` false positive matched by `/\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/iu`
(`:233`). `forEach` therefore throws before reaching any of the 10 new inputs — visible in
`gate-t2-head-docs-contract-…log`, whose only output is one `AssertionError: internal identifier` at
`:233`. The brief's "every new T2 file is added to the input list" is satisfied literally; the neutrality
evidence in fact comes from `checker-inputs.json` plus the manual read, not from the gate. The report
does not state that the scan never reached the new files.

I closed the gap myself rather than leaving it as an evidence hole — see the Verification Gates section:
all seven detectors over all 25 inputs, only the checker fails, all 10 new files clean. So the claim
holds; only its stated provenance is loose. Fix direction: note the fail-fast in the report, or have
EC6WW's rebase make `neutral()` accumulate per-input results instead of throwing on the first.

## Verification Gates

Reused (recorded at `8df256cc`, read not rerun): `t2-diff-check` 0; `t2-head-types` 0;
`t2-head-dynamic-types` / `-v1-types` / `-v2-types` 0; `t2-head-dynamic-negatives` 0 (8 negatives);
`t2-head-focused` 0 — **49 pass / 0 fail / 153 assertions / 1 file**; `t2-head-dynamic-mutants` 0 —
**13 `KILLED` lines**; `t2-head-old-mutants` / `-reply-mutants` / `-root-negatives` / `-v1-negatives` /
`-v2-negatives` 0; `t2-head-drop-node` / `-drop-bun` 0; `t2-head-self-node` / `-self-bun` 0;
`t2-head-zero-selection` 0; `t2-immutable-inputs` 0; `t2-stage-head` 0; `t2-head-toolchain` 0.
`t2-head-docs-contract` **FAILED, producer 1** — expected, and reported as FAILED (not "all gates pass").

Own probe (recorded, reusable): `t2-review-neutrality-probe` — exit 0 —
`node /tmp/rev-yxase/neutral-probe.mjs` (copy at `$ORGASMIC_EVIDENCE_DIR/neutral-probe.mjs`), log
`gate-t2-review-neutrality-probe-01M68DP2VPENX5WZ591C5WNEMC.log`. Reimplements all seven `neutral()`
detectors over all 25 docs-contract inputs without fail-fast: the **only** failing input is
`./check-scope-contract.mjs` on `internal identifier="run-mutants"`; **all 10 new T2 files are clean**
under every detector. No full suite run (not assigned to review; base/head receipts already exist).

Read-only inspection, per brief check:

1. **Zero `src/` changes.** `git diff --name-status` over the range: 11 paths, all under `docs/design/`,
   `test/fixtures/`, `test/stubs/`. `grep -rn "f-host-t2\|stubs/" src/` → no hits, so no production
   importer. `doubles.ts:31` casts `Object.freeze({}) as VerifiedScope` inside a test double, which §2
   expressly permits; no brand constructor, accessor or issuance path is added. Value imports from
   `src/recipient-presentation.js` match the sanctioned precedent byte-for-byte
   (`test/stubs/f-host-4/v2/static-policy.ts:4–6`), and `ConnectionKey`/`AdmissionLane` come from
   `f-host-3/v1/a-contract.ts:174,173` as §2/E7 require.
2. **C1–C6 per operation, no early admit, no admit on parent presence alone.** `dynamic-policy.test.ts:
   212–237` loops C1–C6 × {read, wake, reply} = 18 cases. Code path `dynamic-policy.ts:87–99`:
   C1 `:88`, C2 `:91`, C3 `:92`, C4 `:89`, C5 falls through to `:95–99`, C6 admits only after both child
   conjuncts **and** every per-operation fact conjunct. C1's fixture asserts
   `expect(h.d.parents.get(h.scope)).toBeDefined()` (`:215`), so the deny provably comes from the
   `subConversations` conjunct alone, as §3.6 demands. No branch returns `true` before `:96`/`:99`.
3. **§4 negatives are discriminating.** `v1/typecheck.ts` keeps both facts controls and proves neither
   subsumes the other: `FlipKeepsKeys` (`:14`) passes while `FlipBreaksBoth` (`:15–16`) is
   `@ts-expect-error` — required→optional breaks bidirectional, key-set cannot see it; `ExtraPassesBoth`
   (`:19`) passes while `ExtraBreaksKeys` (`:20–21`) is `@ts-expect-error` — an added **optional** member
   breaks key-set, bidirectional cannot see it. One-way view negative at `:22–24`; no-boolean-member
   negatives at `:26–29`; `ParentAuthorityFact` has no boolean member (`dynamic-policy.ts:20`) and is not
   added to `VerifiedScopeFacts` (`:31` negative). `check-compiler-controls.mjs` removes each suppression
   **separately** and pins the exact line and diagnostic code (`TS2344`/`TS2322`/`TS2353`), so no unused
   suppression counts as a witness. Undefined-safety: `optionalData`→`dataObject`
   (`src/recipient-presentation.ts:32`) rejects `undefined`/arrays/non-plain prototypes, `ownData` (`:26`)
   returns `undefined` for getters **without invoking them** — pinned behaviorally by
   `dynamic-policy.test.ts:67–69` asserting `accessed === false` — and `:82–85`+`:83–100` fail closed on
   absent selector, absent facts and any throw.
4. **Three-level absence; reply = intersection.** `:74` returns `undefined` only for syntactic absence
   (`Object.hasOwn`); a present `{}` block reaches `:75–110` and compiles to a policy that denies every
   operation (test `:35–45`, including `expect(h.p).toBeDefined()` plus all three predicates `false`);
   absent selectors inside a present block deny via `predicate(undefined)` at `:82` with no path to
   `wakeChannels`/`replyChannels`/"all" (test `:46–50`, and `typecheck.ts:33–34` makes a static fallback a
   compile negative); empty lists deny (`:99` `[].includes` → test `:51–54`). `:106` is
   `reply(...) && read(...)` — an intersection: test `:175–179` shows read `true` and reply `false` on the
   same scope, so reply keeps its own members, and `:108–112` shows reply-only denial leaving store/wake
   intact. Independence both ways is pinned (`:142` read does not imply wake).
5. **13 mutants, zero-selection guard.** Traced each to its named failing witness: `absent selector
   allows all`→`:46`; `empty list allows`→`:51`; `present empty block becomes feature off`→`:39`
   (`h.p` would be `undefined`); `reply mirrors read`→`:178`; `DM matches channel id`→`:120`;
   `child skips sub-conversation conjunct`→C1; `child parent presence alone`→C2; `parent authority scope
   binding skipped`→C3 second fixture; `parent authority parent binding skipped`→C3 first fixture;
   `child admits early`→C5 (`dmUserId: 'sample-other'`); `unknown fact allows`→`:152`;
   `predicate memoises`→`:159–167` (`first ??= …` pins a `false` verdict, so `:163` flips). **12 genuine
   behavioral kills; the 13th is F2.** The runner is not tautological in structure either: it asserts a
   unique mutation site (`:38`), exit status 1, no `SyntaxError` masquerading as a kill (`:48`), and a
   `(fail)` line naming the witness (`:49`), then restores and re-verifies the source (`:51–52`).
   Zero-selection guard at `:33`; receipt `t2-head-zero-selection` proves inner exit 1 plus the
   `selector matched zero mutants` message.
6. **49-row table.** First commit `cd5edcd8` is docs-only (81 insertions) and precedes all
   implementation commits. 49 rows, 49 unique ids; the id set is **identical** to the 49 `T2 +…` rows of
   `git show 9d022502:docs/design/f-host-t2-brief.md:172–232` (diff empty). Class counts in the table sum
   to 49 (21/9/19) and match `:17–19` — but see F1. Planned/executed/shared/deferred are kept apart and
   every one of the 30 covered rows names a witness that exists as a real executed test; the 19
   witness-only rows say `none in this slice` or `child denied without parent-authority fact (not a
   real-row witness)`. **Nothing deferred is relabelled as executed.** 7 projector + 3 T4 deferrals match
   §3.6 exactly; 5 `oracle-of-mapping-X` rows = the 5 `+T5` rows.
7. **Checker edit is input-list only.** The diff is a single 10-line insertion into the `docsContract()`
   path array; `neutral()`, the detectors, the oracle and the rejection controls are byte-unchanged. No
   EC6WW merge. docs-contract FAILED as expected (see F3).
8. **Neutrality.** My probe (above) clears all 10 new files under all seven detectors. Manual read for
   split/reconstructed strings: the only `join`/concat constructs in the new tree are
   `check-view-contract.mjs:15` (`'\n'`, descriptor substitution) and `check-compiler-controls.mjs:23`
   (diagnostic joining) — no reconstructed identifier, path, token or credential. The design doc carries
   shas only. All five commit messages are neutral (`git log --format=%B`): no task/run/conv id, no path.
9. **Honesty of claims.** Spot-checked and sustained. `===` is correctly described as proving only the
   cooperative model's binding rule (`report`, `dynamic-selector-core.md:97–99`); stub-of-projector and
   stub-of-AF-view are separated from real coverage; the structural mutant is disclosed in three places;
   "49 pass" is explicitly not claimed as 49 independent row witnesses; "49 independent controls" is
   never claimed. The pinned-drop transcription is byte-bound: `view-contract.ts` reproduces AF
   `registration-view.ts:6–25` with only the documented `RegistrationDescriptor`→`ReadonlyData`
   substitution, enforced at `check-view-contract.mjs:13–17` against the sha-checked drop. F1 is the one
   place where a stated count does not survive checking.

Per instruction, the 31 shared base/head failures were **not** evaluated as a T2 defect. I verified only
that the report keeps them open: they appear as the first Unmet Criterion, in the three-observation
table (4591/31 base vs 4640/31 head, identical per-test on all 4622 existing tests, 49 new pass), and in
a full 31-row name table, with the historical 4622/0 discrepancy called "unexplained" rather than
"environmental". The mock-child root holders and both operator teardown dispositions are recorded as
findings, not repairs.

## Unmet Criteria / test gaps

- **F1**: the per-class counts in the required first-commit deliverable over-report the independent class
  by 2 rows. Every other element of the acceptance list (per-row class/witness/responsible slice, §2–§4
  as written, C1–C6, §4 negatives, named mutant kills, docs-contract FAILED, stub-vs-real separation) is
  met.
- **F2**: 12 of 13 T2 mutants are behavioral; the reply/read conjunction has structural evidence only.
- No behavioral control exists for `{dynamic: undefined}` as distinct from an absent key — it throws
  (test `dynamic-policy.test.ts:57`), which is conservative and intentional, but the brief's §3.1(i)
  three-level rule is pinned only for the syntactic cases.
- Unchanged and in-scope by design: 19 witness-only rows, the projector slice, T3/T4/T5, I6 issuance,
  I7 qualification, the H loader feature-off controls, and the `.4` activation blocker.

## Residual Risk

1. All fact and parent-authority authority is cooperative. `doubles.ts` mints scopes by cast and answers
   from `Map`s keyed by object identity; `===` binding proves the model's rule, never issuance,
   authenticity, parent native agreement, currentness or unforgeability. The 11 parent rows have only the
   common "denied for want of a parent-authority fact" scaffold — correctly labelled, but it is a
   negative scaffold, not a projector witness.
2. With a present dynamic block whose selectors all deny, channel-less `push/event` and
   `inference/request` still yield `{store: true, mayWake: true}` through the frozen `.3` lane path
   (`src/recipient-admission.ts:186–206`), because those items never reach a selector. This is the
   frozen lane rule, not a §3.1 violation, and `dynamic-policy.test.ts:180–187` pins it together with
   `replyAuthority.resolve(...) === null`. Anyone reading "empty block denies every operation" should
   read it as every *selector-gated channel* operation.
3. `subset()` soundness is load-bearing for more than it looks: it is what makes F2 unconstructible and
   what makes `reply ⊆ read` a static fact. Loosening it later silently weakens the conjunction
   evidence.
4. The t2 mutant runner mutates a tracked file in place. It restores and re-verifies in `finally`
   (`run-mutants.mjs:51–52`), and the tree is clean now, but a `SIGKILL` mid-run would leave
   `dynamic-policy.ts` mutated. Same pattern as the existing `f-host-3`/`f-host-4` runners.
5. docs-contract will stay FAILED on the `run-mutants` FP until EC6WW lands, so the literal scanner
   contributes no neutrality coverage for new files in the meantime (F3).
6. The historical 4622/0 vs current 4591/31 discrepancy is unexplained and reproducibility is open —
   out of scope here, correctly left open by the report.

## Fix Directions

1. **F1 (do before this table is cited downstream):** reclassify `a04-joined-after-startup` and
   `dynamic-unreadable-join` as `shared witness`, restate `dynamic-selector-core.md:17–19` as
   19 independent / 11 shared / 19 witness-only, and correct the row-accounting table in the report.
   Alternatively give those two rows distinct fixtures and keep 21/9/19. Docs-and-report only; no code
   change, no gate rerun beyond docs-contract (already FAILED) is implied.
2. **F3:** add one sentence to the report stating that `neutral()` fail-fasts on input #1 so the gate
   never reaches the new files, and cite `checker-inputs.json` + the manual read (and, if useful, this
   review's `t2-review-neutrality-probe` receipt) as the actual neutrality evidence. Have EC6WW's rebase
   make `neutral()` collect per-input results.
3. **F2:** carry forward as a known gap into the projector slice; convert to a verdict fixture as soon as
   a reply member exists that `subset()` cannot prove is covered by read.
4. Nothing else. No `src/` change, no §6 stop crossing, and no production constructor/brand/issuance was
   found; the brief's stop list is respected.
