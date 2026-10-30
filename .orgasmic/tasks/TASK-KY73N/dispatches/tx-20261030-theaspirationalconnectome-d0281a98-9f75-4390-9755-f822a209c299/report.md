# Re-review (fix round 2): TASK-KY73N T1

Bounded delta review of `bcf28f0e..eba36739` (3 files, +206/-33: contract doc,
fixture JSON, checker). `ci.yml` is untouched this round. `a316200c` is still an
ancestor. Worktree clean before and after; no commits, edits or new files in the
tree (all probes in `/tmp`, since removed).

I checked only the six items in the brief. Everything load-bearing was
re-derived against the tree, not taken from `/srv/review-drop/TASK-KY73N-FIX2.md`.

## Verdict

**APPROVE.** All three MEDIUMs from `/srv/review-drop/TASK-KY73N-REREVIEW1.md`
are closed and the closure is independently reproducible: I re-derived the four
oracle mutants on an out-of-tree copy and each one fails **exactly one** case,
the named one (not just "fails first at"). The renames are provably not hidden
deletions — a programmatic case-by-case comparison of the two fixture versions
shows one id renamed with byte-identical requirement/timeline/input/expected,
three cases added, zero removed, and zero expectation drift on the 53 retained
cases. The two send paths' text matches the A/B code at the pins on every
citation I spot-checked (8 of them), with no invented call-time capability check.
Three LOWs remain, all docs/test-hygiene; none unmets an acceptance bullet.

## Findings

**LOW docs — `docs/design/generic-recipient-scope-contract.md:198-203`: path 1
cites only the *exposure* site for tool-allow policy and omits the real
call-time enforcement at A `src/framework.ts:35811`.** The sentence is not
wrong — `:35473` is `isToolAllowed` in the `sendToolsList` offering loop
(verified: `git show 869bb3ad:src/framework.ts | sed -n '35447,35507p'`), and the
dispatch block `:35985–36037` really does reach `sendToolsCall`
(`src/mcpl/server-connection.ts:1965-1975` → `sendRequest('tools/call', …)`)
with no `CapabilityGrant` consult. But `dispatchMcplToolCall` begins at
`framework.ts:35792` and refuses the call outright at `:35811`
(`if (!isToolAllowed(toolName, config))` → `mcplToolDeniedText`, trace
`denied by tool policy`) — a genuine **call-time** tool-policy check inside the
same function the doc cites. The fixture models `explicitToolAllowed` as a
send-time conjunct (`check-scope-contract.mjs:189`, evaluated alongside the
post-wait `current` rechecks), and `:35811` is its actual grounding; as written
the doc's only citation (`:35473`) supports exposure-time denial only, which is
the weaker claim. Direction of error is conservative, and the "**without a
call-time CapabilityGrant check**" phrasing is strictly true — I confirmed
`git grep -nE "has\((['\"])tools\1\)" 869bb3ad -- src` returns **nothing** (exit
1) and the only `CapabilityGrant` uses in `framework.ts` are `pushEvents`
(`:33038`, `:33237`) plus construction sites. *Fix:* add `:35811` to the path-1
citation list and say that the tool-allow policy is enforced both at listing
and at dispatch.

**LOW test — `check-scope-contract.mjs:305`: the mutant loop asserts the
*first* failing case, not that the named case is the *only* failure.**
`expectRejected(() => checkOutcomes(fixture, oracle), \`documentation oracle
mismatch: ${id}\`)` passes as long as the earliest mismatching case in array
order carries the named id. A future case added *after* the discriminating one
that a mutant also kills would be silently absorbed, weakening the
discrimination claim the doc makes at `:404-406` without turning the gate red.
Today the property actually holds — my out-of-tree sweep collected the complete
mismatch set per mutant rather than stopping at the first (table below), and
each set is a singleton. *Fix:* have the mutant loop collect all mismatching
ids and assert `deepEqual([id])`.

**LOW docs — `test/fixtures/generic-recipient/scope-cases.json:1667-1692`: the
new agreement-only control names its *permitted* native parent
`"mailbox/denied"`.** `dynamic-parent-claim-native-disagreement` sets
`parentId:"mailbox/denied"`, `parentJoined:true`; with `read.joinedReadable:true`
that parent is fully inside read scope, which is the whole point of the case.
The sibling `dynamic-claimed-parent-overrides-actual` (`:908`) reuses the same id
with `parentJoined:false`, where it *is* denied. The note does explain this
("permitted by Mapping X despite its invented id"), so it is legibility only,
but two adjacent controls using one id to mean opposite things invites a
mis-model of exactly the conjunct this case exists to witness. *Fix:* rename to
something neutral such as `mailbox/other-joined`.

## What I verified, item by item

**(1) MEDIUM-1 — restored, alongside the unchanged A04 positives, discriminating,
not presented as approved: CLOSED.** `dynamic-addressed-join-read-only` is back
at `scope-cases.json:1628`, `requirement:"dynamic"` (so `:146-147` demand no
`oldStore`/`oldWake` and it carries none — it is outside the parity assertion at
`:216`). Unlike the version deleted at `bcf28f0e`, it now overrides the selector
at *case* level (`wake.joinedReadable:false`, `wake.channels:["mailbox/active"]`)
rather than relying on a global wake policy, so Mapping X stays global and the
case still stores (`store:true`) with `addressed:true` and does not wake. The
three A04 parity positives are byte-identical to `bcf28f0e`
(`a04-addressed-join-mapping-x` store/wake/newBinding/reply all true, `oldWake
true`; `a04-authorized-human-dm` same; `a04-joined-after-startup` `oldStore
true/oldWake false/wake false`). It is explicitly *not* presented as approved:
case note "Synthetic selector-separation control only, **NOT** an approved wake
narrowing or Mapping X migration", and doc `:401-403` repeats it with "the default
A04 dynamic wake-parity positives remain unchanged".
Discrimination re-derived by me, not trusted: bypassing the wake selector in the
oracle fails **exactly** `["dynamic-addressed-join-read-only"]` and nothing else.

**(2) MEDIUM-2 — agreement is the sole cause of the denial: CLOSED.** I re-derived
both mutants on an out-of-tree copy with the fixture untouched:

| oracle mutant (my own, re-derived) | complete set of failing cases |
| --- | --- |
| wake selector always true | `["dynamic-addressed-join-read-only"]` |
| agreement conjunct bypassed (`claimedParent := parentId`) | `["dynamic-parent-claim-native-disagreement"]` |
| trust the claim (`parentId := claimedParent`) | `["dynamic-parent-claim-native-disagreement"]` |
| read `subConversations` forced true | `["dynamic-read-sub-conversations-denied"]` |
| reply selector always true (control, not shipped) | `["t02-reply-only-denied"]` |
| read selector always true (control, not shipped) | 18 cases |

`claimedParent` is read at exactly one place in the oracle — the agreement
conjunct `i.claimedParent === i.parentId` (`check-scope-contract.mjs:172`) — so
canonicalizing that key really is equivalent to deleting the conjunct; `parentId`
is read at `:172` and `:175`, so the trust-the-claim variant genuinely
substitutes claimed identity for native truth. Sole-cause proof for the new case:
evaluated as shipped it yields all-false (matching `expected`); with **only**
`claimedParent` set equal to `parentId` and nothing else changed it yields
`store/wake/newBinding/reply` **all true** with a destination — i.e. every other
conjunct passes and the id disagreement is the single reason for refusal.

**(3) MEDIUM-3 — the two send paths match the A/B code; no invented check.**
Spot-checked 8 citations with `git show <full-sha>:<path> | nl -ba` (read-only):
A `framework.ts:35473` `isToolAllowed` inside the `35447-35507` offering loop ✓;
A `framework.ts:36037` `legacy.sendToolsCall(...)` closing the `35985-36037`
dispatch block ✓; A `server-connection.ts:1962-1975` `sendToolsCall` →
`sendRequest('tools/call', params)` ✓; A `channel-registry.ts:5977`
`!CapabilityGrant.of(server).has('channels.publish')` under the §14.1 comment
`:5973-5975`, repeated at `:6096` ("a grant reduced during any of the waits") and
`:6385` ✓; A `channel-registry.ts:5969-5971` `isInboundOnly(entry)` refusal ✓;
A `feature-set-manager.ts:249-252` `missing = uses.filter(u => !grant.has(u))` →
`disable(name,'missing_capabilities', …'(§6.4)')` ✓; A
`capability-grant.ts:322-330` doc comment "`tools` is sourced from the OUTER
standard MCP `capabilities.tools` … The experimental manifest cannot mint it" +
`advertised.add('tools')` ✓ (and `:145`/`:186` skip `tools` as the comment says);
A `types.ts:572` `| 'tools'` in the capability-path union ✓;
B `reply-routing.ts:15` `authorize` with "Only literal true authorizes" ✓ and
`:136-140` `valid(input.connection, destination) && authorize(...) === true`
re-run at send ✓; B `channel-registry.ts:5962-5970` `routingRefusal()` with
`automatic_reply_binding_missing/destination_missing/target_mismatch/stale/
ungranted`, including `requiredAdmission?.isCurrent(binding.connection) &&
replyBindingCurrent(binding)` and `has('channels.publish')` ✓ — so "current
binding and `authorize(...) === true` are also mandatory at send" is accurate
for B.
No invented call-time capability check: `has('tools')` is absent from all of A's
`src`, and the doc says so rather than asserting one.
`explicitToolAllowed` is honestly described — doc `:356-357` "models the host's
configured tool-allow policy, **not an AF capability grant**", and the case note
at `:1419` repeats "this is not a tools capability-grant check". The division of
labour in §5 matches the oracle exactly: `publishGrant` only in the automatic
branch, `explicitToolAllowed`/`explicitPermitted` only in the explicit branch
(`check-scope-contract.mjs:188-190`), and doc `:360` says exactly that.
Permissions and expected outcomes are unchanged: `t01-no-channel-publish-grant`
still expects `reply:true` with `publishGrant:false, incomingGrant:false` (the
positive witness that the native path needs neither lane grant), and the renamed
tool-policy case still expects all-false.

**(4) The renames are not hidden deletions: VERIFIED programmatically.** Old 54
cases → new 57. `REMOVED: ["t01-no-explicit-tool-grant"]`,
`ADDED: ["t01-explicit-tool-policy-denied", "dynamic-addressed-join-read-only",
"dynamic-parent-claim-native-disagreement", "dynamic-read-sub-conversations-denied"]`.
The removed/added pair is identical modulo id, note and the
`explicitGrant`→`explicitToolAllowed` field rename: same `requirement:"T01"`,
same 7-phase timeline, same `input` (`mode:"explicit"`, flag `false`), same
all-false `expected`. Across the 53 retained cases, diffing `expected`,
`oldStore`, `oldWake`, `requirement`, `timeline`, `policy` and `input` (rename
applied) yields **zero drift**. `base` is identical modulo the rename; `policy`
is byte-identical. The checker's `requiredCases` list moved 47 → 50 with the same
one-for-one substitution plus the three additions, so no required-coverage entry
was quietly dropped. No stale `explicitGrant` / `no-explicit-tool-grant` string
remains anywhere under `docs/` or `test/`.

**(5) LOWs: CLOSED.** `read.subConversations:false` now has its own isolated
denial (`:1694`) and is discriminating (singleton mutant kill above). The parser
(`check-scope-contract.mjs:18-23`) trims and filters: I ran the CLI directly for
unset, `""` and `" , , "` — all three print `PARTIAL naming check: no nonempty
SCOPE_FORBIDDEN_NAMES list; path/id/credential checks only` and
`PASS docs-contract: 4 public files; 8 rejected controls; 0 injected names`,
**exit 0**; `exa-mple` and `ok,sk-123` both refuse with
`AssertionError: invalid forbidden-name list`, exit 1. Notes: the
`a04-joined-after-startup` note is now correct (with `wake.joinedReadable:true`,
`mailbox/newly-joined` *is* inside the wake set and `addressed:false` alone
denies — I confirmed the case's inputs), and
`dynamic-claimed-parent-overrides-actual`'s note no longer claims agreement
discrimination it does not have. `registrationAccepted`'s structural redundancy
is stated at doc `:377-380`, and the self-test banner now reads "45 rejection
controls … (not independent predicate coverage)".

**(6) Public CI carries no private names or secrets, and claims no full hygiene.**
`ci.yml` is unchanged in this diff and contains **no** `env:`, no `secrets.` and
no `SCOPE_FORBIDDEN_NAMES` reference at all (`grep -nE "secrets\.|
SCOPE_FORBIDDEN_NAMES|env:" .github/workflows/ci.yml` → empty); the step
(`:63-67`) is two bare `node` invocations. With the list absent the checker says
PARTIAL and `0 injected names`; the only three "full" strings in the tracked
assets are `checker:17` "The full naming policy is injected by the private gate,
never tracked here", doc `:415` the private-supply comment, and doc `:430`
"it never claims full naming coverage". No PASS line asserts full hygiene.
Tracked-tree neutrality re-checked by me with the list supplied outside the repo:
`git grep -niE '…' -- .` → no matches (exit 1). Fixture digest pinned in the doc
matches the file (`f7c0ee04…`, 1 occurrence).

## Verification Gates

| Gate / command | Outcome | Evidence |
| --- | --- | --- |
| `scope-fixtures` — `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | **PASS, re-run by me**: 57 cases, 45 rejection controls, 4 oracle mutants killed at their named cases, parser controls; exit 0 | `gate-scope-fixtures-01M67HFSSR0JV5QZ26ES925DBX.log` |
| `docs-contract` — `env SCOPE_FORBIDDEN_NAMES=… node … --docs-contract` | **PASS, re-run by me**: 4 public files; 13 rejected controls; 5 injected names; exit 0 | `gate-docs-contract-01M67HFW2Y060SZV7YT5MRV66E.log` |
| `scope-boundary`, `scope-runner` | PASS — receipts reused, not re-run (unchanged gate inputs at this commit) | `…/run-01M67GTE9D3GPPXT5MATK20Q6P/evidence/gate-scope-boundary-01M67H66WEKKFZ75YNRS9EXJ8E.log`, `…gate-scope-runner-01M67H66P8BQF3C2RR3RE4EPHJ.log` |
| Own mutant sweep — out-of-tree mirror (`docs/`, `test/fixtures/`, `.github/`), oracle mutated, fixture untouched, **complete** mismatch set collected per mutant | 6 mutants: 4 shipped ones each fail exactly one named case; reply-selector control fails only `t02-reply-only-denied`; read-selector control fails 18 | probe, `/tmp/ky73n-probe` (removed) |
| Sole-cause check on the agreement case | as shipped all-false; with only `claimedParent := parentId` all four outcomes flip true with a destination | probe |
| Fixture version diff (`bcf28f0e` vs `eba36739`) | 54→57 cases; 1 rename (identical modulo id/note/field name), 3 additions, 0 removals, 0 expectation/timeline/policy/input drift on retained cases; `base` identical modulo rename; `policy` byte-identical; `requiredCases` 47→50 one-for-one | probe |
| Parser CLI probes | unset / `""` / `" , , "` → PARTIAL, exit 0; `exa-mple`, `ok,sk-123` → refuse, exit 1 | probe |
| A/B citation spot-checks (8 sites) | all accurate; `has('tools')` absent from A `src` (exit 1); the uncited call-time check is `framework.ts:35811` inside `dispatchMcplToolCall` (`:35792`) | `git show`/`git grep` at `869bb3ad`/`29edcf8b`, read-only, no fetch, no writes |
| Worktree state | clean before and after; no commits, edits or new files | `git status --porcelain` empty |

## Unmet criteria / test gaps

No acceptance criterion is unmet. The two test-witness gaps this round was asked
to close (wake-selector discrimination, claimed-parent agreement) are closed with
singleton-kill mutants, and the `read.subConversations` gap is closed too.

Remaining gaps, all pre-existing and all for later slices: the mutant loop's
first-failure-only assertion (LOW-2 above); recursive/cyclic lineage still
prose-only with the stated T4 witness requirement (doc `:407-409`); and
`explicitToolAllowed` enforcement itself, which is host/adapter work — AF supplies
tool-allow policy (`framework.ts:35811`) but no verified dynamic scope view.

## Residual risk

- Unchanged from the previous round: everything here is snapshot assertions over
  already-settled booleans. Nothing qualifies how an adapter establishes
  `parentVerifiedNatively`, `scopeVerified` or `nativeMappingValid`.
- No old/new runtime storage or wake parity execution; `oldRead`/`oldWake` remain
  source-derived, and the `oldWake` parity assertion still binds A04 cases only.
- In GitHub Actions the naming mode runs **PARTIAL by design** — the CI step is
  not the naming enforcement point; only the dispatch `docs-contract` gate with
  the injected list is. Documented at doc `:428-434`.
- The `lookup`/`current` view, projector and sidecar lifetimes remain a proposal
  needing a new composed integration SHA before any runtime claim; the report's
  framing (clarification of the existing conditional AF-scope 8–16 h item, not a
  new item) is unchanged and correct.
- No permission or owner-consent boundary moved this round; host-design §4 owner
  questions remain open and nothing here authorizes live activation.

## Fix directions

1. Add `framework.ts:35811` to the path-1 citation list and state that tool-allow
   policy is enforced at listing *and* at dispatch (LOW-1). This strengthens the
   doc's own position; it is not a permission change.
2. Make the mutant loop collect all mismatching ids and assert `deepEqual([id])`
   so the sole-failure property is enforced by the gate rather than by review
   (LOW-2).
3. Rename the agreement-only case's native parent id to something neutral
   (LOW-3).

None of the three blocks ship; all are suitable for T2 alongside the already
listed follow-ups.
