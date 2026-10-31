# Review — TASK-A821A (F-host.2a implementation INPUT)

Range `bff8c46e..e5184b31` (3 commits, docs/test-stub only). Worker report
`/srv/review-drop/TASK-A821A-INPUT.md`, sha256 confirmed
`367bb01305459e1eff0cdbcd399281fb8a2d143779e714372a88926c5cc9012f`, read in full.
Worktree left clean (`git status --porcelain` empty after every probe).

Incorporates the classification from Soma (programme qualifier), passed on by the
task operator Tarn (msg-3d76be5af094458fbaf097b501012171): the `stub-types` gate
run on borrowed TypeScript 5.9.3 is **EXPLORATORY compile evidence only, not a gate
of the host's pinned toolchain**, and must be recorded **PENDING**, not PASS.

## Verdict

**APPROVE** the bounded INPUT, with one gate reclassified **PENDING** and three
MEDIUM follow-ups. No HIGH findings.

The substance holds up under independent checking. All five pinned blob hashes
reproduce exactly, the .2a definition is a verbatim quotation rather than an
invention, the 57-row acceptance table matches the frozen fixture **perfectly**
(ids, order, all five booleans, destination nullity, labels — zero discrepancies),
the dependency pins are real and correctly cited, D/S4Q8F are properly PENDING,
and the bounded-gate INCONCLUSIVE classifications are genuinely unfakeable as PASS.
The brief does not widen 2a.

Two things must be corrected rather than accepted: the `stub-types` receipt is not
toolchain-qualified evidence (F1), and internal harness vocabulary entered tracked
files (F2 — a literal miss against the acceptance criterion "no private
data/internal names in tracked files", contained by the LOCAL ONLY hold and a
trivial scrub, which is why it does not block).

## Findings

### MEDIUM — F1 (test/evidence): `stub-types` is not evidence of the host's pinned toolchain; reclassify PASS → PENDING

`package.json:46` declares `"typescript": "^7.0.2"` (with `bun.lock` /
`package-lock.json`), and `.github/workflows/ci.yml:92-93` typechecks via
`npx tsc --noEmit` — the host's compiler is TypeScript **7.0.2**. The gate instead
ran TS **5.9.3** borrowed from an unrelated project's worktree
(`.../orgasmic/task-v8sb9/ui/node_modules`, report:80); the `task-a821a` worktree
has no `node_modules` of its own (confirmed: no `node_modules/typescript`).

Per Soma's classification this is exploratory compile evidence, **not** a gate of
the declared toolchain. Two concrete corrections follow:

- **report:95 overstates the receipt.** "stub-types: PASS" must read PENDING /
  EXPLORATORY. Neither the brief (`docs/design/f-host-2a-brief.md:202-204`, which
  authorises only "an existing TypeScript compiler") nor the report discloses that
  the compiler is two majors below the repo's own declaration, so a downstream
  implementer reads the stubs as validated against the project compiler when they
  are not.
- **The receipt log cannot substitute.** The `stub-types` log is **zero bytes**
  (status=0), so it cannot even distinguish "typechecked and clean" from
  "typechecked nothing".

Failure scenario: an implementer builds on stub shapes believed compiler-validated
and hits TS 7 diagnostics that 5.9.3 never reported — most plausibly on the four
`@ts-expect-error` directives, which fail the build if the expected diagnostic does
*not* appear.

**TS 7 compatibility risk inventory** (per Soma's request — features the stubs rely
on that are untested under the declared toolchain):

1. `test/stubs/f-host-2a/v0/typecheck.ts:25,27,29,33` — four `@ts-expect-error`
   directives. The most brittle surface: each depends on an exact diagnostic still
   being emitted at that position; an unused directive is itself an error.
2. `stubs.ts:6-11` — `declare const ... : unique symbol` used as a computed
   `readonly` key to build opaque nominal types.
3. `stubs.ts:12-13` — self-recursive type alias `ReadonlyData` through
   `readonly T[]` and a readonly index signature.
4. `stubs.ts:16,23` — `export declare function` generic over an abstract `Admit`
   returned in an object type.
5. `stubs.ts:29` — `AbortSignal`, resolved from the bundled **DOM** lib; lib files
   ship with the compiler, so they differ between 5.9.3 and 7.0.2.
6. `tsconfig.json:7-9` — `"moduleResolution": "Bundler"`, `"types": []`,
   `"lib": ["ES2022","DOM"]` resolution behaviour under the TS 7 native port.

De-risking datapoint, **also exploratory** (a borrowed 7.0.2 install, not the host's
dependency closure, so it does not discharge the gate): under TS 7.0.2 the stub
project typechecks **clean, exit 0**, and a negative control stripping the four
directives produced 5 diagnostics across all 4 sites — so all four are load-bearing
there. This lowers the probability of a real incompatibility; it does not replace a
properly closed gate.

### MEDIUM — F2 (docs/scope): private harness identity introduced into tracked files

- `test/stubs/f-host-2a/bounded-gate.sh:4,9` hard-requires `ORGASMIC_EVIDENCE_DIR`.
- `docs/design/f-host-2a-brief.md:218-221` embeds `orgasmic dispatch gate ...`.
- `docs/design/f-host-2a-brief.md:74,75,243` introduce `INT-1` (3 occurrences).

`git grep -i orgasmic bff8c46e -- .` returns **zero** tracked hits at the base, as
does `INT-1`; this range introduces both. This contradicts report:123 ("New tracked
inputs contain no operator/qualifier/internal names ...; private tool paths exist
only in this evidence report") and the acceptance criterion "no private
data/internal names in tracked files". The worker did scrub `S4Q8F` (zero tracked
hits — confirmed), which marks this as an oversight, not intent.

Critically, the `docs-neutrality` PASS gives **no** assurance here:
`check-scope-contract.mjs:231-235` checks only the (empty) `SCOPE_FORBIDDEN_NAMES`
list, machine-local paths, `TASK|RUN|AGENT|PROJECT-` ids, UUIDs and credential
shapes. Neither `orgasmic`, `ORGASMIC_EVIDENCE_DIR` nor `INT-1` matches any rule,
so all three pass silently.

Secondary, concrete usability effect: the tracked gate script is unrunnable outside
the private harness — `bash test/stubs/f-host-2a/bounded-gate.sh 2s true` exits **2**
("evidence directory required") for any contributor without that variable set.

### MEDIUM — F3 (correctness): the T1 contract checker now crashes if any transient .2a stub file is absent

`test/fixtures/generic-recipient/check-scope-contract.mjs:14-20` reads the five new
.2a files **unconditionally at module load**, before flag parsing at `:22-24`. The
pre-existing T1 `--self-test` and default modes have nothing to do with these stubs,
yet now hard-depend on them. These are explicitly `v0` artifacts with PENDING
packaging (`stubs.ts:3`) that the .2a implementation is expected to replace, so this
will fire in normal course.

Failure scenario, reproduced in a mirror outside the tree — remove one stub file and
the unrelated frozen-contract gate dies on an unhandled stack trace, not a diagnostic:

```
$ rm /tmp/mirror/test/stubs/f-host-2a/v0/typecheck.ts
$ node .../check-scope-contract.mjs --self-test
Error: ENOENT: no such file or directory, open '.../v0/typecheck.ts'   exit=1
```

(Baseline mirror with the file present: exit 0.) This does not *weaken* any existing
check — the diff is strictly additive and every rule function is untouched — but it
couples a frozen T1 gate to a disposable input artifact.

### LOW — F4 (test): a stub type assertion passes for the wrong reason

`test/stubs/f-host-2a/v0/typecheck.ts:29-30` asserts
`// @ts-expect-error Detached descriptor is read-only.` over `descriptor.changed = true`.
In the exploratory 7.0.2 run the diagnostics that actually fire there are `TS18047`
('descriptor' is possibly 'null') and `TS2339` (property does not exist on the
union) — **not** a `TS2540/TS2542` readonly violation. The directive is satisfied
because `ReadonlyData` is an unnarrowed union that rejects any property write, so
readonly-ness is never exercised.

The real risk — mutating a descriptor already narrowed to the object branch — is
unasserted. It *is* enforced; narrowing first yields
`TS2542: Index signature in type '{ readonly [k: string]: ReadonlyData; }' only permits reading.`
So this is a labelling/coverage gap, not a hole in the type. Note this assertion is
also one of the four directives whose TS 7 behaviour is formally untested (F1).

## Verification Gates

Reused receipts (read, not re-run): `docs-neutrality`, `input-diff`, `input-probes`,
`scope-self-test` — exit 0 at `e5184b31`, `clean=yes`. **`stub-types`: reclassified
PENDING / EXPLORATORY per Soma (F1), not counted as a passing gate.**

Independently run by me (all scoped, read-only, tree left clean):

| Check | Result |
| --- | --- |
| 5 pinned blob sha256 + `merge-base` | **All match the brief exactly**, incl. the two SHAs supplied independently in the assignment; host pin is an ancestor of the T1 pin |
| `check-scope-contract.mjs --self-test` (timeout 60) | exit 0 — 57 cases, 45 rejection controls, 4 named oracle mutants killed, singleton negative control, naming-parser control |
| `check-scope-contract.mjs --docs-contract` (timeout 60) | exit 0 — explicit `PARTIAL naming check`, 9 public files, 8 rejected controls. PARTIAL semantics intact |
| no-flag default mode | exit 0 |
| **57-row table vs `scope-cases.json`@bff8c46e** (programmatic; `probe-table-vs-fixture.{mjs,log}`) | **ZERO discrepancies** — 57/57 ids, identical order, all 5 booleans, destination nullity, destination key shape, every `scope/<id>` label |
| bounded-gate: deadline / SIGKILL / cap / plain fail / success | 124→INCONCLUSIVE exit 124; 137→INCONCLUSIVE exit 137; cap→INCONCLUSIVE exit 125; exit 3 passthrough (no INCONCLUSIVE); success exit 0. **Never PASS on 124/137/141/cap** |
| Production imports of stubs (`grep` over repo) | **None** — only the neutrality checker reads them as *text*, never imports |
| Checker diff review | Single additive hunk; `neutral()`, `parseForbiddenNames`, PARTIAL branch all unmodified |
| Stub typecheck under a borrowed TS 7.0.2 + strip-directives negative control | **EXPLORATORY only** (borrowed install, not the host closure): exit 0 clean; all 4 directives load-bearing. Informs F1's risk inventory; discharges nothing |

My probes carry no dispatch receipts by design (scoped probes, not assigned gates).

Checks 1, 3, 4, 6, 7 and the "no weakening" half of 5 passed with no findings:
- **(1)** `generic-recipient-host.md:683` @ `a316200c` quotes verbatim; `:668-673` is
  the normative contract reference; `generic-recipient-fixtures.md:13-53,76-92`
  substantiates every element of the brief's goal paragraph (two peers, JSON-RPC
  stdio + loopback WS, pinned AF tarball, local fake providers, side control pipe,
  protocol-only stdout, no sleeps, entered/release barriers, ordered deltas).
  **Not widened**: T3/T4/T5 explicitly out of scope (`brief:34-40`), T01-T03 deferred
  to T4 and A04 parity to T5 (`:124`), H04-H12 disclaimed (`:189-190`).
- **(4)** A `869bb3ad` and B `29edcf8b` are cited verbatim in T1`:17-18`; the brief's
  "(T1 `:17-20`)" for B's *required* synchronous `authorize` is exact. D is PENDING
  and the brief correctly **refuses** the historical `a5b02a07` head that
  `host.md:20` still lists. S4Q8F view PENDING; `brief:76` explicitly states the
  unreviewed head is not a consumption pin. No unreviewed head used as a pin.
- **(2)** Every stub symbol traces to T1`:303-313`; `Admit`/`Deny` left abstract; no
  packaging, fields or methods invented; `RegistrationProjector` is flagged
  in-comment as a local alias, not an AF export name. "STUB, not integration
  evidence" header present at `stubs.ts:1`.
- **(7)** `brief:233-239` states LOCAL ONLY, no push/no proposed push, fresh clean
  commit/tree on a public base, privacy check of the **whole** publication range,
  private refs kept, and that the local check is not publication approval — fully
  matching the Soma/Tarn guard. Operator/qualifier/owner names, `/home/` paths,
  `task-v8sb9`, `S4Q8F`, `TASK-A821A`, run ids: **zero** occurrences in tracked files
  (F2 covers what *is* present).

## Unmet Criteria / test gaps

- **`stub-types` is PENDING, not satisfied** (F1): no typecheck evidence exists under
  the host's declared toolchain/closure. The stubs' *content* is independently
  verified by reading against T1`:303-313`; only the compile gate is open.
- **Acceptance "no private data/internal names in tracked files": not met** (F2).
  The other three acceptance criteria are met.
- Neutrality evidence is **PARTIAL only**, correctly disclosed by the worker
  (`report:122`, `brief:224-229`) — but F2 shows that gap is not theoretical: real
  internal tokens are present and uncatchable by the current rules.
- Readonly-ness of a narrowed descriptor is unasserted (F4).
- As designed and disclosed: no integration, production-path, native, store or live
  evidence exists. The 57 rows are **planned** tests; nothing here executes them
  against an implementation.

## Residual Risk

- The six TS-7 surfaces listed in F1 remain unqualified until the typecheck is rerun
  in an isolated code-only setup with the host's declared toolchain. The
  `@ts-expect-error` directives are the likeliest failure point.
- **Framing risk for the next implementer:** the table's heading "Acceptance cases
  and evidence levels" lists all 57 scope cases, while the slice definition
  (`host.md:683`) covers only the synthetic peer/harness and real startup/transport
  controls. The brief *does* bound this (`:37-40`, `:87-88`, `:124-127`), but a
  reader skimming to the table could take 57 behaviour cells as .2a's own exit gate
  and pull T2-T5 work into the slice. Worth one explicit line above the table.
- INT-1 (D + composed artifact, tarball/SRI/manifest) and the reviewed S4Q8F head
  remain genuinely blocking for any .2a implementation; nothing here pre-empts them.
- Final projector denial representation, `VerifiedScope` field spelling and AF
  packaging stay open — correctly not frozen by the stubs.
- The LOCAL ONLY ancestry hold is the only thing containing F2; if any part of this
  tree is published without the full-range privacy check, the harness name ships.
- `SCOPE_FORBIDDEN_NAMES` must include harness/tooling vocabulary, or the private
  publication gate will repeat F2's blind spot at publication time.

## Fix Directions

1. **F1** — record `stub-types` as **PENDING**; correct report:95. Rerun **only** the
   affected typecheck with the host's declared toolchain/closure in an isolated
   code-only setup (stub sources + a `typescript@7.0.2` resolution from the host's own
   lockfile, no project source, no network beyond that install, no emit).
   Per Soma: do **not** point `TSC_JS` at another worktree, symlink or otherwise
   borrow a cross-worktree dependency, and do **not** edit the stubs to fit 5.9.3.
   Amend `brief:202-204` to require the compiler major to match `package.json`'s
   `typescript` declaration and to record version *plus* the declaration it satisfies.
   Carry the six-item risk inventory above into the brief until the gate closes.
2. **F2** — remove harness vocabulary from tracked files: take the log directory as
   `$1`/`BOUNDED_LOG_DIR` in `bounded-gate.sh:4,9` with a neutral fallback (e.g.
   `mktemp -d`), and write the brief's command block as the bare
   `bash test/stubs/f-host-2a/bounded-gate.sh ...`, keeping the orchestration prefix
   in private evidence only. Replace `INT-1` with a neutral phrase ("the composed
   A+B+D integration release"). Add rules to `check-scope-contract.mjs:230-236` for
   tooling/programme identifiers so this class is caught structurally rather than via
   an injected list. Correct report:123.
3. **F3** — make the five .2a reads lazy and failure-tolerant: move them inside
   `docsContract()` and either skip-with-notice or `assert` with a clear message when
   absent, so `--self-test` stays independent of disposable stub files.
4. **F4** — narrow before asserting, so the directive witnesses `TS2542`:
   `declare const o: { readonly [k: string]: ReadonlyData }; // @ts-expect-error ... o.changed = true;`
   Re-verify under the host's toolchain as part of fix 1, not separately.
