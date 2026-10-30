# TASK-E9C1M review — F-host design + shared synthetic connector contract

Reviewed: `docs/design/generic-recipient-host.md`, `docs/design/generic-recipient-fixtures.md`
at `9f153044ce63b8da719a0132c0817901f1031216` (worktree `task-e9c1m`, parent
`2c39662289be4cd5a150222e8456ff5577ce7622`, diff = 2 added files, 670 lines, nothing else).
Read-only. No edits, commits or files inside the worktree.

## Verdict

**APPROVE.** Acceptance is met and the design is substantively accurate against the actual
A/B/D APIs — I re-derived every API claim from source at the named pins and found **no
invented framework API**. Six MEDIUM findings should be fixed before F-host.2 starts
(one is a plain arithmetic error in the budget; two are real contract holes that would bite
during implementation). No HIGH: nothing here is a shipped bug, security defect or unmet
acceptance criterion.

### What I independently confirmed (not taken from the worker's report)

- **A** `1d990e41`: `InboundAdmissionConfig = {timeoutMs, bind, isReplyIntent, initializeAttestation?}`
  (`src/mcpl/inbound-admission.ts:22-30`); `bind(key) => {connection, admit}` must return the
  *identical* frozen key (`:106-116`); `AdmissionVerdict` is exactly the closed union the doc
  quotes (`:13-14`); `MAX_ADMISSION_TIMEOUT_MS=60000`, `MAX_PENDING_ADMISSIONS=64`, timeout
  integer 1..60000 (`:6-7,42-48`); `admission_stale` for superseded epochs. All as documented.
- **B** `bf6e8e0e`: `McplServerConfig.replyAuthority` (`src/mcpl/types.ts:231-233`, requires
  `inboundAdmission`); `resolve` returns `{channelId}|null`, framework supplies `serverId`
  (`src/mcpl/reply-routing.ts:10-16,118`); separate 64-pending cap and own deadline (`:100-110`).
  The doc's statement that `authorize` is "optional in the requested contract but required at the
  inspected pin" is **correct** (`:16` declares it required) and is flagged honestly rather than
  papered over. The reconnect rule is accurate against the real implementation:
  `valid()` requires `requiredAdmission.isCurrent(key)` on the *original* key plus the live
  descriptor and `channels.publish` grant (`src/framework.ts:34987-34995`), so a post-reconnect
  binding genuinely can only be the same frozen destination or private-with-notice.
- **D** `a5b02a07`: `FrameworkConfig.contextOnlyStorage` boolean, checked before any offer
  (`src/framework.ts:4582-4621`); `inboundDelivery` reserved on **all** writes under the profile
  (`:17324-17341`) — the doc's "not isolated to opted-in connections" is right; one-way reader
  floor and the three wire deltas match the doc.
- **Host base**: insertion point `src/index.ts:470-538` is exactly the
  merge → overlay → `AgentFramework.create` region. The doc's claim that `enabledCapabilities`
  is operator authority only on recipe-self-defined servers with no same-id file entry matches
  `src/mcpl-config.ts:295-303` verbatim. Upstream main carries **no** recipient protocol at all
  (`grep -rn slackRecipientRouting src/` → empty; no `host-recipient-protocol.ts`).
- **Fixture/evidence identities**: legacy oracle installed tree is AF 0.22.2 / CM 0.14.1 /
  Chronicle 0.4.0 (read from `node_modules/@animalabs/*/package.json`) — matches §5.1.
  Host lock resolves CM **0.24.0** / Chronicle 0.5.1 exactly, so "CM exactly 0.24.0" is correct.
  S0 `452dde05`, S1 `3152457a`, SNAPSHOT `e515245c…`, entry `ffecd468…` all match the supplied
  O4 / SLACK-STATIC reports. MCP `2024-11-05` and MCPL `"0.5"` are the real constants
  (`protocol-family.ts:34`, `types.ts:125`). Policy-shape SHA-256 matches as reported.
- **Brief items 3, 4, 7**: the stale-epoch loss gate carries all five required properties
  (outbox before transmission, commit only on final per-item host acceptance not the A verdict,
  known pre-storage refusal ≠ uncertain ACK, no retro-replay, explicit "no exactly-once");
  the reconnect rule is as the manager specified, with no invented external notice route; and the
  matrix covers **all 8** O4 F-host bullets and **all 6** F-Slack bullets, plus H04/H10/H11/H12
  beyond O4.

## Findings

### MEDIUM

**MEDIUM `docs/design/generic-recipient-host.md:426` (correctness/docs): the hour totals are wrong by one table row.**
The table (`:418-424`) gives .2=10–16, .3=8–12, .4=8–12, .5=8–12, G=8–12, fence .6=10–16, design .1=6–8.
Excluding this document and the conditional fence the sum is **42–64 h**, and **52–80 h** with the
fence — not the stated "50–76 h … or 60–92 h". No reading reproduces the stated figures (all rows
= 58–88; including .1 but not .6 = 48–72). Verified with `python3`. A manager budgeting the slice
from this line over-reserves 8–12 h. Fix: restate 42–64 / 52–80, or add the missing row explicitly.

**MEDIUM `generic-recipient-host.md:118` + `:134` (correctness): the config surface omits AF's legacy-protocol-family restriction on `inboundAdmission`.**
`serverConfigProblems` rejects the configuration outright when the resolved family is not legacy:
`protocol-family.ts:700-703` → `'inboundAdmission is unsupported by the modern protocol family'`,
and family comes from config — `http(s)://` url ⇒ modern, stdio ⇒ `config.protocol ?? 'legacy'`
(`:604-605`). An operator who sets `recipientPolicy` on an `https://` MCPL server, or on a stdio
server with `protocol:'modern'`, gets a framework config problem that §2's validation list
(`:134-136`) never anticipates and the design never maps to an operator-readable refusal.
The fixture harness is stdio + loopback WebSocket, i.e. legacy only, so there is no negative
control either. Fix: add the family precondition to the §2 validation list with a named startup
error, and add a fixture row ("`recipientPolicy` on a modern-family connection fails configuration
before connecting").

**MEDIUM `docs/design/generic-recipient-fixtures.md:138` (correctness): the outbox contract's "unambiguous accepted result" is unobtainable on the notification form of `channels/incoming`.**
A's own doc is explicit: *"Notifications have no wire receipt; no application status is sent for a
refusal"* (`agent-framework@1d990e41:docs/required-inbound-admission.md`), and refusals of
notifications only emit a host-side trace. The state table nonetheless makes a *final per-item host
receipt* the sole commit trigger. A connector that delivers outbox-tracked items as notifications
therefore either never commits (unbounded outbox) or infers acceptance from silence — exactly the
failure the gate exists to prevent. The fixtures doc shows awareness elsewhere
(`H03` "notification trace only", `H09` "a missing receipt is not an acceptance assertion") but
never states the requirement. Fix: require request-form (`id`-bearing) delivery for any item the
connector tracks in the outbox, and add a negative fixture asserting a notification-delivered item
is never settled.

**MEDIUM `generic-recipient-host.md:304-308` (test/correctness): the old-reader probe's "old process" tree is ambiguous in the oracle fixture itself.**
§5.1 names AF 0.22.2 / CM 0.14.1 / Chronicle 0.4.0 and also requires *"Node compiled-import and Bun
source routes separately"* and *"never mix the old and new dependency trees"*. But in
`/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host` the two lockfiles disagree:
`package-lock.json` resolves **AF 0.21.0 / CM 0.13.0**, while `bun.lock` and the installed
`node_modules` are **AF 0.22.2 (codeload `d3538d1f`) / CM 0.14.1**. A Node route reconstructed with
`npm ci` would therefore measure a *different* old reader than the one the doc names, silently
invalidating the barrier/no-barrier conclusion. Fix: state that the npm lock is stale, pin the exact
old payload hashes per entry point, and require the probe to assert the installed versions before
each attempt (the design already requires recording payload hashes — make the assertion normative).

**MEDIUM `generic-recipient-host.md:358-360` (design): skipping F-host.6 on a native barrier has no requalification trigger.**
§5.2 permits omitting the fence "if a sufficient native barrier is demonstrated for every supported
launch", while §5.1 item 6 correctly warns that native physical-format refusal "is not an
application-reader guarantee for every future store or arbitrary SDK version". Nothing then binds
the decision to the release set: a later CM/Chronicle/AF bump in the manifest can void the barrier
that justified skipping the fence, with no gate to catch it. Fix: make the no-fence decision an
artifact of the release-set manifest and require re-running 5.1 (or re-enabling F-host.6) whenever
any storage-layer payload hash in that manifest changes.

**MEDIUM `generic-recipient-fixtures.md:6,56,155` and `generic-recipient-host.md:101,238,293,348` (docs/scope, brief item 9): internal machine paths and drop handles are committed as *normative* contract inputs.**
`fixtures:56` reads **"Read only `/srv/review-drop/soma-rtaln-policy-shape-20261030/POLICY-SHAPES.json`"**
— a mandatory test input that exists only on this box, in a file destined for a PR against
`anima-research/connectome-host` (remotes confirmed). Same for the sealed fixture directory
(`:155`), `/srv/ci-fixtures/pr361-v1-…` (`host:238`) and the three `/srv/review-drop/tarn-rtaln-…`
report paths. The host doc anticipates the objection for dependency artifacts (`:70-73`:
"no private machine paths") and tries to carve these out as "review-only … not install locations",
but a parameter set the contract orders you to read is an input, not a citation. No deployed agent
name, credential or hostname leaks (checked: no `Alnold`, no `/home/…`), so this is reproducibility
+ internal-infra exposure, not a secret. Fix: vendor the synthetic shapes into the repo (or an
in-repo fixture dir) and keep the SHA-256s as the identity; demote the `/srv/*` report paths to
"supplied privately, identified by SHA-256" without the absolute path.

**MEDIUM `generic-recipient-host.md:419` (design, brief item 8): F-host.2 bundles the harness with the gating store probe, and is probably underestimated.**
10–16 h must cover a deterministic synthetic MCPL peer (initialize/grants/tools/channel
register-lifecycle-incoming-publish-typing/push/inference streaming, a barrier control pipe, a
WebSocket variant) **and** a syscall-instrumented old/new store probe with before/after per-file
SHA-256/mode/identity inventories across two runtimes. Those are unrelated skill sets, and the
probe result is what gates .5, .6 and the whole store decision — so the critical-path answer sits
behind the harness build. The rest of the slicing and its dependency arrows are sound. Fix: split
into F-host.2a (harness) and F-host.2b (store probe), let 2b start immediately, and widen 2b to
≈10–16 h on its own.

### LOW

**LOW `generic-recipient-host.md:134` (docs): "unknown fields … fail startup" is unscoped and, read generally, is an unflagged operator-surface change.**
`loadMcplServers` (`src/mcpl-config.ts:80-110`) copies only known keys and silently ignores
everything else, so a global strict-unknown rule would break existing `mcpl-servers.json` files.
The intent is clearly the new policy objects. Scope the sentence to `recipientPolicy` /
`contextOnlyStorage`, and say explicitly that unknown keys elsewhere keep today's tolerant handling.

**LOW `generic-recipient-host.md:232-236` (docs, brief item 6): the no-translator risk statement understates how little is at risk.**
Upstream main has no recipient protocol at all (verified above), so "no host translator" *ports
nothing* rather than removing a live capability; the only affected consumer is the pr361 fork
deployment, which keeps its frozen old release. §4 says "legacy recipient peers are unsupported on
the new release set … keep the frozen old release available separately", which is true but leaves
an owner unable to tell whether a regression is being accepted. State the upstream-has-none fact
directly, and pair it with the one real one-way hazard the doc already names correctly (never run
the old release against a D-stamped store). If F-Slack is late the consequence is then exactly
"the new release set is not activated for that deployment" — worth saying in one sentence.

**LOW `generic-recipient-host.md:306` (test): the CM no-reader-floor premise was audited at a different patch than the probe pins.**
D's own maintainer note audits context-manager **0.24.1** (`docs/context-only-delivery.md`,
"Maintainer questions") for the absence of an application-reader gate; §5.1 pins the new process to
CM **0.24.0** (correct — that is what the host lock resolves). The version choice is right; the
premise transfer is unstated. Add one line requiring the no-floor check to be re-confirmed at 0.24.0.

**LOW `generic-recipient-fixtures.md` "Negative data" row / `H02` (test gap): no fixture for a well-formed identity inside an otherwise non-data initialize result.**
`attest()` runs `snapshot(result)` over the **whole** initialize result and throws on any getter,
non-plain prototype, non-finite number, symbol or non-enumerable key *anywhere* in it
(`inbound-admission.ts:52-68,147-156`), which becomes `initialize_attestation_refused` and aborts
host startup. That is a realistic way for a correct connector to brick startup. Add it as an
explicit H02 negative control.

**LOW `docs/README.md` (docs): the two new docs are not in the repo's doc index.**
`docs/README.md:1-5` is explicitly "a map of the guides, by who they are for" and tabulates every
other doc; `docs/design/` is new and unlisted. Add a Design section with both files.

## Verification Gates

- **Reused receipt (not re-run):** `docs-contract` — exit 0 at `9f153044`, log
  `/home/orgasmic/.orgasmic/runs/run-01M676Y8DFF5Q14C9VNXBEX5AP/evidence/gate-docs-contract-01M677M156AFHCX44AX5KRZWZB.log`.
  I read the gate script: it is a static path/base/author/trailer/link/fence/pin/ID-presence/SHA/
  wording check. It cannot and does not establish API correctness, so every API claim above was
  re-derived from source rather than inherited from it.
- **Scoped read-only probes run (no gate wrapper, no reusable receipt — all read-only):**
  `git diff --stat 2c396622 HEAD`, `git status --short` (clean, left clean);
  `git show`/`git grep` at `1d990e41`, `bf6e8e0e`, `a5b02a07` in `/home/orgasmic/src/agent-framework`
  for `inbound-admission.ts`, `reply-routing.ts`, `types.ts`, `protocol-family.ts`, `framework.ts`
  and the three slice docs; `sed`/`grep` over host `src/index.ts`, `src/mcpl-config.ts`,
  `package.json`, `package-lock.json`, `bun.lock`, `CONTRIBUTING.md`, `docs/README.md`;
  version reads of `/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/{package.json,package-lock.json,bun.lock,node_modules/@animalabs/*}`;
  hash/SHA cross-checks against `/srv/review-drop/tarn-rtaln-20261030/{TASK-EM8Q2-O4.md,TASK-KPY3G-SLACK-STATIC.md,GENERIC-COMPLETION-PLAN-c1.md}`;
  `python3` arithmetic on the hours table.
- **Not run, by design:** no build, no test suite, no AF install, no connector execution, no store
  open. The deliverable is documentation; the design's own gates (old-reader probe, outbox crash
  matrix, compaction+restart) are explicitly unexecuted and are not claimed anywhere as passed.
  CONTRIBUTING's changelog check only triggers on `src/` changes, so the doc's "no fragment needed"
  is correct (`CONTRIBUTING.md:106-110`).

## Unmet Criteria / test gaps

- **Acceptance: met.** Config surface for required admission, reply-authority hook, context-only
  profile and identity (§2–§3); legacy v1 translator boundary (§4); fence proposal with an explicit
  supported boundary *preceded* by the synthetic probe plan (§5.1 before §5.2); shared stub contract
  from the O4 list (fixtures H01–H12 / S01–S06). The base is `2c396622`, not the assignment
  summary's `5416caab`; the doc explains this at `:9-12` and the manager's pinned input commands it,
  so this is not a deviation.
- **Opus review** is a manager-owned gate; the worker claims no receipt. This review is not that gate.
- Fixture-contract gaps, in priority order: modern-family refusal control (MEDIUM 2), notification-
  form settlement control (MEDIUM 3), whole-initialize-result attestation control (LOW 4). Nothing
  from the O4 test lists is missing.
- `authorize`'s optional-vs-required typing is still unresolved upstream; the design's choice to
  always install it makes the host correct under either, but the A+B+D integration commit does not
  exist yet, so no contract test has ever run against this API surface.

## Residual Risk

1. The whole design is validated against three **unmerged, under-review** AF pins. If B's rebase
   onto `1d990e41` changes `resolve`/`authorize` shapes, §2's mapping table needs re-checking;
   the design correctly refuses to build against moving heads, which contains but does not remove
   this risk.
2. The AF 0.31 fork tarball has no identity yet (AF's own `package.json` at `a5b02a07` is 0.30.0),
   so "replace `^0.30.0` with the exact versioned artifact" is an unexecuted instruction. Whoever
   builds it must also bump the fork package version; the manifest requirement covers this only if
   "fork package version" is enforced.
3. D enablement is one-way per store. Everything protecting that boundary is either unexecuted
   (5.1) or conditional (5.2). Treat F-host.5 as gated on 5.1 results, as the table already does.
4. The outbox/ACK contract genuinely cannot be made exactly-once, and the doc says so. Operator
   disposition of quarantined uncertain content is named as a need but not designed anywhere.
5. The `/srv` inputs are mutable, host-local and not content-addressed in-repo beyond the recorded
   hashes; if that drop is pruned the shared contract loses its policy-shape parameter set.

## Fix Directions (ordered)

1. Correct the hour totals at `host:426` to 42–64 / 52–80.
2. Add the legacy-protocol-family precondition to §2's validation list with a named operator error,
   plus a fixture negative control.
3. Require request-form delivery for outbox-tracked items in the fixtures state table, plus a
   notification never-settles control.
4. Record the oracle fixture's npm-vs-bun lock divergence and make per-route version assertion
   normative in §5.1.
5. Bind the no-fence decision to the release-set manifest with an explicit requalification trigger.
6. Vendor the policy shapes in-repo and demote the `/srv/*` paths to SHA-identified private supply
   before any PR.
7. Split F-host.2 into harness / store-probe and start the probe first.
8. Apply the five LOW items (scope the strict-unknown sentence; state that upstream carries no
   recipient protocol; note the CM 0.24.1→0.24.0 audit transfer; add the attestation snapshot
   control; index both docs in `docs/README.md`).
