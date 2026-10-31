# CONV-Y0DNS — Independent read-only review of TASK-ZE2GZ A2 plan v1

Scope: the dispatch brief. Read-only. No edits, commits, installs, network, build or
package execution. All git reads used `GIT_OPTIONAL_LOCKS=0`.
This review is not acceptance, not a gate PASS, not readiness, and grants no authority.

## Verdict

**ACCEPT WITH CHANGES.**

The plan is substantively sound on every dimension the brief names. Payload identity is
correctly modelled as source + recipe/toolchain + artifact digest + declaration closure
bound by a release-set receipt; the four-option comparison is fair and reaches the right
recommendation; there is no registry assumption, no private absolute path in any proposed
tracked change, and no post-build overlay; the ordering matches the qualified
A1 → A2-change → A3 → H sequence; authority limits are explicit and complete. Both of its
precision corrections to REVIEW1 (45→43, "only 0.30.0") are **correct** — I reproduced both
independently. Of 251 unique source locators I resolved automatically, zero were missing
or out of bounds.

The required changes are one named-failure-mode gap with a concrete verified failure path,
one counting/labelling defect in the inventory prose, and three precision items. None
invalidates the recommendation or the identity model.

## Findings

### MEDIUM — A2-PLAN.md:253–260 (with `H:src/update/install.ts:93–96`, `H:package.json:9–18`): the recommended `file:` spec breaks the existing self-updater's own host-install path, and that failure mode is not named.

The plan's updater paragraph argues only that the updater *cannot stage/verify* a release
set and must not be used *for* the AF payload. It never states that the recommended
manifest shape **regresses the updater's existing function**. Verified mechanism:

- `installInto(tmp, running, version, ops)` copies `running/package.json` → `tmp/package.json`
  and the first recognized lock → `tmp/` (`install.ts:95–96`), then runs
  `npm install --save-exact --no-audit --no-fund @animalabs/connectome-host@<version>`
  (or `bun add --exact`) **with cwd `tmp`** (`install.ts:98–102`).
- The copied manifest carries the host's own `dependencies`. Today that is
  `"@animalabs/agent-framework": "^0.30.0"` (`H:package.json:33`) — a registry range,
  resolvable from an empty `tmp`.
- Under the recommendation it becomes `"file:" + P` with P relative (A2-PLAN:193, :186–188).
  No archive is staged into `tmp`, so the manager cannot resolve it, `status !== 0`, and
  `installRelease` throws `ContractError` (`install.ts:103–110`). Self-update fails.
- Second edge, same root cause: the host `files` allowlist is
  `src, recipes, scripts, docs, dist/web, tsconfig.json, .env.example, ARCHITECTURE.md, CHANGELOG.md`
  (`H:package.json:9–18`) — no vendor directory. A packed/published host would carry a
  `file:P` spec pointing at a path it never shipped, so the *registry-installed* host
  breaks too, not only self-update.

The plan names the **packing** half correctly (:258–260, "its current files allowlist does
not establish inclusion of P"), but not the **install-time** half. Impact: a manager acting
under the later separate go could land the §3 edit scope — which explicitly includes the
host `package.json` AF spec and says "Preserve other pins" — and discover self-update and
packed-host installation broken, with no prerequisite row covering it.
Fix direction: add the updater/packed-host breakage to §3's failure list and to §4 as an
exit requirement of the later dependency scope (either stage P inside the released tree and
add it to `files`, or make the released manifest's AF spec distinct from the development
one); state it as an *induced* consequence, not merely as "don't route the payload here".

### MEDIUM — FACTS.md:59 (and REPORT.md:36): the "43 production / 71 test" split mislabels a tracked script as a test file, and the "(43 production paths)" parenthetical understates its own table by one.

I re-derived the inventory independently and the **table is exactly right**: 141 statement
rows, 114 unique files, matching my grep set with zero difference in either direction.
But the classification in the prose is wrong:

- actual: **43** under `src/`, **1** under `scripts/` (`scripts/warmup-session.ts`), **70** under `test/`
- claimed: "43 production import-bearing files and 71 test import-bearing files"

The arithmetic survives (43+71 = 114) only because `scripts/warmup-session.ts` is silently
counted as a test. It is a tracked script, not a test — and the plan elsewhere relies on the
production/test distinction (REPORT.md:36 "Reading test files was not test execution").
The parenthetical "inventory below (43 production paths)" also conflicts with the table's 44
non-test rows. In a document whose entire value is citation precision, and which the brief
asked me to check on exactly this claim, the label must match the table.
Fix direction: state "43 `src/` + 1 `scripts/` + 70 `test/` = 114 import-bearing files,
141 statements/type queries", and correct REPORT.md:36 to match.

### LOW — FACTS.md "0.30-era exported symbols" preamble: two wildcard-route citations point at blank lines, and the advertised self-check could not have caught it.

`V:src/index.ts:20` and `V:src/index.ts:23` are both **blank lines**. The actual routes are
one line earlier: `:19` `export * from './dendrite/index.js';` and `:22`
`export * from './modules/index.js';`. The underlying claim holds, but these two cites are
load-bearing — they are what makes `AgentRecord`, `taskForkSpec`, `workerSpec` (dendrite)
and `WorkspaceModule`, `WorkspaceReadError`, `HistoryModule`, `MountConfig` (modules)
reachable from the root barrel.

This also exposes the limit of the verification REPORT.md:36 advertises: "252 … locators
resolve within actual line bounds" proves only *bounds*, not that the cited line carries the
claimed content. My automated sweep of 251 unique specs found 0 missing files and 0
out-of-bounds, and these 2 all-blank targets — i.e. the defect class the self-check is blind
to is precisely the one present. The error is **isolated, not systematic**, which bounds the
severity. Fix direction: correct to `:19` and `:22`, and change the self-check to assert the
cited span is non-blank.

### LOW — A2-PLAN-v1/SHA256SUMS covers 3 of 5 content files; `REPORT.md` — which the brief requires be read in full — is unpinned.

`sha256sum -c SHA256SUMS` is 3/3 OK and SHA256SUMS itself matches
`73adbeae…` as bound. But the directory contains five content files, and **`REPORT.md`
(5526 B) and `PROGRESS.log` (535 B) are not in the manifest**. REPORT.md carries the
recommendation summary, the gate disclaimers and the counting claims, and the brief names it
as required reading — so the document a reviewer must trust most is the one not sealed.
REPORT.md:21–26 does embed the four hashes, but that is self-reference, not coverage of
itself. Fix direction: extend SHA256SUMS to all delivered files, or state explicitly which
files the manifest is scoped to and why.

### LOW — A2-PLAN.md:280–305: the SC(a) decomposition gates H assignment but is the plan's own reading of qualifiers that were never retrieved.

The plan escalates REVIEW1:140 (which gates SC1–SC6 on *activation*, "independently of all
of it") to a pre-assignment requirement, and supplies its own per-SC definition of the
"(a) source-semantics parts". It flags the divergence openly (:297–301) and says "no
additional unprovided (a)/(b) subdivision is asserted" — yet the gate it builds rests on
exactly such a subdivision. The brief does say "with SC(a) first", so the framing is
inherited rather than invented, and the per-SC content is defensible engineering
interpretation labelled "Meaning here:". But it is load-bearing and unconfirmed: Slack
1793445107/1793445209 were supplied by identifier only (:3–5). Fix direction: mark the six
(a) definitions as the plan's proposed reading requiring Soma's confirmation before they
gate H assignment.

### LOW — A2-PLAN.md §3 is written in imperative mood throughout ("Replace `^0.30.0`", "Stage the archive under…").

Mitigated by :3–7 ("All actions below are future requirements, not commands authorized by
this document"), the §3 heading "separate later go only", and :224. No authority is actually
implied anywhere — I checked §1–§5 and found none. Raised only because §3 reads as a work
order when excerpted. Fix direction: a one-line restatement at the head of §3.

## Verification Gates

**No gate was assigned by the brief**, and none was run — therefore no gate receipt or PASS
is claimed. All work was read-only inspection; nothing was wrapped in
`orgasmic dispatch gate` because no assigned check existed and no probe produced a reusable
pass/fail receipt. No node/npm/bun/tsc, install, fetch, build, pack or network.

Binding first-line facts — **all verified**:
- `git rev-parse HEAD` = `f378eadffc9a02e2ecb5a4f30e266db9321db4f5` ✓
- `cd /srv/review-drop/tarn-rtaln-20261030/A2-PLAN-v1 && sha256sum -c SHA256SUMS` → **3/3 OK**;
  SHA256SUMS = `73adbeae60a85c4068bbada57ae04cfa6d5663202bce1ea80e89ed688da22bfa` ✓
- REVIEW1 = `057a7fafd0f330a60758d9924bc69599c1cd00c27ce083f056e36d6f829f40f0` ✓
- AF `rev-parse`: `a0970645…2464b`, `f8af03a8…9ca20`, `v0.30.0` = `45aa602cb2284feb3ad9228275d201a414e711ba` ✓
  (noted: the AF working checkout's HEAD is `e457abd4 chore(release): 0.21.0`, not v0.30.0; irrelevant to pinned reads)

Read in full: A2-PLAN.md, FACTS.md, OBSERVATIONS.txt, REPORT.md (plus PROGRESS.log and the
cited REVIEW1 regions).

**Citation spot-check — 251 unique locators, far beyond the 20 required.** Automated
resolution of every `[HACV]:path:line` spec in FACTS.md: **0 missing files, 0 out-of-bounds,
2 all-blank** (the LOW above). Manually content-checked, all accurate:
`H:package.json:33,9–18,32–46`; `H:package-lock.json:4,11,30–49`; `H:bun.lock:1–8,22,24–28`;
`H:web/package.json:11–23`; `A|C:package.json:2–3,9,10–11,12–22,27–39,50–57,66,68–70`;
`A:tsconfig.json:3–21`; `A|C:src/index.ts:221,226,149–154`;
`A:src/mcpl/registration-view.ts:6–7,12–21,63–69`; `V:package.json:3`; `V:src/index.ts:203–218`;
`H:src/bug-report.ts:831`; `H:src/history-semantic.ts:53`;
`H:src/update/install.ts:18–22,93–115,119–134`; `H:src/update/releases.ts:7–8,21–28,68–88`;
`H:scripts/lockfiles-agree.mjs:9–20,46–55,74–86`; `H:.github/workflows/ci.yml:46–50,99–103`;
`H:test/stubs/f-host-t2/run-mutants.mjs:9–10`; `check-compiler-controls.mjs:8`;
`H:src/recipient-policy.ts:3–13`.

**Independent re-derivations (not reuse of the plan's own numbers):**
1. **Import inventory** — my grep set equals the FACTS table with zero difference both ways:
   141 rows / 114 files. Split is 43 `src/` + 1 `scripts/` + 70 `test/` (→ MEDIUM above).
2. **54 preserved names** — extracted 54 distinct imported identifiers from the inventory
   statements; **set-identical** to the 54-symbol V map (`cmp` clean). Spot-resolved 24 V
   locators incl. all three wildcard routes; every claimed symbol present.
3. **"45 vs 43" correction — CORRECT.** `src/` files *mentioning* AF = **45**; files actually
   importing = **43**. The two extras are string-only, exactly as cited.
4. **"only 0.30.0" correction — CORRECT.** `^0.30.0` expands to `>=0.30.0 <0.31.0-0`, so it
   admits any 0.30.x; REVIEW1:36's "the only release the declared range admits" is too strong.
   FACTS' `-0` prerelease boundary is the more precise form and is right.
5. **Duplicate-AF / lock enumeration — reproduced exactly**: 168 / 188 / 180 / 181 packages,
   lockfileVersions 3 / 3 / 2 / 1; exactly one AF identity in each root lock, **zero in both
   web locks**, one root edge each (`^0.30.0`); no package in any lock declares AF as a
   dep/dev/peer/optional. No peer or nested AF requester exists today.
6. **`lockfiles-agree.mjs` — confirmed harder than FACTS states.** A `file:` spec falls
   through `pinOf` to `kind:"other"` (`:46–55`). With "other" on both sides the chain enters
   the `unknown` branch (`:157`) and `main()` returns **1** (`:181`). AF is explicitly in the
   compared chain set (`:5–6`). So the proposal makes the check **hard-fail**, which
   A2-PLAN:220 states accurately as "rejects"; FACTS' weaker "cannot certify" understates it.
   The script's own remedy text ("extend pinOf…") matches the plan's proposed support scope.
7. **No invented artifacts.** The only absolute path in A2-PLAN.md is the REVIEW1 evidence
   path in the basis paragraph — not a proposed tracked change. The only long hex strings are
   the verified REVIEW1 hash and the A/C pins. **No `.tgz` name, no `sha256-`/`sha512-`
   literal, no fabricated digest.** 8 `UNRESOLVED` / 3 `UNVERIFIED` markers; every unbuilt
   slot (S, recipe/toolchain, P, D, I, Q, release-set digest, lock serialization, verifier)
   is marked, and P is explicitly "deliberately no invented archive basename" (:189).

**Brief dimensions 2–6, assessed:**
- **(2) Identity — sound.** §1(1)–(5) is exactly source + recipe/toolchain + artifact digest
  + declaration closure + binding receipt. "Why 0.31.0 alone fails" (:75–77) is demonstrated,
  not asserted: A and C both declare `0.31.0` with byte-identical package/barrel yet are not
  established as the same artifact — I confirmed both manifests and both `src/index.ts:221,226`.
  Q is properly defined as a canonicalized transitive closure digest with the resolver/options
  digest recorded alongside, and "hashing only index.d.ts is insufficient" is correct.
- **(3) Options — fair.** All four get the same six-axis treatment; A vs B is honestly
  disambiguated (:112, the `file:` archive form *is* A). Every failure mode the brief names is
  present: npm local-tarball lock serialization/integrity (:207–211), Bun-vs-npm divergence
  (:212–219, "their locks need not be text-identical"), peer duplication (:100–102, :169,
  :204–205), prepare/prepack at install (:64–66, :237, :239 — and I confirmed A/C declare
  **no** `prepare` and **no** `prepack`, only `prepublishOnly`, so "prepublishOnly is not a
  guarantee that packing ran build" is exactly right), dist-vs-src exports (:68–70 — and the
  exports map I verified does select `bun`→`./src/index.ts` vs `types`/`import`→`dist/src/…`,
  so requiring both to derive from the same pre-build S is the correct consequence).
  The recommendation is sound.
- **(4) Install-time digest verification — achievable, and the gap is named.** Crucially the
  protocol does **not** depend on manager support: §3 step 2 computes SHA256/SHA512 over P's
  bytes and stops *before* manager execution on any mismatch or missing slot, and step 3
  mandates frozen/strict locks with lifecycle scripts disabled and network/registry denied.
  Manager-native verification is left UNRESOLVED pending a version probe, with ":214–216 if no
  SRI is emitted/enforced, the pre-install release-set D/I check is compulsory; do not invent a
  lock checksum or claim native verification happened." "Make verified inputs immutable during
  use" (:234) closes the verify→install TOCTOU window. This is the right structure.
- **(5) Ordering — matches the brief.** §4's table is A1 → A2 change scope (separate later go)
  → A3 → H assignment, with SC1–SC6 (a) settled first. The supersession of REVIEW1:140's
  "A2 → (A1,A3)" is declared openly at :12–14 and is coherent: A2 *planning* may precede A1;
  the A2 *change* cannot. REVIEW1:135–136 already treats A1 as independent of A2's range fix,
  so this is a reconciliation, not a contradiction. "Independent AF build/types work … is NOT
  blocked" is stated and correctly still subject to its own permission (:276–278).
- **(6) Authority — clean.** §5:331–336 refuses manifest/lock/git writes, acquisition, network,
  install, build/pack/publication, toolchain execution, post-build overlay, adoption and
  activation. I found no statement anywhere in §1–§5 implying an edit, install, fetch,
  publication or adoption had occurred or was permitted. The `recipientPolicy` refusal is
  confirmed unchanged at `H:src/recipient-policy.ts:3–13`.

## Unmet Criteria / test gaps

No acceptance criteria were set for this dispatch, and A2 is plan-only, so there is no
implementation to test. The gaps are evidence gaps, not missing verification duties:

- No executable receipt exists for any manager behaviour. The two decisive behavioural
  questions — whether the selected npm emits **and verifies** `integrity` for a relative
  `file:` tarball, and what Bun v2-lock emits for a local-file entry — are correctly marked
  UNRESOLVED but remain the plan's largest untested premise. The plan is safe against both
  outcomes (external D/I is compulsory either way), so this is a gap, not a defect.
- `lockfiles-agree.mjs` has no negative control for a `file:` pin today; the plan requires one
  in the later support scope, which is the right place for it.
- Finding 1 (updater/packed-host breakage) has **no covering prerequisite row** in §4.
- Declaration-closure Q, source/dist coherence, relocation and duplicate-identity receipts are
  all named but unproduced — correct for plan-only, and correctly not claimed as passed.

## Residual Risk

- **Highest:** the §4 table can be satisfied as written while self-update and packed-host
  installation are broken, because no row mentions them (Finding 1).
- The 43/71 mislabel propagates into REPORT.md:36 and would be inherited by any downstream
  document quoting the split.
- The two blank-line cites would be inherited silently by anyone trusting the advertised
  bound-check, which structurally cannot detect them.
- REPORT.md being outside SHA256SUMS means the recommendation summary a reviewer reads first
  is unsealed.
- SC(a) content gating H assignment derives from qualifiers never independently retrieved.
- Everything downstream of A1 remains genuinely unverified: no build, declarations, archive,
  manager behaviour, relocation or type identity has been demonstrated. The plan claims none
  of it, which is correct.

## Fix Directions

1. **(MEDIUM)** Add the induced updater/packed-host failure to §3's failure list and as an
   exit requirement on the later dependency-scope row in §4: either stage P inside the
   released tree and extend `files`, or keep the released manifest's AF spec distinct from
   the development one. State it as a consequence of the change, not only as "don't route
   the payload through the updater".
2. **(MEDIUM)** Correct FACTS.md:59 and REPORT.md:36 to "43 `src/` + 1 `scripts/` + 70
   `test/` = 114 files, 141 statements"; drop the "(43 production paths)" parenthetical or
   make it 44.
3. **(LOW)** Repoint the wildcard-route cites to `V:src/index.ts:19` and `:22`, and
   strengthen the citation self-check to assert the cited span is non-blank, not merely
   in-bounds.
4. **(LOW)** Extend SHA256SUMS to REPORT.md and PROGRESS.log, or state the manifest's scope.
5. **(LOW)** Mark the six SC(a) definitions as a proposed reading pending Soma's
   confirmation before they gate H assignment.
6. **(LOW)** Note at the head of §3 that its imperative mood describes future requirements.
7. **(optional)** Upgrade FACTS' `lockfiles-agree` wording from "cannot certify" to the
   verified "exits 1", matching A2-PLAN:220.

## What must hold before A2 counts as "accepted"

1. Fixes 1 and 2 applied (the named-failure gap and the inventory mislabel), and fixes 3–6
   applied or explicitly declined with reasons.
2. A re-sealed, fully-covering SHA256SUMS over the corrected set, re-verified by a reviewer.
3. Soma confirms the SC(a) decomposition, or §4 is reworded so H assignment does not depend
   on an unretrieved qualifier's content.
4. The operator records acceptance explicitly as *plan* acceptance — not A1 authorization,
   not permission to touch the host manifest or either lock, not a readiness or gate claim.
5. The §4 ordering (A1 → A2 change under separate go → A3 → H, SC(a) first) is carried
   forward as the binding sequence, superseding REVIEW1:140.

Nothing above authorizes any edit, manifest/lock change, install, acquisition, build,
publication or adoption, and nothing here is a readiness, qualification or gate claim.

---

# Addendum — operator Tarn, relaying qualifier Soma 1793445894

Handled before finalize. Read-only; no scope change. Verdict **unchanged: ACCEPT WITH
CHANGES**. This addendum adds one LOW finding under (a), one LOW under (c), one named gap
under (b), and one acceptance condition. Nothing below is authority, readiness or a gate.

## (a) Local portable release set only — no publication, public commit or distribution implied

**Checked every occurrence** of `publish|publication|distribut|public|commit|vendor|registry`
across A2-PLAN.md, FACTS.md and REPORT.md. **No wording implies publication or distribution.**
Each hit is benign or an explicit prohibition:

- `prepublishOnly` (A2-PLAN:64; FACTS:36) — AF's own script name, cited precisely to *deny* it
  is a pack/build receipt. Not an act of publishing.
- `PublishRefusalReason` (FACTS:89, :246) — an unrelated AF type in the import inventory.
- A2-PLAN:320 "No install, test, compiler, build, **publication** or runtime was run";
  :332 refuses "build/pack/**publication**"; :335 refuses "recreated AF **public** brand";
  :40 forbids "a renamed AF fork or recreated brands". These are refusals, not implications.
- "vendored" (:86, :175; REPORT:13) is used strictly in the packaging sense — an archive
  inside a local release set — and every option is explicitly scoped to "paths relative to a
  sealed release-set root … None assumes a registry" (:81–82).
- "commit" appears only as *source* commit S (:20, :27, :151, :154) — never as a git write.
  §5:331 refuses "manifest/lock/git writes".

**New LOW finding — A2-PLAN.md:186–189 with §3:200–205: the plan never states where P lives
relative to the repository, so "local-only, not committed" is implied but never said.**
P is defined only as "A1's actual archive path relative to the host manifest in the release
set", and §3 says "Stage the archive under that manifest's release subtree". The later edit
scope is enumerated as *package.json + the two root locks only* — so the archive is correctly
outside the tracked-change scope — but the plan nowhere says whether that release subtree is
tracked or untracked, nor affirmatively that the archive is a local artifact that is **not**
to be committed as a binary and **not** to be published. Grep confirms A2-PLAN.md contains no
`tracked`/`untracked`/`store`/`checked in` statement about P at all. Combined with Finding 1
(the `files` allowlist omits any vendor directory), a reader could reasonably reach either
conclusion. Fix direction: state explicitly that P is a local release-set artifact — not a
committed repository binary, not a published package, not distributed — and say whether its
staging subtree is tracked.

## (b) Independent byte verification before any package-manager or lifecycle action; refuse, never fall back

**The plan says this, in the right order.** Verification is step 2; the manager runs only in
step 3:
- byte verification first: ":232–234 Compute SHA256 and SHA512 SRI over P's exact bytes;
  require D and I from the reviewed receipt … **Mismatch/missing slot → stop before package
  manager execution.** Do not trust version or a same-name cache hit. Make verified inputs
  immutable during use." (that last clause closes the verify→install TOCTOU window)
- missing archive refuses: also :106–108 "missing/relocated archive, mismatched or stale
  D/I/Q … **All deny.**"
- incompatible lock semantics refuses: ":240 A manager unable to satisfy this profile fails;
  do not silently regenerate the lock or downgrade to semver installation"; and for the
  comparator, :220–224 (no bypass, negative controls required).
- no registry fallback: ":237–238 deny network and registry fallback"; ":196 do not use …
  a registry alias, fallback source"; ":108 a transitive requester resolving registry AF.
  All deny." ":209 No old registry URL or old 0.30 SRI."
- lifecycle disabled: ":237 lifecycle scripts disabled"; ":239 AF must not build on install";
  ":66 No deferred install-time compilation is acceptable"; :107 "unexpected lifecycle
  execution" denies.

**Do npm/Bun actually behave that way? Partly verifiable from the repo, partly a named gap.**

*Verifiable and favourable — the plan has receipts it does not cite.* The strict,
lifecycle-disabled profile step 3 requires is **already exercised on both managers in this
repository today**:
- `npm ci --ignore-scripts --no-audit --no-fund` (`H:.github/workflows/ci.yml:50`, and again
  at `:89`, `:90`), with the repo's own comment at `:46–48`: "npm ci never rewrites
  package-lock.json, and fails when the lock doesn't match package.json … where npm 11's
  `npm install` re-resolves it silently."
- `bun install --frozen-lockfile --ignore-scripts` (`:152`), comment at `:149`: "Frozen, bun
  refuses to install when package.json asks for anything [the lock lacks]."

So "frozen/strict locks, lifecycle scripts disabled" is demonstrably available on both
managers here. The `:46–48` comment is also direct in-repo evidence for the plan's
anti-fallback rule: plain `npm install` **does** silently re-resolve, which is precisely the
registry-drift path :240 forbids. The plan should cite these — it would convert part of its
UNVERIFIED column into existing evidence and name `npm install` (as opposed to `npm ci`) as
the concrete fallback vector.

*Named gap — I could not probe it, and must not.* Three behaviours remain genuinely unknown:
1. whether the selected npm emits **and verifies** `integrity` for a *relative `file:`
   tarball* on `npm ci` (as opposed to merely recording `resolved`);
2. what Bun lock v2 serializes for a local-file entry, and whether it carries any checksum;
3. whether `bun install --frozen-lockfile` accepts a `file:` tarball spec at all — and note
   that per `:149` a frozen Bun refusal would be **fail-closed**, which matches the plan's
   intent but is another induced CI consequence of the same class as Finding 1.

**My scope forbids executing npm/Bun/node**, so I ran no package-manager probe and claim no
receipt for any of the three. The plan correctly marks exactly these as UNRESOLVED
(:214–216) and, critically, does **not depend** on any of them: external D/I verification is
compulsory "even if a manager omits local integrity" (:93–94) and ":216 If no SRI is
emitted/enforced, the pre-install release-set D/I check is compulsory; do not invent a lock
checksum or claim native verification happened." **That is the correct structure** — the
refusal guarantee rests on the external check, not on manager behaviour. The gap is real but
does not weaken the refusal property. It must be closed by a version-pinned probe in the
later authorized scope, with negative controls (corrupt archive, missing archive, duplicate
resolution), as :248–251 already requires.

## (c) RANGE vs LOCKED resolution vs CHECKED source API — kept separate

These are three distinct objects and my findings above should be read against this split:

| Layer | What it actually is | Evidence |
| --- | --- | --- |
| **Manifest RANGE** | `"^0.30.0"` = `>=0.30.0 <0.31.0-0`. Excludes 0.31.0. **Does not mean only 0.30.0 exists** — it admits any stable 0.30.x | `H:package.json:33` |
| **Actual LOCKED resolution** | Exactly `0.30.0` with `sha512-Fqfjvlbx2mFFL61tAW8PZY9BszgBDiFOBI0JOBjKHCgIOJNiiNjKGBy3EDoQB5gyaBM4ET5XHll+2rq5tluA+g==` in **both** root locks — one concrete published artifact | `H:package-lock.json:31–33`; `H:bun.lock:22` |
| **CHECKED source API** | The **v0.30.0 git tag source** `45aa602cb2284feb3ad9228275d201a414e711ba`, and the 0.31.0 source at A/C. Source only | `V:package.json:3`; `V:src/index.ts`; `A|C:src/index.ts:221,226` |

The plan's "only 0.30.0" correction operates on the **RANGE** layer and is right there. The
reason the distinction has teeth: the range admits more than 0.30.0, yet the locked layer has
in fact resolved to 0.30.0 — so "only 0.30.0" is false as a *range* claim while being true as
a *current lock* fact. The plan corrects the range claim and does not conflate the two.

**New LOW finding — FACTS.md "0.30-era exported symbols to preserve": the 54-name
preservation check is against tag source, and the locked artifact is a different object that
nothing here connects to it.** I verified the tag `45aa602c` has **zero tracked `dist/`
paths**, so the 54 symbols were confirmed in `.ts` source at a git tag — not in the emitted
declarations of the published `0.30.0` tarball that `sha512-Fqfjvlbx…` identifies. Nothing in
the evidence establishes that the v0.30.0 tag source is the source of that published tarball.
FACTS hedges correctly in the right directions ("The following are source locators, not
emitted declaration verification"; ":48 not a proof about every hypothetical 0.30.x
publication"), so this is not an error — but the tag-vs-locked-artifact distinction is never
stated, and it is the one a reader is most likely to collapse. Fix direction: add one
sentence that the v0.30.0 tag is not established as the source of the locked 0.30.0 tarball,
so the preservation map is a source-API baseline only.

## (d) My spot cites are samples, not semantic validation of every locator

Stated explicitly. Precisely what I did and did not do:

- **Mechanical, exhaustive:** all 251 unique `[HACV]:path:line` specs I could parse from
  FACTS.md were resolved against the pinned blobs — 0 missing files, 0 out-of-bounds, 2
  all-blank. This proves *resolvability*, nothing about meaning.
- **Semantic, sampled:** roughly 40 locators were read and compared to the claim they
  support (listed under Verification Gates). These are **samples**. I did **not** semantically
  validate all ~251 locators, and nothing in this review should be read as certifying that
  every cite supports its sentence.
- **Exhaustive re-derivations (3):** the import inventory (141 rows / 114 files, zero set
  difference), the 54-name map (set-identical), and the four-lock AF enumeration
  (168/188/180/181, identities and edges). These three are complete, not sampled.
- My own parse counted 251 locators / 142 (pin,path) pairs against REPORT.md:36's "252 … 143
  pinned files". The one-apiece delta is within regex-boundary noise between two different
  extractors; **I do not claim it is an error** and did not pursue it.
- The two blank-line cites show the residual risk concretely: a bounds-only check cannot
  detect a cite that resolves but carries nothing. Unsampled locators retain that risk.

## (e) Even accepted, the unresolved slots stay unresolved

Affirmed, and this bounds the verdict. Acceptance of A2 under the conditions above would mean
only that **the strategy and its stated prerequisites are sound as a plan**. It would not
resolve, and this task does not clear:

- **S** (approved composed source/recipe), **P** (actual archive path), **D**/**I**
  (SHA256/SHA512 SRI over delivered bytes), **Q** (emitted declaration closure) — all still
  UNRESOLVED; A2-PLAN:309–314 and FACTS' "Unprovided slots" section mark every one, and no
  invented filename or digest appears anywhere (verified).
- relocation and type-identity receipts, release-set manifest/closure digest, the literal
  manifest spec bytes, and the emitted local-file lock serialization — all UNRESOLVED.
- No build, pack, install, declaration emission, manager probe, relocation or type resolution
  has been demonstrated by anyone, here or in the reviewed round.
- Later host manifest/lock work is **covered engineering only after its prerequisites**
  (actual A1 receipt + a specifically authorized dependency scope, per §4). **This review does
  not clear it, does not authorize it, and is not a gate PASS, acceptance or readiness
  claim.** Old 0.30 integrity must never fill any new slot (:314).

## Addendum effect on the verdict

Verdict unchanged: **ACCEPT WITH CHANGES**. Findings now 2 MEDIUM + 6 LOW (adding the
(a) local-only/P-storage statement and the (c) tag-vs-locked-artifact statement). Added
acceptance condition:

6. The plan states affirmatively that P is a **local release-set artifact only** — not a
   committed repository binary, not published, not distributed — and says whether its staging
   subtree is tracked; and records that the v0.30.0 tag is not established as the source of
   the locked 0.30.0 tarball. Separately, the three unknown npm/Bun local-file behaviours are
   carried as an explicit probe item (with negative controls) in the later authorized scope,
   since no reviewer here may execute a package manager.
