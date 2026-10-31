# Review — TASK-9MKQH, F-host T2 docs/stub PREP

Range `8e92bfbe30d141140531b170e9ef0421c6a16cad..7e93298728393972fa5a967a7e21c5dda7b5aafd`
(2 commits, 5 files, +312/-0). Reviewed report
`/srv/review-drop/TASK-9MKQH-T2-PREP.md`, sha256 verified
`8e03324e5178ad40fd03b3460d1b43f83fbc8634d87617795b70d4a8ee56d7e8` (matches the brief).

## Verdict

**APPROVE** as the narrowed docs/stub input deliverable. No HIGH or MEDIUM findings.
All seven brief check items hold on independent inspection. Three LOW items below are
precision/coverage notes, none blocking.

Scope conformance: the round stays inside the operator's narrowing (ask-007c1d48). No
production host code, no H rebuild, no invented `VerifiedScope` fields, no
dependency/lockfile/CI change, no push. `src/`, `.github/`, `package.json`,
`package-lock.json`, `bun.lock` are byte-unchanged across the range (`git diff --stat`,
empty). No owner (Vasyl) decision is asserted anywhere in the brief.

## Checks against the brief's seven items

**(1) T2 definition verbatim, not widened — CONFIRMED.**
`git show a316200c:docs/design/generic-recipient-host.md` line 703 reads
"Add dynamic scope compile/bind coverage and feature-off controls beyond H's static
policy/binder; do not charge that baseline compile again". `f-host-t2-brief.md:9-11`
quotes that string character-for-character and cites `host.md:703` plus the design SHA.
`a316200c` is an ancestor of HEAD; the worktree's `host.md:703` is the same line. The
brief adds the "12–20 h row is incremental work, not authorization to implement H again"
guard (`:12-15`) — a narrowing, not a widening. H's PR rows are correctly `:680-689`
(682 F-host.1 … 689 G host qualification), and `.2a` is correctly `:683`.

**(2) Four blockers concrete; "H missing" holds — CONFIRMED.**
- `src/` is byte-identical to upstream `2c39662289be4cd5a150222e8456ff5577ce7622`
  (`git diff --stat 2c396622 HEAD -- src/` empty).
- `git grep -nE 'recipientPolicy|inboundAdmission|replyAuthority|joinedReadable|VerifiedScope' -- src/`
  → exit 1, zero matches. Repo-wide the terms appear only in docs/fixtures/stubs, never
  under `src/`. No branch carries an H implementation.
- Each gap names what T2 needs with T1 lines: H pin (`brief:25-45`, insertion points
  `src/recipe.ts:506-620`, `src/mcpl-config.ts:33-63,71-112`, `src/index.ts:470-496,508-533`);
  VerifiedScope (`:47-65`, T1 `:303-308,324-326,336-339` — all three citations verified
  against `bff8c46e`); reviewed S4Q8F view head (`:67-78`, T1 `:298-334`); A+B+D release
  (`:80-92`, `host:43-76`). A/B pins `869bb3ad`/`29edcf8b` are not objects in this repo —
  correctly so: they are AF-repo commits, matching T1 `:17-20` and `host.md:43-46`. The
  "B requires synchronous `authorize` at this pin" claim is verbatim T1 `:18-20`.
- Integration gate is left explicitly **OPEN** (`:90-92`).

**(3) `registration-view v0` aliases only, nothing invented — CONFIRMED.**
`test/stubs/f-host-t2/v0/stubs.ts:9` is a single alias
`export type RegistrationView = Parameters<typeof bind>[1]` over the **unchanged** 2a
declaration. `test/stubs/f-host-2a/v0/stubs.ts` is byte-unchanged in the range. No added
members, no projector representation, no AF export, no packaging. `VerifiedScope` stays
the 2a `unique symbol` brand (`f-host-2a/v0/stubs.ts:11`), and `typecheck.ts:29-30`
asserts a fabricated `{readable:true}` must fail. The T1 §4 shape claim is accurate:
T1 `:303-308` is exactly `bind(key, registrations: {lookup(channelId) -> {ref, descriptor,
scope?} | undefined; current(ref) -> boolean})`. "STUB, not integration evidence" heads
both `stubs.ts:1` and `typecheck.ts:1`. No production file imports either stub
(`git grep -nE 'f-host-(2a|t2)' -- src/ scripts/ web/ recipes/ package.json` → exit 1).

**(4) All 57 ids mapped; T3/T4/T5 boundaries correct — CONFIRMED programmatically.**
Parsed the brief's table and `test/fixtures/generic-recipient/scope-cases.json`:
57 unique ids each side, **0 missing, 0 extra, 0 duplicates, identical order**.
Allocation counts: `T2 +T4` 38, `T2 +T5` 5, `T2 +T3/T4` 6, `T3/T4 only` 8 → **49 host
portions + 8 native-only = 57**, matching `brief:151`. Boundary anchors verified against
`host.md`: `:704` T3 F-Slack native adapter, `:705` T4 native qualification, `:706` T5
dynamic storage parity — so `+T5` = storage/wake parity, `+T3/T4` = native capture, and
`T3/T4 only` = native effect/permission are each pinned to the right row. The 7
`input.mode === "explicit"` cases in the JSON are exactly 7 of the 8 native-only entries.
`scope-cases.json` and `policy-shapes.json` are unchanged.

**(5) Feature-off controls planned, mutants NOT RUN, suite PENDING — CONFIRMED.**
7 control rows (`brief:131-137`), introduced by "These are **planned tests**, not
implemented controls or executed mutants" (`:127`) and "No byte-equivalence result can be
claimed before H exists" (`:141`). T2 production mutants explicitly **NOT RUN** with no
survivor/kill claim (`:252-256`), and the checker's 4 documentation-oracle kills are
explicitly separated from them. The PENDING claims are true, not merely asserted: `bun`,
`tsc` and `node_modules/` are all absent (`ls node_modules/.bin/tsc` → no such file;
`which tsc bun` → empty; node v22.23.3), `typescript` is declared `^7.0.2`, and the cited
suite anchors are exact (`package.json:23` = `"test": "bun test"`;
`scripts/test-on-tmpfs.sh:26` = `npm test -- --parallel=4`). No acquisition occurred.
Root `tsconfig.json` is `include: ["src"]`, so the new `.ts` files cannot enter the
project typecheck; neither `stubs.ts` nor `typecheck.ts` matches a Bun test glob, so
nothing new can falsely report "passing".

**(6) Checker change = inputs only; gates re-run by me — CONFIRMED.**
The whole diff to `check-scope-contract.mjs` is +4 lines inside the existing
`docsContract()` text-scan path list (`:341` onward) — the brief, `stubs.ts`,
`typecheck.ts`, `tsconfig.json`. No oracle, fixture, neutrality-rule, counting or CI
logic change. I re-ran both gates myself under
`timeout --signal=TERM --kill-after=60s 60s`:
- `--self-test` → **exit 0**; 57 cases, 45 rejection controls, 4 oracle mutants killed,
  singleton negative control and naming-policy parser pass.
- `--docs-contract` → **exit 0**; "13 public files; 11 rejected controls; 0 injected
  names". Reproducing the same gate at parent `8e92bfbe` gives "9 public files" — a clean
  +4, exactly the four added scan inputs, with the rejected-control count unchanged at 11.
- `git diff --check 8e92bfbe HEAD` → **exit 0**.

**(7) No internal names; LOCAL ONLY section present — CONFIRMED.**
`git grep -niE 'orgasmic|tarn|soma|vasyl|TASK-[0-9A-Z]{5}|ask-007c|dovganchin|theaspirational'`
over the brief, the three stub files and the checker → exit 1, zero matches; repo-wide
clean for the operator/infra terms. The `## Publication` section (`:258-264`) states
LOCAL ONLY, that scrubbing new files does not clean ancestry, that a later public
submission needs a fresh clean tree on a public base plus a whole-publication-range
privacy review, and that a general topic-push go does not lift the hold. HEAD is on no
remote (`git branch -r --contains HEAD` empty) — nothing was pushed.

## Findings

**LOW (test/allocation) `docs/design/f-host-t2-brief.md:192`:**
`t03-native-send-access-revoked` is the one `T3/T4 only` entry that is not an
`input.mode === "explicit"` case. The brief's native-only rationale (`:163-164`) is
"explicit native tool permissions/exact root or native effect access", but this row is an
automatic-ingress case whose fixture expectations include host-observable
`store:true, wake:true, newBinding:true, privateNotice:true`; its three sibling t03
revocation rows (`:190,191,193`) are all `T2 +T4`. The allocation is defensible under the
brief's own "uncovered witness" rule (`:155`) because the host half — revocation must not
erase storage/wake — is witnessed by `t02-reply-only-denied` (`:185`) and
`t02-author-access-storage-only` (`:184`). But the exception is not stated, so a later
implementer reading `:163-164` cannot tell why this non-explicit row carries no T2 test.
Fix direction: add one clause naming the host half of this row as covered by the t02
rows, or re-allocate it to `T2 +T4` with the storage/wake-eligibility mutant.

**LOW (test) `test/stubs/f-host-t2/v0/typecheck.ts:24,27,29`:**
No machine has ever compiled this file, so its three `@ts-expect-error` directives are
unverified in both directions — if any of those lines were in fact well-typed, `tsc`
would report an unused-directive error that nothing currently surfaces. Reading the types
by hand, all three look correct (`Promise<boolean>` vs `boolean`; `ConnectionKey` vs
`RegistrationRef` as distinct `unique symbol` brands; `{readable:true}` vs the
`VerifiedScope` brand), and `Parameters<typeof bind>[1]` is sound because the second
parameter does not mention the `Admit` type parameter. The brief does disclose this
(`:104-105`, typechecking PENDING), so it is an unverified artifact, not a false claim.
Relatedly, `f-host-2a/v0/` ships a `bounded-gate.sh` wrapper while `f-host-t2/v0/` ships
only a `tsconfig.json`; the brief deliberately disowns the 2a wrapper's 65536-byte cap
(`:250`) but leaves the next round no bounded wrapper of its own. Fix direction: when the
TypeScript 7 toolchain is authorized, run `tsc -p test/stubs/f-host-t2/v0/tsconfig.json`
first and record the resolved input-file list per `f-host-2a-brief.md:211-232`.

**LOW (docs) `docs/design/f-host-t2-brief.md:13`:**
"T1–T5 and conditional AF-scope are `:700–715`" is over-wide. At `a316200c` the table
header is `:700`, the T1–T5 rows are `:702-706`, the `T total` row `:707` and the
conditional AF-scope row `:708`; `:710-715` is allocation/critical-path prose, not those
rows. Every other line citation I spot-checked was exact, so this is an isolated
imprecision. Fix direction: narrow to `:700–708`.

## Verification gates

Reused receipts (read, not re-run), all exit 0 at `7e932987` on a clean tree:
`final-allocation-audit`, `final-diff-check`, `final-docs-contract`,
`final-input-preflight`, `final-self-test` — logs under
`/home/orgasmic/.orgasmic/runs/run-01M67YP2548BACQSKEP8MKY1QE/evidence/`. The
`final-input-preflight` log independently records "src is byte-identical to upstream
2c396622 tree", "0 matching lines; expected H baseline absent", "bun unavailable / tsc
unavailable / local node_modules absent"; `final-allocation-audit` independently records
"57 unique fixture ids in order; 49 planned host portions; 8 native-only exclusions; 0
missing/extra ids". Both match my own independent reproductions.

Run by me in this worktree (scoped probes, bounded):
- `timeout --signal=TERM --kill-after=60s 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` → exit 0.
- `timeout --signal=TERM --kill-after=60s 60s node .../check-scope-contract.mjs --docs-contract` → exit 0, 13 public files.
- Same `--docs-contract` against a `git archive` of `8e92bfbe` in a temp dir → exit 0, 9 public files (delta = +4).
- `timeout 60s git diff --check 8e92bfbe HEAD` → exit 0.
- Node script diffing the brief's 57 table rows against `scope-cases.json` → 0 missing, 0 extra, 0 duplicates, identical order, 49/8 split.
- `git diff --stat` for `src/ .github/ package.json package-lock.json bun.lock test/stubs/f-host-2a/ test/fixtures/ docs/design/generic-recipient-*.md` → all empty.
- `git grep` probes for H symbols, stub imports from production, and internal names → all exit 1 (no matches).

Not run, with reason: host suite (`bun test`) and stub typecheck — Bun, `tsc` and the
local dependency closure are absent and acquisition is unauthorized in this round. This
matches the brief's PENDING statement rather than contradicting it.

Worktree left exactly as found: `git status --porcelain` empty, no commits, no tracked-file
edits, no new files inside the tree. Report and probes live in `$ORGASMIC_EVIDENCE_DIR`.

## Unmet criteria / test gaps

Nothing in the narrowed assignment is unmet. The gaps are inherent to a docs/stub input
and are disclosed by the brief:
1. Zero executable T2 coverage. The 49 host portions, 7 feature-off controls and all T2
   mutants are planned labels; none exists as a test. The only executed verification is
   the pre-existing documentation oracle plus the text scan.
2. The `registration-view v0` alias has never been compiled (LOW 2), so even its
   compile-only claims are unproven.
3. `--docs-contract` reports `PARTIAL naming check: no nonempty SCOPE_FORBIDDEN_NAMES
   list; path/id/credential checks only`. The gate therefore does not prove absence of
   internal names; that conclusion rests on my explicit `git grep`, which is clean. The
   brief correctly requires this stay PARTIAL (`:234-235`) rather than claiming full
   naming qualification.
4. All four named blockers remain open, so T2 implementation cannot start: no H pin, no
   concrete `VerifiedScope`, no reviewed S4Q8F view head, no A+B+D release.

## Residual risk

- **Low, contained.** The change cannot affect production: `src/` is untouched, no
  production path imports the stubs, the root `tsconfig.json` excludes them, and no Bun
  test glob matches them. The only behavioral delta in the repo is four extra files read
  by `--docs-contract`'s text scan.
- **Main risk is interpretive, not mechanical:** a later reader could mistake the planned
  allocation table for coverage. The brief guards this repeatedly (`:3-5,127,147,151-153,252`),
  but the mitigation is prose, not a mechanical check — nothing fails if a future round
  cites a `t2/...` label that has no test behind it.
- **Publication hold is the standing risk.** The commits descend from the private
  F-host/T1 line (`2c396622..a316200c..bff8c46e`); local neutrality checks are not
  publication approval. HEAD is on no remote today, and that must hold.

## Fix directions

1. (LOW 1) Either justify `t03-native-send-access-revoked`'s native-only placement by
   naming `t02-reply-only-denied`/`t02-author-access-storage-only` as the host witnesses,
   or move it to `T2 +T4` with a storage/wake-eligibility mutant.
2. (LOW 2) First action of the next authorized round: `tsc -p
   test/stubs/f-host-t2/v0/tsconfig.json` under the declared TypeScript 7 major,
   recording compiler version, hash, lockfile closure and resolved input files per
   `f-host-2a-brief.md:211-232`; add a bounded wrapper for `f-host-t2/v0` at this round's
   64 MiB cap rather than reusing 2a's 65536-byte one.
3. (LOW 3) Narrow the `:700–715` citation in `brief:13` to `:700–708`.
4. Keep the integration gate open and the LOCAL ONLY hold in force until the four
   blockers are resolved; T2 implementation should not be scheduled before the H pin
   exists, per `host.md:703`.
