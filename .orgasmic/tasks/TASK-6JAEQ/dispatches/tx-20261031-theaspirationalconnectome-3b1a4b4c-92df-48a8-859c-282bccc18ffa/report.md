# Review 2 — TASK-6JAEQ fix round 1 (bounded delta, LOCAL ONLY)

Range `9c6f6ea1de60f2ac749f9f3184adcc71f128253f..c9f0991d20e9e2476f7ff57b09f1f2685671d97a`.
Independently confirmed diff shape: 8 files, **+200/-18**, 0 tracked deletions.
Implementer report `/srv/review-drop/TASK-6JAEQ-FIX1.md` read in full, SHA256 verified
`f1dfd0e2…df95`; previous review `TASK-6JAEQ-REVIEW1.md` SHA256 verified `25bea49a…4740`.

## Verdict

**APPROVE** the bounded compiler-core delta — scaffolding only, not functional H.3 /
runtime-binding completion. No HIGH, no MEDIUM. Four LOW findings, all documentation /
evidence-legibility / test-scope, none blocking.

Review-1's MEDIUM (inference ceiling) is **resolved** on the rule the operator Tarn
confirmed (with Soma's condition): explicit lane allowance only, no push-ceiling borrow.
I re-derived that rule from the docs myself before accepting it (see below), reproduced
controls a–d against the real head source, and killed all three required mutants with the
named witness. LOW-1…LOW-4 are each reproduced independently. These are the task
operator's engineering calls, not owner decisions.

## Findings

### LOW-1 (docs, precision) `src/recipient-policy.ts:32` / `docs/design/recipient-policy-staging.md:11-14`
The staging doc says "**Any** configured `recipientPolicy`, including null/malformed
values, refuses". The scanner's refusal site is narrower: `path.length === 2 &&
path[0] === serversField && key === 'recipientPolicy'`, i.e. an own key on a *server
definition* under the servers field. A top-level `recipientPolicy` is not refused.
Reproduced: case `policy-word-top-level`
(`{"recipientPolicy":{"a":1},"mcplServers":{"edge":{"command":"node"}}}`) is byte-identical
base vs head and returns `ok:true` with the key collected into `otherKeys`
(`gate-rv-overlay-differential`). Impact is nil today — nothing reads a top-level
`recipientPolicy`, there is no schema position for it, and the per-server
`refuseUnreleasedRecipientPolicy` covers every read position — but the doc sentence
overstates the guard's scope, which matters as T2 reads this doc as the contract.
**Fix direction:** scope the sentence to server definitions under the servers field,
or extend the walker to any `recipientPolicy` key and add a positive control.

### LOW-2 (test, declared gap) `test/fixtures/generic-recipient/check-scope-contract.mjs:336-343`
The `--docs-contract` naming/neutrality oracle scans nine public inputs plus the
`f-host-2a/v0` stub set. It does **not** include `docs/design/recipient-policy-staging.md`
or anything under `test/stubs/f-host-3/` — i.e. the entire text surface this round added
(+200 lines) is outside the automated scan, and the gate is already `PARTIAL`
(no nonempty `SCOPE_FORBIDDEN_NAMES`). The implementer declares this. Mitigated, not
closed, by my own scan of the delta: 200 added lines, zero hits for operator/worker/owner
names, task/ask/run identifiers, machine-local or Windows paths, credentials, or
non-loopback URLs/emails/IPs (`gate-rv-internal-names`). Pre-existing hits elsewhere in
`src/mcpl-config.ts` (bearer-token *field names*, the real `@animalabs/agent-framework`
import) are untouched by this delta.
**Fix direction:** add the staging doc and `test/stubs/f-host-3/**` to the
`docsContract()` input list; the full naming policy still needs the absent forbidden-name
list, so the gate stays PARTIAL regardless.

### LOW-3 (evidence legibility) `$REVIEWED/quiescence.py:69`
`print('Path-limited lsof:', r.returncode, 'holders output bytes', len(r.stdout), …)`
renders as `Path-limited lsof: 1 holders output bytes 0` in
`gate-operator-witness-quiescence-01M683754QEJZGQVYXCPXQX6ZW.log`. Read literally that
says one holder was found on a gate that exited 0; it actually means lsof returncode 1
with 0 bytes of output, i.e. **no** holders. The adjacent byte counts and the report text
("no holders/no stderr") disambiguate it, so the receipt is not dishonest — but this is an
operator-facing quiescence line on the one gate that authorised staging, and the
misreading is the dangerous direction.
**Fix direction:** print `exit=<rc> holders=<n>` separately.

### LOW-4 (docs, carried gap) `src/bug-report.ts:341,346,355-357`
`src/bug-report.ts` is unchanged (0 diff lines) — correctly, no bypass was added. The
staging doc now records the `:346` `mergeRecipeServers` case. It does not mention that the
same function swallows the named refusal on **two further** paths: `:341`
`loadMcplServers` → "the servers file couldn't be read", and `:355` `applyAgentOverlay` →
"the agent overlay couldn't be read". The `:355` path is newly reachable from this round's
LOW-2 change (policy hidden behind duplicate JSON ids/maps in an *overlay* file now
refuses where it previously parsed). On all three the operator's "loud refusal with a
named reason" degrades to a generic diagnostic note. Not a regression — the error identity
changes (`recipient_policy_operator_only` → `recipient_policy_integration_pending`) but
both were already caught by the same handler. The named reason **is** preserved on the
real loader paths: `src/index.ts:478` `overlayWarnings` propagates it unguarded (boot
stops loudly), and `src/modules/mcpl-admin-module.ts:854` → `reasonText:71` surfaces
`error.message` verbatim for non-`OverlayUnreadableError`.
**Fix direction:** extend the doc's unmet-coverage paragraph to `:341` and `:355-357`, or
have `bug-report.ts` re-emit the policy reason when the caught error carries
`RECIPIENT_POLICY_PENDING`.

## Checks I performed myself (not taken from the report)

**(1) MEDIUM — inference rule.** Read `docs/design/generic-recipient-host.md:196-214` and
`docs/design/generic-recipient-scope-contract.md:222-234` directly. Host:204-208 gives
channel-less push its own rule ("may wake only under `wakeWithoutChannel` plus existing
wake policy") and separately states "Inference can be explicitly allowed by lane, with no
channel reply intent"; T1:229-233 keeps "the baseline lane-specific rules" and adds no
channel-less escape. The implemented rule matches: `src/recipient-admission.ts:102`
(exact-key `!==` identity + `signal.aborted` + `lanes.has`), `:106`/`:118`
(featureSet/messages schema), `:123` returns `{store:true,mayWake:true}` before the
`tags`/`addressed`/`wakeWithoutChannel` logic at `:132-137`. `read`/`wake`/
`wakeWithoutChannel`/addressed are not consulted on the inference path and the push
ceiling is not borrowed.

*A's guards stay outside the host.* `grep -niE 'grant|hook|render|queue|isClosed|
validateInbound|isInHook'` over `src/recipient-{admission,policy,presentation,scope,
binding}.ts` hits **comments only** (`:20`, `:22`, `:84`, `:121`) — no invented or
duplicated grant check, no implementation. `transportEpoch` appears only in the `Key`
type, so currentness is reference identity, not a host-side epoch comparison. I confirmed
the docs' claim that A's implementation is outside the copied contract: grep for
`validateInbound|isInHook|queue.isClosed|featureSetManager` over
`/srv/review-drop/af-a869bb3a-contract/` returns **nothing**. The docs say real-path
enforcement "stays PENDING" and do **not** claim A's guards were runtime-tested; the
report attributes the `framework.ts`/`inference-router.ts` line numbers to the operator
rather than to a reproduced receipt. The interface doc says "These are **not** all-lane
ceilings", so nothing advertises all-lane ceilings to T2.

*Controls a–d, reproduced against the real head-root source* (my own probe, not the
implementer's test file — `gate-rv-inference-controls`):
- (a) lanes `{channels/incoming, push/event}`, valid inference item → `{store:false,
  mayWake:false, reason:'admission_denied'}`.
- (b) lanes `{inference/request}`, `read:()=>false`, `wake:()=>false`,
  `wakeWithoutChannel:false`, `lookup:()=>undefined`, `readable:()=>false` →
  `{store:true,mayWake:true}`; also true with `tags:[]` (no addressed evidence).
- (c) all refused: wrong key, reconstructed-equal key `{...k}`, stale epoch
  `{...k,transportEpoch:0}`, pre-aborted signal, `featureSet:''`, `messages:[{}]`,
  missing `messages`, smuggled `delivery:'selected'`.
- (d) no `replyIntent`/`destination`/`channelId`/`reply` on either the allowed or the
  denied inference verdict; key set is exactly `{store,mayWake[,reason]}`.
- extra: with `read`/`wake`/`lookup`/`readable` all set to *throw*, the inference path
  still returns `{store:true,mayWake:true}` — the channel ceilings are provably never
  consulted; `wakeWithoutChannel:true` does not change the verdict either.

*Three inference mutants, reproduced in an isolated scratch copy of the head source*
(`gate-rv-inference-mutants`). Unmutated `test/recipient-policy.test.ts` passes; each
mutant is unique-site, applied, and the source byte-restored. Each is killed **by the
right test** — the named witness `inference explicit lane exemption keeps key abort and
schema guards, never a reply intent` is among the failures in all three cases:
- `unconditional-true-before-guard` → fails the inference witness **and** "malformed lane
  evidence and unsupported selectors".
- `lane-check-removed` (`!lanes.has(input.lane)` → `false`) → fails the inference witness
  **and** "ambient read/wake independent".
- `push-ceiling-borrow` (apply `wakeWithoutChannel && addressed`) → fails the inference
  witness **only** — exactly the discrimination control (b) was added for.
The implementer's own runner additionally asserts a unique mutation site and that the
`(fail)` line names the intended witness (`run-mutants.mjs:41,49`), and its receipt shows
24/24 killed (old 21 + 3).

**(2) LOW-1 tombstone.** `src/mcpl-config.ts:342-345`: the
`Object.hasOwn(e,'recipientPolicy')` throw now precedes the `disabled === true` early
return. Differential probe base `d7154d79` vs head `c9f0991d`
(`gate-rv-overlay-differential`): `{disabled:true, recipientPolicy:<obj|null|false|
undefined>}` now throws `recipient_policy_operator_only` via both `readOverlayEntry` and
`composeOverlay` (5 cases changed); a policy-free `{disabled:true}` is **unchanged** —
`{entry:{disabled:true},notes:[]}`, and `composeOverlay` still returns `[]`.

**(3) LOW-2 overlay scanner — explicitly checked for a contract change; there is none.**
`readAgentOverlayFile` now calls `parseRecipientConfigJson(raw,'mcplServers')`
(`src/mcpl-config.ts:265`). Mechanically this cannot change policy-free behaviour:
`parseRecipientConfigJson` delegates to the same `parseHandWrittenJson` and
**returns early** unless a `"recipientPolicy"` string token exists in the text
(`recipient-policy.ts:22-24`); `parseHandWrittenJson` only ever throws `SyntaxError` from
`JSON.parse`, so the new `instanceof SyntaxError` → `OverlayUnreadableError('not-json')`
wrapping is exactly equivalent for every syntax error. Proven, not just argued: a 22-case
× 2-function differential probe over base `d7154d79` and head `c9f0991d` gives **43
identical / 11 changed**, and every one of the 11 carries a `recipientPolicy`. Identical
includes plain, BOM, unknown top keys, unknown server keys, nested unknown structures,
tombstone, grant drop-with-note, `{broken` → "isn't valid JSON", empty string, `null`
root, array root, missing servers field — and critically **all seven cases where the
literal token "recipientPolicy" appears in a non-policy position**: as a command arg, as
a server id, as a deep `env` key, at top level, inside an unknown top-level object,
`P`-escaped in a string value, and inside an array of objects, plus a
numbers/escapes/`recipientPolicyLike` stress case. The token walker neither crashes nor
over-refuses on those. **So this is not a contract change for current configs → LOW, not
HIGH.** Changed cases: `{"mcplServers":{"edge":{"recipientPolicy":…}}}` and both
duplicate-key hiding variants (duplicate server id, duplicate `mcplServers` root), which
now refuse with the named `recipient_policy_integration_pending: dynamic selector schema
and qualified AF release missing (INT-1; …)`.

**(4) LOW-3 v1 compiler controls.** Independently reproduced with the pinned
`tsc 7.0.2` (`gate-rv-v1-controls`). Baseline `-p test/stubs/f-host-3/v1/tsconfig.json`
passes, so all three `@ts-expect-error` directives (lines 13/15/17) are used and none is
unused. Removing each in turn, keeping line numbers stable, yields exactly one diagnostic
at the exact expected line: `(14,33) TS2353` 'accountId' not in `VerifiedScope`;
`(16,23) TS2339` property read on `VerifiedScope`; `(18,7) TS2741`
`[verifiedScopeIdentity]` missing in `{}`. Matches the implementer's receipt
byte-for-byte on codes and positions; `typecheck.ts` restored.

**(5) LOW-4 feature-off byte-identity.** Re-ran the probe myself across both cells
(`gate-rv-feature-off`): base cell/root `d7154d79` vs head cell/root `c9f0991d`,
`cmp` → **BYTE_IDENTICAL**, both
`5af23aaa62c8996f61dc32caf50443b233f92b6e0675a68f98cbb88d97936e82` — exactly the claimed
hash. Normalization scope checked directly: `normalize()` replaces only the ephemeral
`mkdtemp` directory and the `127.0.0.1:<random-port>` recipe URL, and is applied only to
error messages and captured `console.warn` text — **not** to returned values. Since both
the temp dir and the port differ between the two runs, any unnormalized ephemeral text in
a *value* would have broken `cmp`; it passed, and `grep` over the output finds no `/tmp/`
path and no `127.0.0.1:<port>`, only `<probe-dir>`/`<recipe-url>` placeholders. The
normalization therefore cannot mask a semantic difference. Coverage is real: file/HTTP/
saved loaders × plain/BOM/invalid/unknown-keys, with the unknown-key warning text
captured, saved-BOM still refusing ("it isn't JSON"), the policy-free overlay-file
reading, and the unconfigured `inference` server emerging with its original
`enabledCapabilities`/`enabledFeatureSets` and no callback.

**(6) Gate binding classes.** All three labels check out (`gate-rv-binding-classes`):
- *Original base* — `tmp-recipient/build/base` src+test = **423** tracked files of
  `d7154d79`, every sha256 matching the git blob, **0** missing / **0** mismatched /
  **0** extra-or-stale. It contains no `src/recipient-*` and no `test/stubs/f-host-3`, so
  "v1 gates N/A on base, not a claimed base pass" is honest; the recorded
  `fix-base-v0-*` gates target `f-host-2a` only.
- *Reused old-head* — `reused-old-head-observations.json` tags **every** entry
  `"REUSED old-head observation ONLY; not proof of fix-head"` with the `9c6f6ea1` commit,
  the prior run's log path and the log sha256. No `9c6f6ea1` observation is presented as a
  fresh-head gate; all 22 recorded gates are at `c9f0991d`.
- *Fresh head* — `tmp-recipient/build/head` src+test = **437** tracked files of
  `c9f0991d`, all matching, closed (0 missing / 0 mismatched / 0 extra-or-stale) both
  before I started and after all my probes. `staged-inputs.json` is exactly the delta's 7
  src/test paths (set-equal to `git diff --name-only`, 0 deletions), with every
  `before_sha256` equal to the `9c6f6ea1` blob and every `after_sha256` equal to the
  `c9f0991d` blob.
- *Closure* — the digest **`6b42678559f175f926db4a39c1527f2415c37bab1392be6a74ae95446233ed64`**
  is byte-identical across the previous round's inherited `installed-{base,head}.json` and
  all 8 of this round's `installed-{base,head}-{before,approved-before-stage,after-stage,
  after-gates}.json`; `controlled-stage.py:17` asserts equality against the *inherited*
  file, so no closure was regenerated. I also verified the manifest against the live disk:
  16820/16820 entries present, 0 missing, 0 extra, all 24 symlink targets and all file
  sizes matching, and 500 randomly sampled content hashes all matching. Toolchain pins
  confirmed on disk: bun 1.4.3 `7ce7d6b6…f24b`, node v22.23.3 `fde6a4bf…48f8`, tsc 7.0.2.
- *Honest failures retained* — `stage-bindings` exit 1 ("head root is busy or lsof
  incomplete; no staging"), `supplementary-quiescence` exit 1 (the parser `ValueError`),
  `quiescence-corrected-status-parser` exit 1 ("unexplained unreadable pid; STOP, no
  staging"). None relabelled. Ordering is right: staging gate at 04:24:25, first head
  gate at 04:25:12.

**(7) Internal-names scan of the diff** (`gate-rv-internal-names`): 200 added lines, zero
hits. Only loopback `127.0.0.1` / `example.invalid` appear, both in the new test probe.
Commit metadata is the project's GitHub noreply identity with the required coauthor
trailer; no operator/worker/owner names, no `/srv`, `/home`, task or ask identifiers.

## Verification Gates

Reused (implementer receipts at `c9f0991d`, read not re-run): `fix-head-mutants` 24
killed, `fix-head-focused` 93 pass/0 fail, `fix-head-suite` 4600/0, `fix-base-suite`
4579/0, `fix-head-v1-controls` TS2353/2339/2741 at (14,33)/(16,23)/(18,7),
`fix-head-docs-contract` "9 public files; 11 rejected controls; 0 injected names;
PARTIAL naming", `controlled-first-staging`, `fix-final-bindings`,
`operator-witness-quiescence`, plus the retained exit-1 receipts above.

My own gates, all exit 0, logs in `$ORGASMIC_EVIDENCE_DIR`:

| Gate | What it proves |
| --- | --- |
| `rv-inference-controls` | controls a–d on real head source + channel ceilings never consulted |
| `rv-inference-mutants` | 3 inference mutants killed, each by the named witness; source restored |
| `rv-overlay-differential` | 22-case × 2-function base↔head diff: 43 identical / 11 changed, all policy-bearing |
| `rv-v1-controls` | tsc 7.0.2, TS2353@14 / TS2339@16 / TS2741@18, baseline clean |
| `rv-feature-off` | base↔head byte-identical, sha `5af23aaa…6e82` |
| `rv-binding-classes` | closed 423/437 inventories, closure manifest `6b426785…` vs live disk |
| `rv-internal-names` | 200 added lines, 0 internal-name hits |

Bounded throughout via the inherited `bounded.sh` (16G scope, swap 0, `timeout 3000s`,
64 MiB cap) and `cell.sh` (scrubbed env, existing roots). No install, no lockfile touch,
no new root. Worktree left clean at `c9f0991d` with 0 `git status` entries; both build
roots re-verified closed after my probes; scratch copies deleted.

## Unmet Criteria / test gaps

Nothing from fix round 1 is unaddressed. Carried forward, correctly declared PENDING:

- **Runtime binding is not implemented and not tested.** Canonical selector representation
  is an INPUT GAP; INT-1 composed release and reviewed S4Q8F integration absent; installed
  AF is 0.30.0, not the pinned `869bb3ad` release.
- **Protected-overlay guards: unit-only.** Production loading fail-closes before those
  guards are reachable, so they must be re-proved on the real path when interim refusal is
  lifted. This was Review 1's unmet item and remains unmet by design.
- **A's grant/hook/render/queue/epoch guards are unverified here** and their
  implementation is absent from the copied contract dir. The `framework.ts` /
  `inference-router.ts` line references are operator-attributed, not reproduced.
- **No real trusted scope verifier/constructors/accessors**; `VerifiedScope` opacity is
  compile-time only (which is precisely what the three v1 controls pin).
- The naming oracle does not cover this round's text (LOW-2); `--docs-contract` is PARTIAL
  for want of a forbidden-name list; the 8-file diff inspection is not a whole-history
  privacy qualification.
- Feature-off proves production *functions*, not transport / no-await / wire behaviour;
  B (.4), D (.5), storage/native/dynamic parity stay out of scope.
- Inference items are not key-closed: a malformed `tags` value is accepted on the
  inference lane because `:123` returns before the `tags` validation at `:132-133`.
  Pre-existing at `9c6f6ea1`, outside this delta, and inert (inference never reads `tags`)
  — noted so it is not mistaken for new coverage.

## Residual Risk

- The approved slice is **interim scaffolding**. Every configured `recipientPolicy` refuses,
  so the compiler is unreachable through production loading; its correctness is pinned by
  stub-level controls and 24 mutants, not by any real A traffic. Do not read this approval
  as "H.3 complete".
- Staging quiescence is point-in-time evidence, not a lock. 252–258 root-owned pids and
  three same-user unreadable services (1830/39736, 1907/39737, 3182/40128) rest on the
  operator's separately attributed root witness at 2026-10-31T04:23:31Z. I did not
  re-derive that witness; it is operator evidence, and LOW-3 is about how its sibling line
  reads.
- Fail-closed makes an overlay-file policy abort boot at `src/index.ts:478` (loud and
  named — intended), while `bug-report.ts` flattens the same refusal to generic notes
  (LOW-4).
- **LOCAL ONLY.** Private ancestry retained; nothing here authorises a push. A public
  submission still needs a fresh clean tree on a public base plus a privacy check of the
  whole publication range; my clean 200-line delta scan is not that qualification.

## Fix Directions

1. LOW-1 — scope the staging doc's "any configured `recipientPolicy`" to server
   definitions under the servers field, or widen the walker and add a control.
2. LOW-2 — add `docs/design/recipient-policy-staging.md` and `test/stubs/f-host-3/**` to
   `docsContract()`'s input list (`check-scope-contract.mjs:336-343`).
3. LOW-3 — in `quiescence.py:69`, print `exit=<rc> holders=<n>` instead of interpolating
   the return code before the word "holders".
4. LOW-4 — extend the doc's unmet-coverage paragraph to `bug-report.ts:341` and
   `:355-357`, or re-emit the policy reason when the caught error carries
   `RECIPIENT_POLICY_PENDING`.

All four are documentation / evidence-text / test-scope changes. None needs a source
behaviour change, and none blocks this round.
