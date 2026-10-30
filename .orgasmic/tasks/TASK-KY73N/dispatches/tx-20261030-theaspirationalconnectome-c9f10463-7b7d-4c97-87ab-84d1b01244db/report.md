# Re-review: TASK-KY73N T1 fix round 1

Reviewed `bcf28f0e0aa9f56d06f69a2f4e3fb994550da292` against parent
`60b506c6754f811b53c1d011a3b7067033297bc4`; frozen base `a316200c` is an
ancestor. Diff is 4 files (+496/-96): the contract doc, the fixture JSON, the
checker, and one CI step. No production code, no dependency/lockfile change, no
threaded-typing work. Worktree clean before and after; no commits, no edits.

## Verdict

**APPROVE.** M1–M5 and the LOWs from `/srv/review-drop/TASK-KY73N-REVIEW1.md`
are substantively closed, and I verified each one against the tree rather than
the implementer report. Three MEDIUM findings remain: two **test-witness** gaps
in checks that *do* exist in the doc and the oracle (one a coverage regression
introduced by this round — a discriminating case was deleted), and one **doc
sentence that diverges from A's actual grant geometry** on the explicit-tool
path. None unmets an acceptance bullet; the divergence is fail-closed (the
sentence requires *more* than A does), so the fix is the sentence and the
fixture field's description, not the permissions. All three belong to T2.

### Soma's refinements, item by item (what I confirmed)

**M1 — dynamic wake preserved via Mapping X, no narrowing: CLOSED.**
`generic-recipient-scope-contract.md:50-61` now states wake-side Mapping X
(`joinedReadable:true` + authorized-human `dmUsers` + `subConversations:true`),
says an exact wake list cannot preserve the non-enumerable wake sources either,
and states "No wake narrowing is proposed" (:64). Positive witnesses exist and
are real: `a04-addressed-join-mapping-x` (addressed post-startup join →
`store/wake/newBinding/reply` all true) and `a04-authorized-human-dm` (first
contact → wake true). The checker now asserts old/new wake parity for every A04
case (`check-scope-contract.mjs:207-212`: `assert.equal(c.oldWake,
c.expected.wake)`), and mutating `d.cases[0].oldWake` is a rejection control
(`:281`).
I checked faithfulness against the frozen old oracle myself
(`/srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/src/host-recipient-protocol.ts`):
line 117 is `addressed = human && metadata.dmAuthorizedPeer === access.dmPeer`,
so a bot/unknown-peer DM **never** woke the old pair — the fixture's
`a04-first-contact-peer-dm` with `addressed:false` and a human-only
`wake.dmUsers` is therefore parity, not narrowing. Line 122 wakes on any
author's mention in a readable joined channel, which wake-side `joinedReadable:true`
preserves. The one genuinely new wake conjunct
(`triggeringAuthorHasNativeAccess`) is pre-approved by
`generic-recipient-fixtures.md:133` ("Triggering author without native access →
no new binding, no wake; this must not narrow ingress storage"), so it is not an
undeclared narrowing.

**M2 — lookup bound to connection/epoch/generation; acceptance ≠ parenthood: CLOSED,
with one witness gap (MEDIUM-2).** Doc:111-121 now names the evidence path
(detached descriptor from the operator-authenticated adapter on the attested
protected binding), states the claimed parent is "**only as a key into
`lookup()`**", states "Lookup success alone proves neither parenthood nor
authorization", and states the direction explicitly: "native facts →
verifiedScope, never verifiedScope → presumed native truth". Descriptor
`address`/`metadata`/`label` are still declared inert, and the checker still
reads `claimedLabel`/`interveningDestination` **nowhere** in the oracle.
I verified the oracle's conjuncts by mutation-killing the *oracle* (not the
fixture) on an out-of-tree copy — each line below is "delete the conjunct, re-run
`--self-test`, see which case catches it":

| oracle conjunct (`check-scope-contract.mjs`) | caught by |
| --- | --- |
| `i.parentAssertedInDescriptor` (:166) | `dynamic-parent-not-asserted` |
| `i.parentVerifiedNatively` (:166) | `dynamic-claimed-parent-widening` |
| `i.parentRegistrationCurrent` (:167) | `dynamic-parent-removed-before-ingress` |
| `i.parentConnection === i.connection` (:168) | `dynamic-cross-connection-parent` |
| `i.parentEpoch === i.epoch` (:169) | `dynamic-stale-parent-epoch` |
| `i.nativeMappingValid` (:168) | `dynamic-unknown-native-root` |
| `i.admissionRegistrationCurrent` (:174) | `dynamic-registration-replaced-during-admission` |
| `i.connection === i.registrationConnection` (:174) | `dynamic-cross-connection-registration` |
| `i.epoch === i.admissionEpoch` (:175) | `dynamic-stale-ingress-epoch` |
| `i.epoch === i.registrationEpoch` (:175) | `dynamic-stale-registration-epoch` |
| `i.registeredBeforeUse` (:174) | `dynamic-use-before-registration` |
| `i.scopeVerified` (:173) | `dynamic-arbitrary-registration` |
| `i.claimedParent === i.parentId` (:167) | **nothing — see MEDIUM-2** |
| `i.registrationAccepted` (:173) | nothing (structurally redundant — LOW-2) |

So the brief's specific question — *is the asserted-in-descriptor-only negative
real?* — answers **yes**: `dynamic-claimed-parent-widening` keeps the accepted,
allowed, descriptor-asserted parent and sets `parentVerifiedNatively:false`, the
oracle denies all five outcomes, and deleting the check from the oracle makes
the suite fail on exactly that case. The post-await negative
(`dynamic-registration-replaced-during-admission`) and both stale-epoch
negatives are present and discriminating. "Accepted registration is not proof"
is witnessed independently by `dynamic-arbitrary-registration`
(`registrationAccepted:true`, `scopeVerified:false` → deny everything).

**M3 — the CI step is real and correct: CLOSED.** `.github/workflows/ci.yml:63-67`
adds `Scope documentation contract` to the existing `build-and-test` job, after
`Setup Node.js` (`:36-40`) and `npm ci`, so `node` exists; paths are
repo-root-relative and `run:` runs at the repo root; GitHub's default
`bash -e {0}` aborts on the first nonzero, and the checker exits 1 on any
`AssertionError` (verified: `SCOPE_FORBIDDEN_NAMES= … → exit=1`). The step has no
`if:`, so it runs on all five matrix rows (redundant, harmless). The
`scope-runner` gate does execute that block: I re-ran it and the log prints the
two extracted commands verbatim before running them, and the `sed` range
(`- name:` → first blank line, drop 2, strip 10 spaces) yields exactly the two
node invocations — I checked the extraction with `cat -A`.

**M4 — exact-list config explicitly superseded: CLOSED.** Doc:61-85 adds
"Provisional configuration mapping (superseded exact-list shape)", states the
host-design §2 shape is "**superseded**, not a usable representation of Mapping
X", gives the per-field mapping table, states `policy-shapes.json`'s policy
block "**predates this contract**", and states the joined narrowing with its
parity citation. I confirmed §2 is the right reference: `generic-recipient-host.md:78`
is "## 2. Proposed recipe/settings surface and mapping" and `:93-96` declare
`readChannels`/`wakeChannels`/`replyChannels`. The joined-narrowing claim matches
the frozen oracle's line 120 (`!readJoined || access.joined !== true` → refuse).

**M5 — no internal-name literals in the tracked tree: CLOSED.** The denylist is
gone from source; `check-scope-contract.mjs:17-20` reads
`SCOPE_FORBIDDEN_NAMES`. I ran the grep myself with the list supplied outside the
repo, over tracked files only (`git ls-files -z | xargs -0 grep -niE
'\bsoma\b|orgasmic|tarn|alnold|alder'`) and `git grep -niE` at HEAD: **both
return no matches**. The checker source itself is in the scan set
(`:11` reads `import.meta.url`), as is `ci.yml` (`:12`) — `docs-contract` reports
`4 public files`, and the denial controls are built at runtime from
`String.fromCharCode`/`join` so scanning the source has no exemptions
(`:292-298`). Without the list it prints
`PARTIAL naming check: SCOPE_FORBIDDEN_NAMES not supplied; path/id/credential
checks only` and `0 injected names` — honest, as required.

**LOWs from review 1 — all closed.** Lane grant moved out of `Live` into `Store`
(doc:146-147, :169; oracle `:177`) with two discriminating cases
(`dynamic-no-configured-lane`, `dynamic-no-incoming-grant`). Explicit tool grant
stated (doc:196-201) with `t01-no-explicit-tool-grant` /
`t01-no-channel-publish-grant`, both discriminating. `joined`-list positive and
`joined:false` negative added. `automaticReplies:false` case added and
discriminating. Inverse id invariant added (`:95-96`, mutation at `:252`).
`assert.throws` now requires `{name:'AssertionError', message: /…/}` via
`expectRejected` (`:228-231`) — I confirmed every one of the 45 rejection checks
goes through it. Trace tap rejected in the alternatives list (doc:265-269) and
its citations are accurate: at A `869bb3ad`,
`channel-registry.ts:1689-1694` emits `mcpl:channels-register` **after**
`await this.reconcileChannels` (`:1687`), and `:1777` emits
`mcpl:channel-descriptor-rejected` — so "asynchronous/unordered against
admission" is supported by the source.

**Explicit-tool-grant claim vs A/B code (brief item + manager's clarification):**
not a permission change, but the sentence is wrong about A for one of the two
paths. I traced both paths separately at the pins rather than accepting one
blanket rule — see MEDIUM-3.

## Findings

**MEDIUM test — `test/fixtures/generic-recipient/scope-cases.json`: the `wake`
selector is inert; deleting `selected(p.wake, i)` from the oracle
(`check-scope-contract.mjs:178`) leaves all 54 cases green.** This is a coverage
*regression* from this round: the previous commit's `dynamic-addressed-join-read-only`
was the only case where the wake selector was the reason for a denial
(`git show 60b506c6:…/scope-cases.json | grep -c dynamic-addressed-join-read-only`
→ 1; at `bcf28f0e` → 0), and it was removed while wake policy flipped to
`joinedReadable:true`. Every remaining wake denial is driven by `addressed`,
`authorHasAccess`, `contextOnly` or `store`. Impact: the contract's load-bearing
separation claims — doc:65-66 "Wake and reply remain independently configured;
neither implicitly inherits read" and doc:52-53's "Wake/reply effective sets are
intersections with read" — now have no fixture witness on the wake side, so T2
could compile wake as an alias of read and pass this gate. (Reply-side
separation *is* witnessed by `t02-reply-only-denied`.)
*Fix:* restore a narrow-wake case alongside the new Mapping X positive — e.g.
addressed post-startup join with case-level `wake.joinedReadable:false,
wake.channels:["mailbox/active"]` → `store:true, wake:false`. Marking it
`dynamic`/`negative` (not A04) keeps it out of the `oldWake` parity assertion, so
it demonstrates selector separation without asserting a narrowing is approved.

**MEDIUM test — `check-scope-contract.mjs:167` /
`scope-cases.json` case `dynamic-claimed-parent-overrides-actual`: the
claimed-parent↔native-fact *agreement* conjunct has no discriminating case; that
negative is over-determined.** The case sets `parentId:"mailbox/denied"`,
`parentJoined:false`, `claimedParent:"mailbox/active"`. Its denial comes entirely
from `joined(i.parentId, i.parentJoined)` being false because `parentJoined` is
false — not from the id disagreement. I proved this with a "trust-the-claim"
oracle mutant: delete `i.claimedParent === i.parentId &&` *and* rewrite the
parent scope check to `joined(i.claimedParent, i.parentJoined)` → **`--self-test`
still exits 0**. Deleting the agreement conjunct alone is likewise not caught.
Impact: the exact control Soma's M2 names ("claimed descriptor fields remain
inert; claimed parent is only a lookup key") is asserted by the doc
(:114-118, fixture prose "Both must agree") and implemented in the oracle, but
the fixture cannot tell an id-substituting implementation apart from a
fact-respecting one — and the case note claims it can ("A claimed allowed parent
cannot override the verified actual parent"). This is a witness gap, not a
widening: the independent `parentVerifiedNatively` control *is* discriminating,
so the acceptance bullet is still met.
*Fix:* add one case where the native parent is fully permitted and only the claim
disagrees — `parentId:"mailbox/denied"`, `parentJoined:true` (so
`joinedReadable` would admit the actual parent), `claimedParent:"mailbox/active"`
→ `store:false`. That makes the agreement conjunct the sole cause of denial.

**MEDIUM docs/correctness — `generic-recipient-scope-contract.md:196-201`: the
"require its effective `tools` grant for `tools/call`" half of the sentence
diverges from A; the `channels.publish` half is exactly right.** Per the
manager's clarification I traced the two send paths separately at A `869bb3ad` /
B `29edcf8b` instead of checking one blanket rule.

*Path 1 — the adapter's own native MCP send tool, called via `tools/call`.*
Exposure: `src/framework.ts:35447-35507` lists `server.sendToolsList()` and
offers each tool gated **only** by `isToolAllowed(tool.name, config)`
(`:35473`) and toolset presence (`:35474`). Dispatch: `src/framework.ts:35985-36060`
→ `src/mcpl/server-connection.ts:1962-1975` `sendRequest('tools/call', params)`.
**Neither site consults `CapabilityGrant`**, and `git grep -nE "has\(['\"]tools"`
over all of `src` at the A pin returns nothing — there is no effective-`tools`-grant
check on this path anywhere in the tree. `tools` *is* a real capability path
(`src/mcpl/types.ts:572`), minted only from the OUTER standard-MCP
`capabilities.tools` (`src/mcpl/capability-grant.ts:322-330`, doc comment
`:132-136`, and deliberately not walked from the experimental manifest at `:145`),
but it is consumed by exactly one thing: §6.4 feature-set derivation at
`src/mcpl/feature-set-manager.ts:249-252`, which disables a declared feature set
whose `uses` are not in the grant. So an absent `tools` grant disables feature
sets that declare `uses:['tools']`; it does not refuse a `tools/call` to an
ordinary advertised tool. The real gates on path 1 are the host's configured
tool-allow policy and the adapter's own native destination/effect access.

*Path 2 — the host's generic `channel_publish` / `channels/publish`.* Tool
definition `src/mcpl/channel-registry.ts:569-580` (host-synthesized), executor
dispatch `:2505-2506` → `handleToolPublish` → `ChannelRegistry.publish`, whose
grant check is `if (!server || !CapabilityGrant.of(server).has('channels.publish'))
return ungranted();` at `:5977` under the §14.1 comment at `:5973-5975`, with the
same check repeated after waits at `:6096` and `:6385`, plus the inbound-only
destination refusal at `:5969-5971` and the up-and-granted precondition at
`:1420`. On the B side `src/mcpl/reply-routing.ts:15` keeps `authorize` required
and `:106-140` re-runs `valid(key,target) && authorize(...) === true` at send
(`:136-139`). The doc's carve-out — "a generic channel-publish tool would instead
need its own publish grant" — matches this exactly.

Impact: §4 of this doc is otherwise a precise file:line API-gap list, and this is
an uncited factual claim about A's grant geometry that is wrong as stated for
path 1. A T2 implementer could add an `explicitGrant` conjunct believing AF
supplies the check, and find nothing to read it from. It is **not** permission
widening and **not** owner consent: the fixture's `explicitGrant` conjunct
(`check-scope-contract.mjs:184`) requires strictly more than the previous round's
explicit path did, so the divergence is fail-closed, and descriptor claims supply
neither grant. *Fix the sentence and the fixture field, not the permissions:*
state that the native explicit tool requires the host's configured explicit-send
tool permission (`framework.ts:35473`) plus the adapter's fresh native effect
access, and that AF's `tools` grant gates only §6.4 feature-set enablement
(`feature-set-manager.ts:249-252`, `capability-grant.ts:322-330`,
`types.ts:572`) — not `tools/call` itself; keep "not `channels.incoming` /
`channels.publish`", which is correct and now verified; and redescribe
`explicitGrant` in doc §5 accordingly so the fixture field stops naming a grant
AF does not check.

**LOW test — `check-scope-contract.mjs:166`: the `subConversations` selector
boolean is inert.** No case sets `read.subConversations:false`; deleting
`s.subConversations &&` from the oracle keeps the suite green.
`t02-reply-only-denied` sets it false only inside a wholly-empty reply selector,
where the parent `channels`/`dmUsers` checks already deny. One case with
case-level `read.subConversations:false` on the base sub-conversation input →
`store:false` closes it.

**LOW test — `check-scope-contract.mjs:173`: the `i.registrationAccepted`
conjunct is structurally unreachable as a sole cause.** The schema forces
`registrationAccepted:false` ⇒ no `register-accepted` barrier (`:153`) ⇒
`registeredBeforeUse:false` (`:152`), so `dynamic-rejected-registration` is
over-determined and deleting `i.registrationAccepted` from the oracle is not
caught. Harmless (the conjunct is correct and fail-closed) but the "45 rejected
mutations" figure overstates discrimination here; worth one sentence in the
fixture prose rather than a new case.

**LOW robustness — `check-scope-contract.mjs:18-20`: an empty
`SCOPE_FORBIDDEN_NAMES` hard-fails the checker instead of reporting PARTIAL.**
`nameList === undefined ? [] : nameList.split(',')` turns `""` into `[""]`, and
`/^[a-z]+$/iu` rejects it → `AssertionError: invalid forbidden-name list`, exit 1
(verified). Doc:384-385 tells operators to `export SCOPE_FORBIDDEN_NAMES=…`, and
the natural CI wiring (`env: SCOPE_FORBIDDEN_NAMES: ${{ secrets.… }}` with the
secret unset) produces exactly the empty string, which would turn the new CI step
red for a configuration reason unrelated to the contract. Non-alphabetic entries
behave the same way. *Fix:* filter empty entries
(`nameList.split(',').map(s => s.trim()).filter(Boolean)`) and keep the assertion
for the rest.

**LOW docs — `scope-cases.json`, case `a04-joined-after-startup` note: stale
after the M1 change.** The note reads "Readable ambient post-startup join
**outside wake/reply sets**", but wake and reply now both carry
`joinedReadable:true`, so `mailbox/newly-joined` *is* inside both sets; the
denial is purely `addressed:false`. A reader checking M1 against the fixture gets
the wrong model of why that case does not wake. One-line note edit.

**LOW design — `check-scope-contract.mjs:12`: scanning `.github/workflows/ci.yml`
couples the neutrality gate to an unrelated file.** M5 asked for the checker
itself to be scanned; `ci.yml` was added too. Any future CI edit mentioning a
machine-local path (`/home/`, `/tmp/`, `C:\`), a `TASK-`/`RUN-`-shaped token or a
UUID now fails `--self-test`'s sibling mode in a way whose cause is not obvious
from the step name. It passes today (verified). Keep or drop deliberately, but
the coupling deserves a sentence in doc §5.

## Verification Gates

| Gate / command | Outcome | Evidence |
| --- | --- | --- |
| `scope-fixtures` — `node …/check-scope-contract.mjs --self-test` | **PASS**, re-run by me: `54 cases`, `45 rejected mutations with expected assertion messages`, exit 0 | `gate-scope-fixtures-01M67G9REBR1BBRM53GFWGNKF6.log` |
| `docs-contract` — `env SCOPE_FORBIDDEN_NAMES=… node … --docs-contract` | **PASS**, re-run by me: `4 public files; 13 rejected controls; 5 injected names`, exit 0 | `gate-docs-contract-01M67G9WHACR20S3754GC0A0MB.log` |
| `scope-runner` — extract the `Scope documentation contract` block from `ci.yml` and execute it with `SCOPE_FORBIDDEN_NAMES` unset | **PASS**, re-run by me; log prints the two extracted commands, then `PARTIAL naming check` + `8 rejected controls` | `gate-scope-runner-01M67GFM28CGJR0V7BFSKXJA3N.log` |
| `scope-boundary` (frozen parent, clean tree, allowed paths, both fixture digests, recursive name grep, identity) | PASS — receipt reused, not re-run | `…/run-01M67FRDPNZHR2ZWVWECNPX3ES/evidence/gate-scope-boundary-01M67G50WKQXPB0FPMEKWAWZ1A.log` |
| Tracked-tree name grep, list supplied outside the repo | **no matches** — `git grep -niE` at HEAD and `git ls-files -z \| xargs -0 grep -niE '\bsoma\b\|orgasmic\|tarn\|alnold\|alder'` both empty | probe, this review |
| Oracle kill-mutant sweep (27 mutants, oracle source mutated out-of-tree, fixture untouched) | 23 caught with a named case; 4 not caught → MEDIUM-2, LOW-1, LOW-2 (one was a probe-application artifact, re-run and caught) | probe, `/tmp` copy, now removed |
| CI step mechanics | node present after `setup-node` (`ci.yml:36-40`); repo-root paths; checker exits 1 on assertion (`SCOPE_FORBIDDEN_NAMES= … → exit=1`); `sed` extraction verified with `cat -A` | probe |
| Old-oracle parity for M1 | `host-recipient-protocol.ts:117` requires `human` for DM wake and `:122` wakes on any mention in a readable joined channel — fixture's bot-DM `addressed:false` and human-only `wake.dmUsers` are parity | read `/srv/ci-fixtures/pr361-v1-8153e7b84442cc11` |
| Author-access wake conjunct pre-approval | `generic-recipient-fixtures.md:133` mandates "no new binding, no wake" for an author without native access | read |
| A-pin citations added this round | `channel-registry.ts:1689-1694` (emitted after `await reconcileChannels` at `:1687`) and `:1777` both accurate | `git show`/`git grep` at `869bb3ad`, read-only, no fetch, no writes |
| Two send paths traced separately (manager's clarification) | path 1 `tools/call`: exposure `framework.ts:35473-35474`, dispatch `framework.ts:35976/35985-36060` → `server-connection.ts:1962-1975`, **no grant check**, `has('tools')` absent from all of `src`; `tools` consumed only at `feature-set-manager.ts:249-252`. path 2 publish: tool `channel-registry.ts:569-580`, executor `:2505-2506`, grant `:5977` (+`:6096,:6385`), inbound-only `:5969-5971`, up+granted `:1420`; B `reply-routing.ts:15,106-140` | `git show`/`git grep` at `869bb3ad`/`29edcf8b`, read-only |
| B-pin citation | `reply-routing.ts:15` `authorize` still required at `29edcf8b` | `git show` |
| `generic-recipient-host.md` §2 reference | §2 at `:78`; `readChannels`/`wakeChannels`/`replyChannels` at `:93-96` | read |
| Worktree state | clean before and after; no commits, no edits, no new files | `git status --porcelain` empty |

## Unmet criteria / test gaps

No acceptance criterion is unmet, and none of M1–M5 is open. All four acceptance
bullets remain satisfied, now with the wake side of the contract made expressible
(M1), the evidence path named and directed (M2), a real CI gate (M3), the
superseded config surface declared with a mapping (M4), and the private naming
policy out of the tracked tree with the checker in its own scan (M5).

Test gaps for T2, in priority order: wake-selector discrimination (MEDIUM-1,
a regression this round); claimed-parent agreement witness (MEDIUM-2);
the explicit-tool grant sentence and fixture field (MEDIUM-3);
`read.subConversations:false` case (LOW-1); empty-env-var handling (LOW-3);
recursive/cyclic lineage, still correctly prose-only with an explicit T4 witness
requirement (doc:365-368); and `explicitGrant` enforcement, which A does not
provide and the host must implement.

## Residual risk

- Everything here remains snapshot assertions over already-settled booleans.
  Nothing qualifies that an adapter establishes `parentVerifiedNatively`,
  `scopeVerified` or `nativeMappingValid`; M2's doc text now names the evidence
  path, but the path itself is unimplemented AF-scope/adapter work.
- No old/new runtime storage or wake parity execution. `oldRead`/`oldWake`
  (`check-scope-contract.mjs:192-212`) are source-derived from the frozen oracle;
  the `oldWake` parity assertion binds only A04 cases, all of which have
  `authorHasAccess:true`, so the one approved wake narrowing is never exercised
  against old behavior.
- In GitHub Actions the naming check runs **PARTIAL only** — CI carries no
  denylist by design, so a non-neutral name committed to any of the four public
  files would be caught only by the manual `docs-contract` dispatch gate. This is
  documented (doc:386-390) and is the intended trade for keeping the list
  untracked, but it means the CI step is not the naming enforcement point.
- The proposed `lookup`/`current` view, the projector and sidecar lifetimes are
  still a proposal needing a new composed integration SHA before any runtime
  claim. The report's framing — refinements clarify the existing conditional
  AF-scope (8–16 h) item, not a new slice — matches doc:313-316 and
  `generic-recipient-host.md:708`.
- All owner questions in host-design §4 remain open; nothing here authorizes live
  activation.

## Fix directions

1. Restore a wake-selector-discriminating negative alongside the new Mapping X
   positive (MEDIUM-1) — this is the only finding I would ask T2 to do first,
   because the deletion happened in this round.
2. Add the claimed-parent-disagreement case with a *permitted* actual parent
   (MEDIUM-2), so the agreement conjunct is the sole cause of denial.
3. Add a `read.subConversations:false` case; note the structural redundancy of
   `registrationAccepted` in the fixture prose (LOW-1, LOW-2).
4. Filter empty/blank entries out of `SCOPE_FORBIDDEN_NAMES` before asserting
   (LOW-4).
5. Correct the `a04-joined-after-startup` note (LOW-5).
6. Rewrite doc:196-201 to separate the two send paths and cite them
   (MEDIUM-3): native explicit tool → host tool-allow policy
   (`framework.ts:35473`) + adapter native effect access, with AF's `tools`
   grant scoped to §6.4 feature-set enablement
   (`feature-set-manager.ts:249-252`); generic channel publish →
   `channels.publish` (`channel-registry.ts:5977,6096,6385`). Redescribe the
   fixture's `explicitGrant` to match. Add a sentence on why `ci.yml` is in the
   neutrality scan (LOW-5).
