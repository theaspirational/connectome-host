# TASK-R5BF7 review — F-host H brief (docs, local only)

Range `8e92bfbe30d141140531b170e9ef0421c6a16cad..0f18209b673058bab2b7c5ac695409d6ab632020`,
1 commit, one added file `docs/design/f-host-h-brief.md` (408 lines, pure addition).
Reviewed report `/srv/review-drop/TASK-R5BF7-H-BRIEF.md`, sha256
`463140b7ffd5b1e4c7f3232ba1bd5d7cc2ed55061e8a00963a659cf65ba9713d` — matches the
dispatch value and is byte-identical to the implementer's `evidence/report.md`.

## Verdict

**approve.** All seven dispatch checks verified against source. No HIGH or MEDIUM
findings. Three LOW items, all about verification coverage around the brief rather
than defects in it; none blocks.

This approves **documentation fidelity only**. The brief asserts no implementation,
no PASS receipt and no owner decision, and every real H gate stays PENDING on the
missing artifacts it names. Worktree left clean at `0f18209b`; no tracked file
touched, probes written to the evidence dir and `/tmp` (scratch removed).

## Findings

### LOW (test) `evidence/verify-brief.py:18` — bespoke gate's case-id check asserts nothing
`covered=[x for x in ids if f'`{x}`' in s]` computes an intersection and only
`print`s it (line 19). A brief citing a **fabricated** fixture id would not appear
in the printed list and the gate would still exit 0, while the log line "Named exact
policy-shapes case IDs: ..." reads like a verified claim. The other assertions in
that file are genuine (lines 6-7 compare against real `host.md` bytes; 11-13 assert
byte-identity to the base plus hash presence), so this is the one soft spot.
I closed it by hand: extracted every backticked kebab-case token in the brief and
diffed against the union of `policy-shapes.json`/`scope-cases.json` ids — **18 cited
ids all exist, 0 fabricated**; the 23 non-matching tokens are commit SHAs, file
digests, `authorize`, `host`, `fixtures`. No defect in the brief.
*Fix direction:* assert `set(cited) <= set(ids)` by matching the id grammar, so the
gate fails on a fabricated id instead of silently omitting it.

### LOW (docs/test-coverage) `docs/design/f-host-h-brief.md:395-404` — brief is permanently outside `--docs-contract`
The brief's diagnosis is **correct and I reproduced it end-to-end**, but the
consequence is that the repo's standing neutrality scan covers 9 inputs and never
this 408-line document, with no in-repo gate closing the gap. The brief discloses
this ("record this coverage limitation and inspect the new diff separately") and
rightly refuses to obfuscate citations or add an exemption, so this is residual
risk, not misconduct. Compensating evidence is the one-off `verify-brief.py`
privacy check, which its own log calls "NOT full private naming-policy qualification".
*Fix direction:* separate task — narrow `checker:233`
(`/\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/iu`) so it cannot fire on ordinary
lowercase repo filenames (require an uppercase prefix, or a digit in the suffix),
then add the brief to the docs-mode list. Do not special-case the two filenames.

### LOW (docs) `docs/design/f-host-h-brief.md:133` — coarse range attribution in the ci.yml row
The cell attributes "Fixed baseline Node **22.12.0**, Bun **1.4.3**" to the combined
list `ci.yml:23–29,49–50,82–97,139–155`. Every individual claim in the cell is
sourced inside those ranges, but `:139–155` is the lockfile-agreement job, which
pins **Node 24** and Bun 1.4.3 — not the 22.12.0 baseline, and not one of the
"floating" cells the same sentence describes. A reader reconstructing which job
runs which runtime from this ledger could be mildly misled. Content is otherwise
exact (baseline suite row `23-29`, strict `npm ci --ignore-scripts --no-audit
--no-fund` at `49-50`/`86-88`, host `npx tsc --noEmit` at `93`,
`bun install --frozen-lockfile` at `152`, `lockfiles-agree.mjs` at `155`).
*Fix direction:* split the range per claim, or say "pinned Node 22.12.0/24 per job".

## What I verified (all seven dispatch checks)

**(1) H definition verbatim — CONFIRMED programmatically.** Brief lines 20-29 vs
`host.md@a316200c:680–689` are **byte-identical**, sha256
`0a5aa027ed24a87a21a0a6eb4d1dec84b19fcf2aeee1bba686c9ee21b3f8a995` for all three of
brief / `a316200c` / base `8e92bfbe` — the table is also unchanged between the two
pins. The dispatch's `:666-694` is the §6 section; the table itself sits at 680-689
in both pins. Hours and dependency clauses (`after .2a/.3`, `after .2a/.2b/.3 and
store decision`, `can start immediately`) carried over unaltered. `52–80 h` / `62–96 h`
match `host:691–693` verbatim including the `10+10+4×8` derivation; the brief
re-estimates nothing, adds no bucket, proposes no rewrite, and retains `.1` only as
labelled source context.

**(2) Boundaries — correct.** §2 rows check out row-by-row against `host:702–708`:
T2 "beyond H's static policy/binder" is `host:703` exactly (so T2 cannot precede H,
as the assignment states); T3 `705`, T4 `706` ("not a second full harness build"),
T5 `707` ("without repeating H's ordinary D persistence work"), AF-scope `708`.
The supersession claim is exact — `T1:72` says the exact-list recipe shape is
"**superseded**", and `T1:88–90` gives listed-but-not-joined denial. The "H does not
do" list is sourced to `host:262–321,392–420,625–664,734–739`; live migration, policy
weakening and public history are all excluded, and G's opt-in/migration/rollback
docs are explicitly "not execution authorization".

**(3) Input pins — every AVAILABLE pin is real; MISSING named precisely.**
All **12 claimed SHA-256 digests recomputed and matched exactly**
(host, T1@`bff8c46e`, fixtures, 2a, stubs.ts, policy-shapes, scope-cases,
old-reader-payloads, package.json, package-lock.json, bun.lock, ci.yml), and every
claimed line count matched (host 1-772, T1 1-466, fixtures 1-235, 2a 1-286,
stubs 1-30). The four host-repo commit pins all resolve, and `2c396622 → a316200c →
bff8c46e` are confirmed real ancestors of HEAD. The four AF-fork pins (A `869bb3ad`,
B `27a32127`/`29edcf8b`, D `a5b02a07`) are absent from this repo — consistent with
the brief labelling them "AVAILABLE **contract pins only**" under its own stated
definition, with substitution for a composed release explicitly forbidden.
The I4 B optional/required discrepancy is genuine (`host:31–33` optional vs
`T1:19–20` required). I6's "0.31-line" and "existing `^0.30.0` is not that release"
are `host:52–53` verbatim. Toolchain cited correctly: `package.json:44` TypeScript
`^7.0.2`, `package-lock.json:2052` top-level **7.0.2** plus `:1095` linux-x64 7.0.2
native optional with both integrity values, `bun.lock:17,158,318` the same —
correctly distinguished from nested tool-dependency TypeScript. Acquisition plan is
isolated and code-only: own lockfile, CI's `npm ci --ignore-scripts`, separate Bun
route checked for agreement, and explicit bans on copying a neighbouring
`node_modules`, a global compiler, a symlinked install, a fresh caret resolution or
a 5.9.3 downgrade — `2a:231` independently forbids both the 5.9.3 observation and a
"borrowed 7.0.2 probe", which the brief echoes. No borrowing.

**(4) Contract decisions sourced; owner questions are exactly the existing seven.**
`host:745` states "The single authoritative numbered list is **decisions 1–7 in §4**",
and all seven brief items map 1:1 onto `host:448–481` with **per-item line citations
that are exact** (454-457, 458-461, 462-466, 467-470, 471-474, 476-478, 479-481).
Nothing added, renumbered or relabelled as an owner question. Engineering inputs are
kept in a separate subsection citing `host:751–772` ("Engineering calls") and T1 —
and the brief correctly classifies Mapping X parity / native thread identity as
requirements rather than owner decisions, which `host:483–485` and `host:747–749`
both confirm.

**(5) VerifiedScope = operations only.** §5 proposes an opaque type plus trusted
constructors/accessors per `host:703` and `T1:298–339`. Every fact it names (account
association, conversation kind, readable/joined facts or DM **user** identity, exact
parent `RegistrationRef`, opaque immutable mapping generation) is exactly
`T1:324–326`'s list — **no invented field or wire member**, and the brief says so
outright. Lifetime/ordering semantics track `T1:251–261,328–334`. Authority is
correctly denied to a model object, arbitrary descriptor or register ACK, matching
`T1:311–321` ("Only a configured verifier consuming authenticated adapter evidence
may produce `verifiedScope`"). The cited `stubs.ts:6–13` block does hold the
nominal/opaque `VerifiedScope` declaration (symbol line 8, type line 11).

**(6) The .3 brief.** Goal is `host:685`'s cell **verbatim**. **All eight cited
source locations exist, are in bounds, and contain the described code** — spot-read
each: `recipe.ts:506` `RecipeMcpServer`, `:1566` `Recipe`, `:1950` `RECIPE_KEYS`
(the unknown-key seam), `:2717` the `mcpServers` validator; `mcpl-config.ts:33`
`ServerFileEntry`, `:140` `RECIPE_OVERRIDABLE_SERVER_FIELDS`, `:296`
`OPERATOR_ONLY_OVERLAY_FIELDS`, `:317` `readOverlayEntry` (incl. the
`disabled === true` tombstone the brief relies on), `:537` `resolveOverlayEntry`,
`:656` `openedBy` wrapping AF `resolveServerBinding` — which is precisely the
"enforce legacy-family on final protected binding, not by URL guesses" point;
`index.ts:470–496` merge/overlay and `:498–515` ending at `AgentFramework.create`,
matching "compile callbacks after checks and before create";
`framework-agent-config.ts:60` `systemPromptFor` and `:99` `buildFrameworkAgentConfig`.
Stub-testable vs needs-real-APIs is split correctly and conservatively: the brief
states a stub "can prove policy output, opaque type use and callback omission in
compiled config, **not** issued-object identity at runtime, attestation-before-bind
ordering, real no-await behavior, post-await invalidation or actual storage/wake
counts", and consistently files A04/T01-T03/races to T2-T5. **All case ids exist**
(18/18, 0 fabricated — see LOW 1); shared rows H02/H04/H05/H06/H08, A01-A03, U01/U02
all present in fixtures.md, and the `H02` row at `fixtures:103` independently
corroborates acceptance items 3-4 including `recipient_policy_requires_legacy_family`,
"zero attempts", and the non-data initialize-result/accessor control. Error strings
match `host:155–162` exactly. Mutants and feature-off controls are consistently
worded as **planned** ("planned .3 coverage, not PASS receipts"), with a "Real gate
remaining" column on every row; `.3` gates are stated **PENDING** without acquisition.
The `.3`-first justification is sound: `.3` is the only slice with no `after` clause
in the table and is unit-testable against versioned doubles, whereas `.2b` — though
it "can start immediately" — cannot be *tested* without the MISSING I8 runtimes;
the brief frames this as selecting a starting point, not a completion claim.

**(7) Scan-inclusion claim — reproduced; neutrality clean.** `checker:233` is indeed
`/\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/iu` → `'internal identifier'`, and the
docs-mode supplemental list is at `:333–351` as cited. In a scratch copy outside the
worktree I added only the brief's path to that list and ran `--docs-contract`:
`AssertionError [ERR_ASSERTION]: internal identifier at neutral (...:233:3)`, exit 1.
**Claim confirmed** — the brief cannot be added without a logic change. The trigger
is a false positive on two ordinary filenames, `agent-config` (line 275) and
`agent-overlay` (line 277); no other `neutral()` rule fires. Neutrality risk is
otherwise clean: zero hits for operator/owner names (Tarn, Soma, Vasyl), tooling
names, task/run ids, `review-drop`, `ledgers`, `worktrees`, repo/connector names, and
zero machine-local paths, UUIDs, emails, URLs or credential-shaped text. §7's
LOCAL ONLY hold is correctly justified — the private ancestry chain it cites is real
ancestry of this commit (verified by `merge-base --is-ancestor`), so scrubbing files
genuinely would not clean it.

## Verification Gates

Ran myself through the recorder, both bounded at 60s as the brief specifies:

- `self-test` — **exit 0** — `orgasmic dispatch gate self-test -- bash test/stubs/f-host-2a/bounded-gate.sh 60s node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` — 57 cases, 45 rejection controls, 4 oracle mutants killed, singleton negative control, naming-policy parser. Log `gate-self-test-01M67ZD2YWN9Z834VFK86R4XD7.log`.
- `docs-contract` — **exit 0** — same wrapper with `--docs-contract` — 57 cases; `PARTIAL naming check` (no `SCOPE_FORBIDDEN_NAMES`, confirmed unset in env); 9 public files, 11 rejected controls, 0 injected names. Log `gate-docs-contract-01M67ZD5RTBY083MQNJARPV7V4.log`.

Both reproduce the implementer's logs exactly. Wrapper semantics in the brief are
accurate against `bounded-gate.sh`: `timeout --signal=TERM --kill-after=60s`,
65536-byte cap, cap → exit 125, `124|137|141` → INCONCLUSIVE (lines 15-24), matching
`2a:234–245`.

Reused receipts (read, not re-run): `brief-source-check`, `diff-check` — both exit 0
at `0f18209b` on a clean tree. I re-derived `brief-source-check`'s central assertion
independently rather than trusting it.

Scoped probes (artifacts in evidence dir): `reproduce-inclusion.mjs` (rule-match
enumeration over the brief); scratch-tree list-only checker patch + run (removed);
hash/line-count recomputation of all 12 pins; commit-pin resolution and ancestry;
fabricated-case-id sweep; per-range reads of every `host`/`T1`/`2a`/`fixtures`/`src`
citation named above.

Not run, by design: no full suite, typecheck, native, store or live run. `tsc` is
unavailable in this tree by the brief's own account (declared TypeScript 7.0.2 is
unacquired), so no toolchain gate was possible — correctly declared PENDING rather
than claimed.

## Unmet criteria / test gaps

Task acceptance was "not set"; I judged against the assignment and the dispatch's
seven checks, all of which are met. Gaps are coverage, not unmet criteria:

1. The brief has no standing automated neutrality gate (LOW 2). Its one-off check is
   not full naming qualification, and `--docs-contract` is `PARTIAL` anyway while
   `SCOPE_FORBIDDEN_NAMES` is unset — so "neutral" here means "no detected leak by
   the path/id/credential rules plus my manual sweep", not qualified neutrality.
2. `verify-brief.py`'s case-id coverage line is informational only (LOW 1); I closed
   it manually for this commit, but nothing prevents regression in a future brief.
3. No gate asserts the brief's ~100 `file:line` citations stay valid as `src/` moves.
   I verified them at this commit by hand; they will silently rot. Inherent to prose
   citations, and the brief does pin itself to a base commit, which limits the damage.

## Residual risk

Low for what this commit is — a docs-only addition that changes no code, no fixture,
no stub and no gate, is byte-faithful to its sources, and asserts no result. Risk
sits downstream: this brief becomes the input ledger for H, so (a) the citation set
is only valid at the pinned base and will drift as `src/` changes; (b) its AVAILABLE
labels cover contracts and bytes, never acquired runtimes — anyone reading I4's
AVAILABLE pins as consumable would be wrong, though the brief says so explicitly and
repeatedly; (c) the I5-I10 artifacts are genuinely absent, so `.3` cannot start its
real gates, and nothing here changes that; (d) the brief is excluded from the
neutrality scan, so a later edit to it gets no automated privacy check. The
LOCAL ONLY hold is correctly scoped and the private ancestry is real — publication
would require a fresh clean tree on a public base plus a full-range privacy check,
exactly as §7 states.

## Fix directions

1. *(optional, separate task)* Narrow `check-scope-contract.mjs:233` so the
   internal-identifier rule cannot match lowercase repo filenames, then add
   `docs/design/f-host-h-brief.md` to the docs-mode list at `:333–351`. Keep it a
   rule change, not an exemption or a path allowlist.
2. *(optional)* Make the case-id coverage check in the brief-source gate assert
   subset-of-fixture-ids rather than print an intersection.
3. *(optional, cosmetic)* Split the `ci.yml` range citation at brief line 133 per
   claim so the Node 24 lockfile-agreement job is not folded under "fixed baseline
   Node 22.12.0".

None is a merge condition. The brief is accurate, conservatively scoped, honest about
what it has not proven, and makes no owner decision.
