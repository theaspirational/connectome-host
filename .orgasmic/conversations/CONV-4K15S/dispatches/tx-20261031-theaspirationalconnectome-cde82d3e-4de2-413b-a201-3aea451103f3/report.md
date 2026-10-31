# CONV-4K15S — Independent read-only check of A2 v1.1 (TASK-ZE2GZ)

Reviewer dispatch, targeted. Operator Tarn; qualifier Soma 1793446672 as relayed by the
dispatch (not independently fetched). **This is not acceptance, not a gate PASS, and carries
no authority.** No edit, commit, Git write, install, fetch, network, build, node/npm/bun/tsc
or test ran; all Git reads used `GIT_OPTIONAL_LOCKS=0`. The worker's helper scripts were read
but **not executed**. Writes went only to `$ORGASMIC_EVIDENCE_DIR`.

## Verdict

**All ten brief items CLOSED.** Every correction REVIEW1 required is present, and every
substantive claim I could test independently reproduced — including the two MEDIUMs, which I
recounted and re-derived from source rather than reading off the worker's figures. REVIEW1's
"What must hold" conditions **1, 2 and 5 are MET**.

New findings: **0 HIGH, 0 MEDIUM, 3 LOW + 1 trivial** — all documentation-precision or
traceability, none affecting a correction's validity and none blocking.

Two results are worth flagging as unusually strong corroboration:
- My own citation checker, written independently of the worker's, reproduced its headline
  figure **exactly** (275 locators / 311 spans / 145 files over FACTS.md) and found **zero**
  out-of-bounds and **zero** all-blank spans across all three sealed docs (284/322/145).
- I content-verified **all 141** inventory rows and **all 54** name-map rows against real
  source at the pinned revisions, not a sample.

## Findings

### LOW-1 (docs/correctness) `A2-PLAN.md:196–197`: the packed-host exclusion rests on a git-tracking criterion, but pack inclusion is path-based
The plan states P lives in an "**untracked**, private local staging subtree, outside the
tracked source tree", and FACTS.md:273 concludes a packed host "would point to an archive it
does not carry". Pack inclusion is governed by `H:package.json:9–18`, whose entries include
whole directories (`src`, `recipes`, `scripts`, `docs`, `dist/web`), and npm pack is
path-based, not git-aware — there is no root `.npmignore` (verified: only `.gitignore` is
tracked at HEAD). So "untracked" alone would **not** keep P out of a pack if it were staged
under any of those four directory entries. The conclusion holds under the plan's evident
intent ("outside the tracked source tree" implies outside `src/`), but the criterion actually
written is not the criterion that governs. Since the later scope must produce *entry-prevention
evidence* for exactly this path, the distinction matters there.
*Fix direction:* state the exclusion against the allowlist directly — P staged outside
`src/`, `recipes/`, `scripts/`, `docs/`, `dist/web/` — rather than (or in addition to) "untracked".

### LOW-2 (docs) `report.md:55–57`: the self-check figure is not scoped to the one file the script actually scans
The report presents "**275 locators / 311 cited spans / 145 files**; every cited span is in
bounds **AND non-blank**" as a property of the round. The script's citation loop reads
`FACTS.md` only (`a2-v11-doc-check.py:30`); A2-PLAN.md's 3 cites and OBSERVATIONS.txt's 6 are
outside it. **Immaterial in substance** — my checker ran all three docs and those 9 extra
cites also pass — but as written the claim overstates the script's coverage.
*Fix direction:* say "across FACTS.md", or extend the loop to all three sealed docs.

### LOW-3 (docs/traceability) `report.md:63–65`: the gate log named for the seal records a superseded manifest digest
The report names `gate-a2-v11-seal-01M68WB10SZ7TEQTK0VJH4MZSQ.log` as "the first immutable
gate log". That log reports `SHA256SUMS_SHA256=21728cdc39ca059afd5d4439bd1d4b9299732a4e020fa134dc49b899b1fd528a`
— **not** the delivered manifest digest. The authoritative `4c0dac68…` appears only in the
second gate log (`…01M68WBVX121K7JW1D6YN4NJ89.log`) and in `a2-v11-seal.log`, neither of which
the report names by filename. The report *does* disclose the rerun honestly ("The seal is
rerun after inserting this outcome and regenerating the manifest"), so this is a navigation
hazard, not a misstatement: an auditor who opens the one log the report names by name reads a
stale digest. Both seal invocations exited 0 and the final state verifies 7/7.
*Fix direction:* name the final gate log alongside the first.

### Trivial (docs) `CHANGES.md:23`: unresolvable shorthand citation
`H:ci.yml:46–50,89–90,149–152` abbreviates the path; under FACTS.md's own `pin:path:line`
convention it does not resolve (the real path is `.github/workflows/ci.yml`, which A2-PLAN.md
writes out in full). This is the only non-resolving locator in the whole drop and it is in the
delivery ledger, not a sealed fact document.

## Item-by-item result

| # | Item | Result | Independent evidence |
| --- | --- | --- | --- |
| 1 | MEDIUM-1 updater/packed-host fail-closed | **CLOSED** | `install.ts:95` copies package.json, `:96` one lock, `:101` runs manager in tmp, `:103–110` throws `ContractError` on nonzero → induced failure is real. `package.json:9–18` allowlist carries no AF artifact dir. Named as induced failure in §3 **and** as a §4 exit requirement. Refusal is required "before any package-manager or lifecycle action, rather than rely on an eventual missing-file error after execution" — genuinely pre-manager. Forbids registry fallback, removed dependency check, and committed/published/distributed vendored binary; "No updater/files-allowlist edit is authorized by this docs round." |
| 2 | MEDIUM-2 counts 43/1/70=114, 141 | **CLOSED, recounted** | `git grep` → 118 AF-mentioning files; 4 string-only (`scripts/lockfiles-agree.mjs`, `src/bug-report.ts`, `src/history-semantic.ts`, `test/lockfiles-agree.test.ts`); import-bearing = **43 src/ + 1 scripts/ (warmup-session.ts) + 70 test/ = 114**, **141** specifier occurrences. The FACTS table itself = **141 rows / 114 distinct files**, set byte-identical to my enumeration. Parenthetical 44 = 43+1. Consistent in FACTS, OBSERVATIONS, report.md, CHANGES.md; no stale "71 test"/"45 files". |
| 3 | `:19/:22` cites + non-blank self-check | **CLOSED, exceeded** | `V:src/index.ts:19` = dendrite wildcard, `:22` = modules wildcard, `:20`/`:23` blank — confirmed at the tag. My checker: 284 locators / 322 spans / 145 files across the sealed docs, **0** out-of-bounds, **0** all-blank; FACTS.md alone reproduces 275/311/145 exactly. Content checked far past the requested 20: **141/141** inventory rows and **54/54** name-map rows verified against real source. |
| 4 | SUMS coverage, manifest separate | **CLOSED** | `sha256sum -c` 7/7 OK. Covers exactly the 7 other files; no self-entry, no phantom entry, nothing in the directory uncovered but the manifest. Digest `4c0dac68…` reported separately — no hash cycle. v1's unpinned `REPORT.md` gap is fixed. |
| 5 | SC(a) = required contracts, SC5 gating | **CLOSED** | "**REQUIRED SOURCE-SEMANTIC CONTRACTS**, not already-satisfied facts and not approval of any particular mechanism". SC5: "attestation **CONTRACT must be defined before H assignment**; actual qualified I6/native evidence remains separately gated", "not inferred from a brand". "**Packaging proves none of SC(a).**" |
| 6 | §3 future-mood note | **CLOSED** | §3 opens: "Imperative wording in this section describes future requirements only; no action is authorized now." |
| 7 | P LOCAL; staging tracked status | **CLOSED** | "**LOCAL release-set artifact only**: not a committed repository binary, not published and not distributed"; staging "**untracked**, private local staging subtree, outside the tracked source tree". All four parts of REVIEW1 addendum (a)'s fix direction addressed, including the tracked-status question. See LOW-1 for the criterion precision. |
| 8 | RANGE / LOCKED / tag-source distinct | **CLOSED** | Three layers labelled and separated in FACTS and A2-PLAN §5. Tag object `45aa602c…` verified as a true annotated `tag` object; peeled commit `630e2a3a…`. 54-name map labelled "a source-API baseline only, not verification of the locked artifact's declarations"; tag→tarball provenance explicitly **not** established. |
| 9 | Optional: ci.yml cites, "exits 1", gaps | **CLOSED** | `ci.yml:46–50` (npm ci; `:46–48` really does name `npm install` as the silent re-resolve vector), `:89–90` (two `npm ci --ignore-scripts`), `:149–152` (frozen Bun `--ignore-scripts`) — all content-verified. "exits 1" verified through control flow: `file:` → kind `"other"` (`:55`) → both-other → `unknown` (`:157`) → skips `return 0` (`:164`) → `return 1` (`:181`). Strictly more precise than v1's "cannot certify". All three npm/Bun gaps retained **UNRESOLVED**. |
| 10 | No regression | **CLOSED** | Only the three docs changed: 14 hunks, 91 added / 33 removed content lines, every anchor matching CHANGES.md. Every readiness/authority-vocabulary hit in the added lines is a negation or constraint — no new authority or readiness wording. 13 v1 constraints spot-checked for survival across reflowed text: all preserved or strengthened. FACTS' self-reference `O` hash correctly updated to `11d6d7f8…` (= actual OBSERVATIONS.txt); `R` = `057a7faf…` matches. |

### Helper scripts — text-only? **Yes** (no verdict impact)
Read `a2-v11-doc-check.py` (SHA256 `15f0707a…`, matching report.md) and `a2-v11-seal.py` at
source; **neither was executed by me**. Both are read-only text/hash inspectors: `Path.read_text`
/`read_bytes`, `hashlib`, `json.loads`, `difflib`, and `git rev-parse` / `status --porcelain` /
`show` only. `doc-check` asserts `GIT_OPTIONAL_LOCKS=0` at line 6 and `seal` at line 15. No
file writes, no network, no install/build, no node/npm/bun/tsc, and no host code is executed —
host source is read as Git blobs. The seal script only *verifies* the manifest; it does not
write it. Scope note at LOW-2.

## Verification Gates

No gate was required of this review, and I ran none — a reviewer gate receipt would not be
reusable evidence for the worker's claims, and the brief assigns a read-only check. What I ran
instead, all unwrapped read-only probes, with the artifacts kept in `$ORGASMIC_EVIDENCE_DIR`:

- `git rev-parse HEAD` → `f378eadf…`; `sha256sum -c SHA256SUMS` in the v1.1 drop → **7/7 OK**;
  manifest, v1 manifest and REVIEW1 digests — **all five dispatch bindings match exactly**
  (first line of `progress.log`).
- Independent diff regeneration (`diff -u` over the three v1/v1.1 copies): **body byte-identical**
  to the delivered `V1-TO-V1.1.diff`; only the `---`/`+++` header labels were rewritten to
  `v1/` and `v1.1/`. 247 lines, 3 files, 14 hunks.
- `evidence/cite-check.py` (mine) — bounds + non-blank over all cites in the sealed docs.
- `evidence/row-content-check.py` (mine) — all 141 inventory rows against source at `H`.
- `evidence/namemap-check.py` (mine) — all 54 name-map rows against source at `v0.30.0`.
- Targeted `git show` reads at fixed pins: `install.ts`, `package.json`, `ci.yml`,
  `lockfiles-agree.mjs`, `src/recipe.ts:868`, `V:src/index.ts`, both root locks.
- Re-verified every hash report.md asserts: report equality `7e80fe26…`, script `15f0707a…`,
  doc-check log `62c24693…`; both gate logs exit 0.

Worker-evidence reuse: I read the worker's gate logs and helper sources as **claims** and
re-derived each load-bearing number myself. No prior gate PASS is reused or inferred.

## REVIEW1 "What must hold" — conditions 1, 2, 5

- **1 — MET.** Both MEDIUMs applied (independently re-derived, not taken on trust). All four
  LOWs (blank wildcard cites, SUMS coverage, SC(a), imperative §3) applied — **none declined**,
  so the "or explicitly declined with reasons" branch is unused. Addendum (a) and (c) also applied.
- **2 — MET.** Re-sealed `SHA256SUMS` covers the corrected set with no gap and no self-entry,
  and is **re-verified by a reviewer** (7/7, this report). The specific v1 defect — unpinned
  `REPORT.md` — is fixed.
- **5 — MET.** The §4 ordering table (A1 → A2 change under separate go → A3 → H, SC(a) first)
  is carried forward unchanged; `SC1–SC6 (a)-semantics settled first` remains in the H entry
  row. REVIEW1:140 is superseded **explicitly** at `A2-PLAN.md:15–17` ("supersedes REVIEW1:140's
  older 'A2 → (A1,A3)' shorthand") and again at `:339`.

Conditions 3 and 4 are outside my brief and remain external: 3 depends on Soma's confirmation,
which the dispatch relays and which v1.1 cites as relayed (not fetched); 4 is the operator's
own act and v1.1 correctly does not claim it.

## Unmet criteria / test gaps

None against this round's assignment — it was docs-only and no correction was deferred. The
gaps below are **inherited and correctly disclosed** by v1.1, not new:

- Every payload slot stays **UNRESOLVED**: S/composition recipe, toolchain/build closure,
  archive bytes/path P, D/I, declaration closure Q, release-set digest, spec/lock
  serialization, local staging support, relocation and type-identity receipts.
- Three unexecuted probe gaps retained: selected npm relative-file integrity **emission AND
  enforcement**, Bun v2 local-file serialization/checksum, frozen-Bun `file:` acceptance.
- No package-manager, install, behavioral, type-resolution or compatibility execution evidence
  exists anywhere in this round — by design, and v1.1 says so.
- The fail-closed UNSUPPORTED disposition is a **source-derived plan commitment**; the
  pre-manager refusal/entry-prevention evidence it demands does not exist yet and belongs to
  the later authorized scope.

## Residual risk

- A non-blank, in-bounds citation check is **mechanical presence, not semantic validation** —
  v1.1 states this limit itself. My row-level content checks narrow but do not eliminate it:
  I verified each cited span contains its claimed statement/symbol, not that every surrounding
  prose inference is sound.
- Tag-source API is **not authenticated provenance** for the locked 0.30.0 tarball; the
  54-name baseline could diverge from that artifact's emitted declarations. Correctly disclosed.
- The whole drop is source-layer only. No installed tree, package artifact or emitted
  declaration was ever inspected, here or in v1.
- LOW-1's criterion gap could let a later implementer satisfy the letter of "untracked" while
  still packing P.
- Soma's confirmation and the operator's acceptance are both **relayed, not verified** here.

## Fix directions

Ordered by value; all are optional polish, and **none should hold up acceptance**:

1. **LOW-1** — restate P's exclusion against the `files` allowlist directories rather than
   git-tracking, since that is what governs pack inclusion and what the later scope must prove.
2. **LOW-3** — name the final seal gate log next to the first, so the delivered manifest digest
   is reachable from the log the report names.
3. **LOW-2** — scope the 275/311/145 figure to FACTS.md, or widen the script's loop to all three
   sealed docs (it would pass: I ran the wider check).
4. **Trivial** — expand `H:ci.yml` to `H:.github/workflows/ci.yml` in `CHANGES.md:23`.

A v1.2 is **not** warranted for these alone; they are better folded into whatever next round
touches these files.
