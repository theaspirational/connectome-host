# TASK-EC6WW fix round 2 — independent delta review (LOCAL ONLY)

Range `698bb5870116d14e45ae5e78ff1bdf37fb95c144..8d211982eaaf59b83713ebe236e5c5c91715e00e`
(`c77c54c5` intermediate, superseded). Reviewed report
`/srv/review-drop/TASK-EC6WW-FIX2.md`, SHA-256 verified
`6b0dabd0856ef91093eae3e326955af7384dc804e53758825e06c981a90b5948`.
Previous review `/srv/review-drop/TASK-EC6WW-REVIEW1.md` read for context.
Format contract re-derived read-only from `/home/orgasmic/src/orgasmic`
(code/spec only; no ledger, history or credentials read).

## Verdict

**APPROVE.** No HIGH. The amended scope is met: the minted TASK/CONV stem shape
and the compact RUN ULID shape are reproduced **exactly** as the source defines
them, separator adjacency works, no other detector was weakened, no exemption or
input was added beyond the H brief, and the diff and commit messages are
neutral. Both named gates reproduce PASS independently.

Four MEDIUM findings are honest-description and test-coverage gaps, not defects
in the shipped predicate; three LOW are limitations. Nothing blocks the fix
round.

The initial m1/m4 failures at `c77c54c5` classify as **harness/preflight
expectation errors, not surviving mutants** — same checker and mutant digests in
attempt and rerun, expectation-only harness diff, and the mutated checker already
exited 1 on a named assertion in attempt 1. Full binding of receipts to attempts
below.

**Scope of this PASS:** it describes **only** the tested predicate
(`internalIdentifier`, checker:233-238) over the **18 enumerated docs-contract
inputs**. It is not a universal-privacy, privacy-clean, production-qualification
or publication-approval claim. History is **not** cleaned (see Residual Risk).

## Format contract — verified exact (brief point 1)

| Contract element | Source | Checker | Match |
| --- | --- | --- | --- |
| Node stem length | `id.rs:15` `STEM_LEN = 5` | `{5}` at checker:233 | exact |
| Node alphabet | `id.rs:13` `"0123456789ABCDEFGHJKMNPQRSTVWXYZ"`, `id.rs:404-406` `is_crockford_char` excludes I/L/O/U | `[0-9A-HJKMNP-TV-Z]` expands to digits + exactly those 22 letters | exact |
| At least one letter | `id.rs:375-395` (`random_stem` re-rolls), `id.rs:398-402` `is_minted_stem` | lookahead `(?=[…]{0,4}[A-HJKMNP-TV-Z])` | exact |
| `TASK-` prefix | `id.rs:29`, `shipped/plugins/tasks/plugin.org:25` | alternation at checker:233 | exact |
| `CONV-` prefix | `shipped/schema/node-types/conversation.org:7` | alternation at checker:233 | exact |
| Run id = `run-` + ULID | `run_id.rs:11,19-29`, `compact_run_id_token` len check `run_id.rs:35-40` | `RUN-` at checker:234 | exact |
| ULID length 26 | `ulid-3.0.0/src/base32.rs:4` `ULID_LEN = 26` | `[0-7]` + `{25}` = 26 | exact |
| ULID alphabet | `ulid-3.0.0/src/base32.rs:6` same Crockford table | `[0-9A-HJKMNP-TV-Z]` | exact |
| ULID first char 0-7 | 128-bit value, 26×5 = 130 bits → leading char carries 3 bits (`base32.rs:60-65`) | `[0-7]` | exact, and a correct superset |

Behavioural spot checks (`probe-boundaries.mjs`): `task-01234` and `TASK-01234`
are still caught by the retained digit branch, so the legacy numeric family is
not lost; the legacy `run-<timestamp>-<uuid>` form is caught.

## Findings

### MEDIUM — the collision limitation is a class, not one word (checker:279-283; report "Controls and unavoidable collision")

The report and the in-code comment present the collision as the single word
`task-based` ("The unavoidable task-based collision"). The actual rejected set is
**every** `task-`/`conv-` + 5-character token whose letters avoid i/l/o/u.
Measured (`probe-collision.mjs`):

* 32 of 46 plausible technical compounds collide, including `task-state`,
  `task-graph`, `task-event`, `task-stage`, `task-ready`, `task-types`,
  `task-trace`, `task-start`, `task-steps`, `task-paths`, `task-depth`,
  `task-cache`, `task-phase`, `task-entry`, `task-edges`, `task-dates`,
  `task-names`, `task-gates`. (`task-queue`, `task-level`, `task-scope`,
  `task-nodes`, `task-order`, `task-group`, `task-count`, `task-label`,
  `task-value`, `task-token`, `task-chain`, `task-write`, `task-notes` are safe
  — the four excluded letters are what saves them, not any rule about words.)
* 270 of 927 (**29.1%**) distinct 5-letter lowercase words appearing in this
  repository's own prose collide as `task-<word>`.

No false FAIL today: I confirmed **0** occurrences of any `task-`/`conv-` +
5-Crockford token across the 18 docs-contract inputs
(`probe-18-inputs.mjs`), and `grep` finds only `task-ec6ww` repo-wide, inside
`.git` (untracked, not an input). But a future doc writing `task-graph` or
`task-state` in prose will FAIL this gate, and whoever reads that FAIL will have
only the `task-based` framing to go on.

*Fix direction:* state the class in the comment at checker:279 and in the report
("any 5-char token after `task-`/`conv-` without i/l/o/u"); add `task-state` and
`task-graph` as named collision controls so the breadth is visible in a failure.

### MEDIUM — the named remaining limitation omits six real minted families (checker:231-232; report "REMAINING LIMITATION")

The comment and report name only AGENT/PROJECT, which are **not** minted prefixes
anywhere in the source. The families the source actually mints are
(`shipped/` `:ID_PREFIX:` lines):

`TASK-` (tasks/plugin.org:25) · `CONV-` (node-types/conversation.org:7) ·
`ART-` (artifacts/plugin.org:20) · `CMT-` (node-types/comment.org:7) ·
`SEC-` (node-types/secret.org:7) · `dec_` (decisions/plugin.org:13) ·
`term_` (glossary/plugin.org:13) · `gotcha-` (gotchas/plugin.org:13) ·
`MEET-` (orgasmic-plugin-author/SKILL.md:158), all with the same
`random_stem()` 5-char stem (`node_type.rs:323-325`). `id.rs:27-34` lists
`TASK-`/`dec_`/`term_`/`ART-` as first-class node classes.

All six uncovered families are MISS in the detector (`probe-boundaries.mjs`):
`ART-ABCDE`, `art-abcde`, `CMT-ABCDE`, `SEC-ABCDE`, `dec_073a`, `dec_ABCDE`,
`term_ABCDE`, `gotcha-ABCDE`, `MEET-ABCDE`. `dec_073a` is a literal id in
`id.rs:1`. None appear in the 18 inputs today (verified, 0 occurrences each).

Named as a limitation per the brief; **no fix demanded**. The honest-description
gap is that the stated limitation points at two non-existent families while
omitting six existing ones.

### MEDIUM — the "at least one letter" clause has zero discriminating control (checker:233)

I built and ran mutant **r5**: delete the lookahead
`(?=[0-9A-HJKMNP-TV-Z]{0,4}[A-HJKMNP-TV-Z])`. It **survives** — the full
`--self-test` exits **0**
(`extra-mutants.mjs r5`, mutant SHA-256
`03fc818cb0cf2517bebc640463933702021e0964acf374a1e6535487737e90f7`).

The clause is observable, so this is a live uncovered contract element, not an
equivalent mutant: `x_task-01234_x` → HEAD `false`, r5 `true`
(underscore isolation defeats the `\b` digit branch, leaving only the shape
branch). The clause is also correct — a 5-digit stem is neither minted
(`random_stem` re-rolls) nor a legacy numeric task stem — so this is a test gap
only.

*Fix direction:* add `['non-shape node all-digit stem task', ['x_task', '01234_x'], false]`.

### MEDIUM — the new control block can fail the private gate for a naming reason, reported as an identifier failure (checker:313-318)

`identifierControls()` now runs inside `docsContract()` (checker:408) and asserts
`neutral(sample)` on each benign sample. `neutral` includes the
`SCOPE_FORBIDDEN_NAMES` substring check (checker:242), and the benign samples are
built from plain English words — `model`, `overlay`, `config`, `root`, `queue`,
`level`, `mutants`, `conversation`, `based`. Reproduced:

```
SCOPE_FORBIDDEN_NAMES=model        -> exit=1  identifier scan control: ordinary filename agent model
SCOPE_FORBIDDEN_NAMES=overlay      -> exit=1  identifier scan control: ordinary filename agent overlay
SCOPE_FORBIDDEN_NAMES=conversation -> exit=1  identifier scan control: ordinary conversation word
SCOPE_FORBIDDEN_NAMES=zebra        -> exit=0  PASS docs-contract: 18 public files; 12 rejected controls; 1 injected names
```

Both recorded `docs-contract` receipts (implementer's and mine) ran with **0**
injected names and `PARTIAL` naming, so this interaction is untested in the
configuration the private publication gate actually uses — the gate this task
exists to unblock. The failure message blames the identifier controls.

*Fix direction:* assert the predicate only for benign samples
(`assert.equal(internalIdentifier(sample), false, …)`), or exclude the
forbidden-name check from the control path.

### LOW — the %2F question: NO, percent-encoded adjacency is not caught (checker:233-234)

Brief point 2 asked directly. Probe results (`probe-boundaries.mjs`):

```
MISS   orgasmic%3A%2F%2Fproj%2FTASK-ABCDE
MISS   orgasmic%3a%2f%2fproj%2ftask-abcde
MISS   proj%2FTASK-A1B2C
MISS   proj%2FRUN-0ZZZZZZZZZZZZZZZZZZZZZZZZZ
MISS   1TASK-ABCDE          (any trailing alphanumeric)
CATCH  TASK-ABCDE%2Fx       (trailing side is fine)
CATCH  https://x/proj/TASK-ABCDE, https://x/?node=TASK-ABCDE, orgasmic://proj/TASK-ABCDE
```

Root cause: `%XX` always ends in a hex digit, and *both* the lookbehind
`(?<![A-Za-z0-9])` and the `\b` in the two retained word-boundary branches treat
it as a word character — so all three branches fail together.
`canonical_uri_segment` (`id.rs:91-101`) makes `%XX`-encoded node references a
real spelling in this product. The report discloses this only generically
("encoded/reconstructed IDs … remain limitations") with no control.

*Fix direction:* name `%XX`-adjacency concretely, and add one control
`['%2Ftask', 'zzzzz']` pinning whichever behaviour is intended.

### LOW — narrowing regression vs base, controlled and documented (checker:236-237)

The base assert at `c353e1c8` was `/iu`; the uppercase branch is now `/u`, so
`agent-abcde`, `project-abcde` and `run-abcde` (lowercase, all-letter) were
CATCH at base and are MISS at HEAD. This is the task's intent and is pinned by
the two named `remaining limitation:` controls (checker:307-308); no such prefix
is minted in the source. Noted for completeness only.

### LOW — Unicode edges behave in the safe direction (no fix)

`ätask-zzzzz`, `аtask-zzzzz` (Cyrillic), `—task-zzzzz—`, `task-zzzzzä` → CATCH:
the lookbehind only excludes ASCII alnum, so non-ASCII adjacency over-detects.
`/iu` case folding makes U+212A KELVIN SIGN and U+017F LONG S match inside a stem
(`task-zzKzz`, `task-zzſzz` → CATCH) — over-detection only. `task-zzızz` /
`task-zzİzz` → MISS, but ı/İ are not Crockford characters, so no real id is
affected. No finding; the earlier review's boundary table is not reopened.

## Verification Gates

Run by me, `orgasmic dispatch gate`, bounded
`timeout --signal=TERM --kill-after=60s 600s`, node
`/opt/node-v22.23.3-linux-x64/bin/node`, no retries, no installs, worktree left
clean (`git status --porcelain` empty before and after).

| Gate | Command | Result | Log |
| --- | --- | --- | --- |
| `rv-docs-contract` | `… check-scope-contract.mjs --docs-contract` | **exit 0** — `PASS docs-contract: 18 public files; 11 rejected controls; 0 injected names`; `PARTIAL naming check` | `gate-rv-docs-contract-01M68A9A6F1YRQFZWY6P1CCYKD.log` |
| `rv-self-test` | `… check-scope-contract.mjs --self-test` | **exit 0** — all identifier controls, 45 schema rejection controls, 4 oracle mutants, singleton negative control | `gate-rv-self-test-01M68A9EQPP25N4ZABHAMYM49S.log` |

Independent probes (unwrapped, no reusable receipt), all in
`$ORGASMIC_EVIDENCE_DIR`: `probe-boundaries.mjs`, `probe-collision.mjs`,
`probe-18-inputs.mjs`, `rv-neutrality.mjs` (+ `rv-net.diff`, `rv-messages.txt`),
`extra-mutants.mjs` (+ `r1..r6.checker.mjs`).

### Extra mutants I built and ran (brief point 4)

Source checker SHA-256 `02e8cf6bba14e59638468aff0f438823e0b67c377b5172df576099498fa4bfce`
(matches the report's claimed H digest).

| Mutant | Change | Outcome |
| --- | --- | --- |
| r1 | node alphabet widened to `[0-9A-Z]` (I/L/O/U admitted) | **KILLED** exit 1 — `identifier detector control: ordinary filename task queue` (widening makes `task-queue` a false positive; the alphabet restriction is load-bearing) |
| r2 | node stem length `{5}` → `{4,6}` | **KILLED** exit 1 — `identifier detector control: non-shape node too short task` |
| r3 | ULID alphabet widened to `[0-9A-Z]` | **KILLED** exit 1 — `identifier detector control: non-shape run excluded letter` |
| r4 | ULID first-char bound `[0-7]` dropped | **KILLED** exit 1 — `identifier detector control: non-shape run overflow` |
| r6 | shape alternation widened to `AGENT|PROJECT` | **KILLED** exit 1 — `identifier detector control: remaining limitation: agent all-letter suffix` |
| r5 | "at least one letter" lookahead deleted | **SURVIVES**, exit 0 — see MEDIUM above |

The brief's two named hypotheticals ("alphabet including I/L/O/U", "length
{4,6}") are therefore both covered. m1-m9 receipts read, not rerun: each records
exit 1 with its named assertion; the m1/m4 failure receipts
(`gate-r2-m1-01M689SB02VJXGJ3DA5YFHJETD.log`,
`gate-r2-m4-01M689SBGR82CHRAJYH05H0FVH.log`) are present, unrelabelled, at
`c77c54c5` with `clean=yes`, exactly as the report describes. One survivor out of
15 mutants examined.

### Classification of the initial m1/m4 failures at `c77c54c5` (addition from Soma via Tarn)

Both attempts were **harness/preflight expectation errors, not surviving
mutants.** Proven, not inferred:

| | m1 attempt 1 | m1 rerun | m4 attempt 1 | m4 rerun |
| --- | --- | --- | --- | --- |
| Receipt | `gate-r2-m1-01M689SB02VJXGJ3DA5YFHJETD.log` | `gate-r2-m1-expectation-corrected-01M689V4V57AECPFDWKGSRHYXT.log` | `gate-r2-m4-01M689SBGR82CHRAJYH05H0FVH.log` | `gate-r2-m4-expectation-corrected-01M689V50214SXB06KXWREH528.log` |
| Gate exit | **1 (FAILED)** | 0 | **1 (FAILED)** | 0 |
| HEAD / tree | `c77c54c5`, clean | `c77c54c5`, clean | `c77c54c5`, clean | `c77c54c5`, clean |
| Checker SHA-256 | `18b82e77…` | `18b82e77…` | `18b82e77…` | `18b82e77…` |
| Mutant SHA-256 | `f837bfce…` | `f837bfce…` | `46cbe2e2…` | `46cbe2e2…` |
| Harness | `identifier-mutants.initial.mjs` | `identifier-mutants.pre-shapes.mjs` | `.initial.mjs` | `.pre-shapes.mjs` |
| Mutated checker's own exit | **1** (assertion) | 1 | **1** (assertion) | 1 |
| Mutated checker's first named assertion | `identifier detector control: known false negative: lowercase all-letter suffix` | identical | `identifier scan control: known false negative: lowercase all-letter suffix` | identical |
| Why the gate failed | harness `assert(stderr.includes(kill))` → `missing named kill: … ordinary filename run mutants` | — | same, `… scan control: ordinary filename run mutants` | — |

**Inputs were identical between each attempt and its rerun.** Same checker
digest, same mutant digest, and `diff -u identifier-mutants.initial.mjs
identifier-mutants.pre-shapes.mjs` is **exactly two lines** — the m1 and m4
`kill` strings. No mutant construction, no spawn arguments, no checker byte
changed. The harness repair diff is therefore bound to the attempts as an
expectation-only edit.

**No earlier survivor exists to be erased.** In `identifier-mutants.mjs` the
status assertion (`assert.equal(result.status, 1, 'mutant must fail with an
assertion')`) precedes the kill-name assertion. Attempt 1 failed on
`missing named kill`, which is only reachable after the status assertion passed —
so the mutated checker already exited 1 on an assertion in attempt 1. Both raw
logs show that assertion by name. The mutant was killed in attempt 1 as well;
only the harness's *prediction of which named control would kill it* was wrong.

**The new control really is the one each mutant violates** (operator Tarn's
condition). At `c77c54c5` the control list added
`['known false negative: lowercase all-letter suffix', ['task', 'abcde'], false]`
(`c77c54c5:270`). m1 restores the broad `/iu` rule, which detects `task-abcde` and
therefore violates that `false` expectation directly; m4 restores the broad rule
at the scan consumer, so `neutral('task-abcde')` throws `internal identifier`
where the control asserts `doesNotThrow` (visible in `r2-m4.raw.log` as
`Got unwanted exception: … actual: … internal identifier`). Both are genuine
kills by the earlier-ordered new control, so the semantics of the kill are
unchanged — consistent with the report's claim.

**Scope limit on these receipts.** All four are at identity `c77c54c5`, which the
scope change superseded; they do not establish anything at H, and a pass at
`c77c54c5` plus a pass at H is not a stability argument for either. The
authoritative m1/m4 evidence at H is `gate-shapes-m1-…` and `gate-shapes-m4-…`
(both exit 0 at `8d211982`, clean), where the expected kill names are back to
`ordinary filename run mutants` because the scope change **deleted** the four
`known false negative:` controls (`grep` finds 0 at HEAD, 4 at
`c77c54c5:270-273`). The kill name thus moved twice *because the control set
itself changed*, not because the expectation was tuned to whatever happened to
pass — `identifier-mutants.pre-shapes.mjs` → `identifier-mutants.mjs` reverts the
two names and adds m6-m9, matching the control deletion.

**My own independent rerun (one, labelled).** Gate `rv-m1-independent` at
`8d211982`: `exited:0`, source checker SHA-256
`02e8cf6bba14e59638468aff0f438823e0b67c377b5172df576099498fa4bfce`, mutant
SHA-256 `2d0330e3ed0b1efb0ffa84e3826517fb296fa290376330067f39a20c728b6298` —
bit-identical to the implementer's `shapes-m1` receipt —
`PASS mutant killed: m1 -> identifier detector control: ordinary filename run
mutants; checker exit=1`. Log
`gate-rv-m1-independent-01M68AJA08AVQGBS15Z306VN8G.log`. m4 was inspected only,
not rerun.

As instructed: the `task-based` collision class and the uncovered minted families
remain **limitations**, and this PASS describes only the actual controls and the
18 enumerated inputs.

### Inputs and other detectors (brief point 5)

* 18 docs-contract inputs = 2 `publicTexts` (checker:12) + 16 paths
  (checker:410-427). Verified by gate output and by enumeration.
* Across the whole range `c353e1c8..HEAD` the **only** input-list change is
  `+ '../../../docs/design/f-host-h-brief.md'` (checker:414). Nothing removed.
  In `698bb587..HEAD` the list is byte-identical.
* `neutral()` (checker:239-248) is otherwise untouched: tooling-environment,
  tooling-CLI, programme-integration, forbidden-names, machine-local-path,
  UUID and credential-shaped checks are unchanged; the only edit replaced the
  inline identifier regex with the `internalIdentifier()` call.
* `rejectsMutation` list unchanged (45 controls); 11 runtime denial controls
  unchanged. **No exemption, allowlist or skip was added anywhere.**

### Neutrality of diff and messages (brief point 6)

`rv-neutrality.mjs` scanned (a) the net diff `c353e1c8..HEAD`, (b) all four
commit messages, and (c) every string literal on added lines **reconstructed**
by joining them with `''`, `'-'`, `'_'` and `'/'`, against 11 families: minted
TASK/CONV shape, compact RUN ULID, legacy `run-<ts>-<uuid>`, `run-01…`,
`ART|CMT|SEC|MEET`, `dec_|term_|gotcha-`, UUID, machine-local path, tooling env,
credential-shaped, and any bare 26-char Crockford ULID.

* diff: **0 hits in all 11 families**. messages: **0 hits in all 11 families**.
  Authors/committers are the expected `Vasyl Dovhanchyn` noreply identity with
  the `GPT-6.1-Sol` co-author trailer.
* reconstructed literals: 6 shape hits, all demonstrably synthetic —
  `task-0abc0`, `conv-0abc0`, `task-zzzzz`, `TASK-zzzzz`, `conv-zzzzz`,
  `task-based`. The full 99 distinct added literals are listed in the probe
  output; none is a real value.

**r1 history confirmed still a known positive, not cleaned.** `a5017576` is an
ancestor of HEAD (`git merge-base --is-ancestor` → yes) and its added lines at
`a5017576:35-36` carry a real compact run ULID and a real-looking 5-character
task stem, as *split* literals — which is why a whole-string scan reports 0 and
why split/reconstructed search was the right instruction. Values not reproduced
here. The report's Residual Risk states this accurately.

## Unmet Criteria / test gaps

No unmet criterion under the amended scope. Test gaps:

1. The "at least one letter" clause — mutant r5 survives (MEDIUM above).
2. No control for percent-encoded / alphanumeric-adjacent prefixes (LOW above).
3. No control runs with a nonempty `SCOPE_FORBIDDEN_NAMES`; naming coverage is
   `PARTIAL` in every receipt, and the new control block is untested under the
   production configuration (MEDIUM above).
4. No control for `ART-`/`CMT-`/`SEC-`/`dec_`/`term_`/`gotcha-`; the limitation
   is unpinned, so a later widening of those families has nothing to discriminate.
5. The 29.1%-of-ordinary-words collision class is pinned by exactly one sample
   (`task-based`), so the control suite does not reveal its breadth.
6. Not checked and not in scope: the private gate's real behaviour with its
   actual naming list, any input outside the 18, and any non-ASCII-id question
   beyond the adjacency probes above.

## Residual Risk

* **History is not cleaned** — independently confirmed, as the report states.
  `a5017576` remains an ancestor of HEAD carrying real identifiers; no rewrite,
  squash or deletion occurred and private refs remain. This review does not
  sanitize `/srv/review-drop/TASK-EC6WW-IMPL1.md` or prior evidence either.
* **PASS is narrow.** It covers only `internalIdentifier` over the 18 enumerated
  inputs. It is not a universal-privacy or publication-approval claim, and the
  18-input scan at HEAD proves nothing about any other document.
* The predicate rejects a broad class of ordinary English compounds and misses
  six minted families, `%XX`-adjacent ids, split/reconstructed literals, and
  lowercase all-letter `agent-`/`project-` forms. As one publication-gate input
  it is usefully conservative in the TASK/CONV/RUN families and quiet elsewhere.
* No publication, push, PR, merge, install or live action was taken. Nothing was
  written inside the worktree; it is byte-identical to how I found it. LOCAL ONLY.

## Fix Directions

Ordered; none blocking this round.

1. checker:313-318 — assert only `internalIdentifier(sample) === false` for
   benign samples, so a `SCOPE_FORBIDDEN_NAMES` word cannot fail the gate under
   an identifier label.
2. checker:233 — add `['x_task', '01234_x'] → false` to pin the
   at-least-one-letter clause (kills r5).
3. checker:231-232 and the report — replace the AGENT/PROJECT wording with the
   six minted families that actually exist (`ART-`, `CMT-`, `SEC-`, `dec_`,
   `term_`, `gotcha-`, plus `MEET-` in the plugin-author skill), citing the
   `shipped/` `:ID_PREFIX:` lines.
4. checker:279 and the report — describe the collision as a class, not
   `task-based`; add `task-state` and `task-graph` controls.
5. checker:233-234 — name `%XX`-adjacency explicitly and add one control fixing
   the intended behaviour either way.
