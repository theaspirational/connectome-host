# Changed

Fix round 2 completed at **`eba367392801c25e52b7793214fca80d2f817345`**, direct parent `bcf28f0e0aa9f56d06f69a2f4e3fb994550da292`, branch `task-ky73n-impl`. Frozen base `a316200c4781a844d6109a6f8c812c3be99e20b0` remains an ancestor. Local commit only; no push/merge, production code, dependency, lockfile, CI edit, native connector, live data or threaded typing changes.

Author: Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>.
Trailer: `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.

Read `/srv/review-drop/TASK-KY73N-REREVIEW1.md` in full and handled the incoming binding refinements. Engineering asks belong to task operator **Tarn**, not human owner **Vasyl**. No ask/new consent was necessary: permissions are unchanged, and the tool-path correction describes actual pinned AF behavior rather than adding an authorization requirement.

## Files and fixes

1. `docs/design/generic-recipient-scope-contract.md`
   - Separates native MCP tool policy from generic channel publish with exact A/B citations (table below).
   - Describes `explicitToolAllowed` as configured tool-allow policy, not an AF capability grant; keeps native destination/effect checks distinct from exposure and owner consent.
   - Explains the new discrimination controls, registrationAccepted's structural redundancy, and that rejection counts do not imply independent predicate coverage.
   - Documents absent/empty/blank naming lists as PARTIAL, and intentional coupling of the neutrality scan to the unchanged `ci.yml`.
   - Updates the fixture digest.
2. `test/fixtures/generic-recipient/scope-cases.json` — **57 cases**, preserving all prior semantic cases and expectations:
   - **MEDIUM-1:** restores `dynamic-addressed-join-read-only` alongside unchanged A04 wake-parity positives. Its case-level wake selector has `joinedReadable:false`, `channels:["mailbox/active"]`; addressed post-startup join stores but does not wake. Marked dynamic, with no oldWake assertion, and explicitly **not an approved narrowing/Mapping X migration**.
   - **MEDIUM-2:** adds `dynamic-parent-claim-native-disagreement`: actual `parentId:"mailbox/denied"`, `parentJoined:true`, otherwise readable/permitted/current; claimed parent is `mailbox/active`; all outcomes false solely because ids disagree.
   - LOW: adds isolated `read.subConversations:false` denial; fixes the ambient A04 note to attribute no wake to addressed:false, not scope exclusion.
   - `dynamic-claimed-parent-overrides-actual` note now correctly describes its independent unjoined-parent read denial, rather than claiming agreement discrimination.
   - Renames `explicitGrant` → `explicitToolAllowed`, and `t01-no-explicit-tool-grant` → `t01-explicit-tool-policy-denied`, to remove the false grant claim. Same boolean guard and expected refusal; not a case deletion or permission change. The native no-publish-grant case's note is corrected for the same reason. Every retained case's expected outcome remains unchanged.
3. `test/fixtures/generic-recipient/check-scope-contract.mjs`
   - Adds the three new required cases and field/control rename.
   - Keeps **45 schema/input/outcome rejection controls** with expected assertion messages; explicitly does not equate that count with independent predicate coverage.
   - Adds four **oracle** mutation witnesses over unchanged fixture data (not just fixture mutations):

     | Oracle mutant | Required named failure |
     | --- | --- |
     | Bypass wake selector | `dynamic-addressed-join-read-only` |
     | Bypass parent agreement | `dynamic-parent-claim-native-disagreement` |
     | Trust claimed parent as native identity | `dynamic-parent-claim-native-disagreement` |
     | Bypass read sub-conversation selector | `dynamic-read-sub-conversations-denied` |

     The agreement-bypass variant canonicalizes only the key to the native fact, equivalent to deleting its sole agreement conjunct. The trust-the-claim variant is `reference(i,p,(s,facts) => selected(s,{...facts,parentId:facts.claimedParent}))`: it substitutes claimed identity for native truth, leaving the fixture untouched. Both mutants fail at the new agreement-only case, as recorded in the self-test log.
   - Trims/filters empty name-list entries; missing/empty/blank lists select PARTIAL. Invalid remaining entries still refuse. Parser controls and an actual empty-environment CLI probe pass.

New fixture SHA-256:
`f7c0ee04e3458e0a0fc8ffcc99573cb7807457f0253088ae65712e1ad4100bd5`.

Unchanged `policy-shapes.json` SHA-256:
`b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed`.

## Pinned authorization evidence and API gaps

A = `869bb3add7b48893d48b2ce50ea423e4b33fdfbc`.
B = `29edcf8b7f7b31d0ab35f59ab0e6aab75c0f4e04`.
All paths below are AF-repository relative at the indicated pin. These two send paths were re-read directly with `git show <full-sha>:<path> | nl -ba`; no fetch or shared-clone writes.

| Path | Actual pinned checks / disposition |
| --- | --- |
| Native adapter MCP send tool via `tools/call` | A `src/framework.ts:35447–35507` (especially isToolAllowed at `:35473`) and existing toolset presence/masks control exposure; dispatch `:35985–36037` → `src/mcpl/server-connection.ts:1962–1975` has no call-time CapabilityGrant check. Require configured tool-allow policy plus adapter fresh native destination/effect access, **not channels.incoming/channels.publish**. |
| AF `tools` capability | A `src/mcpl/feature-set-manager.ts:249–252`, `src/mcpl/capability-grant.ts:322–330`, `src/mcpl/types.ts:572`: §6.4 feature-set enablement, not ordinary tools/call authorization. Neither this grant nor descriptor claims supply native permission or owner consent. |
| Generic channel_publish/channels/publish | A `src/mcpl/channel-registry.ts:5977,6096,6385`: effective channels.publish checks, including after waits; `:5969–5971`: inbound-only refusal. B-bound automatic sends additionally require current binding and authorize===true at send: B `src/mcpl/reply-routing.ts:15,136–140`; `src/mcpl/channel-registry.ts:5962–5970`. This conditional B check does not turn fresh explicit tools into automatic-authority borrowers. |
| Existing A binder/registration API | A `src/mcpl/inbound-admission.ts:9–28,109–145`, `src/mcpl/channel-registry.ts:1653–1687,1887–1951,2411–2413`: existing exact key/registration/race checks still expose no host-facing verified dynamic scope view. Accepted registration alone is not native parenthood. |

**AF-scope remains needed, narrowly:** read-only lookup/current view plus configured authenticated projector/immutable verified-scope sidecars. The existing full table in document §4 is unchanged. These refinements clarify the existing conditional **AF-scope (8–16 h)** item, not a new item/estimate. Static source/API-gap review is not implementation/runtime qualification.

# Verification Gates

All four gates were **newly run** at reported head, clean before/after, exit 0, with recorded receipts. No inherited gate pass was reused. Node `v22.23.3`.

Log names are relative to this round's `$ORGASMIC_EVIDENCE_DIR`:

| Gate / command | Result | Log |
| --- | --- | --- |
| `orgasmic dispatch gate scope-fixtures -- node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: 57 cases, 45 rejection controls, 4 oracle mutants killed at required named cases, name-list parser controls. | `gate-scope-fixtures-01M67H66AF63REK4FJAG2H5HCW.log` |
| `orgasmic dispatch gate docs-contract -- env SCOPE_FORBIDDEN_NAMES=orgasmic,soma,tarn,alnold,alder node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | PASS: all 4 public files (including checker and unchanged CI), 13 denial controls, all 5 injected names. | `gate-docs-contract-01M67H66G2RFE6NJE727W1N3D6.log` |
| `orgasmic dispatch gate scope-runner -- bash -euo pipefail -c '<payload below>'` | PASS: actual unchanged CI block with list absent, plus empty-list CLI probe. Both naming runs honestly report PARTIAL with exit 0, 8 path/id/credential controls, 0 injected names. | `gate-scope-runner-01M67H66P8BQF3C2RR3RE4EPHJ.log` |
| `orgasmic dispatch gate scope-boundary -- env SCOPE_FORBIDDEN_NAMES=orgasmic,soma,tarn,alnold,alder bash -c '<payload below>'` | PASS: reviewed parent, frozen ancestor, clean tree, allowed doc/fixture paths only, whitespace, fixture hashes, recursive naming grep, author/trailer. | `gate-scope-boundary-01M67H66WEKKFZ75YNRS9EXJ8E.log` |

Exact scope-runner payload (line breaks expanded):

```sh
block=$(sed -n "/^      - name: Scope documentation contract$/,/^$/p" .github/workflows/ci.yml | sed "1,2d;s/^          //")
test -n "$block"
printf "CI step commands:\n%s\n" "$block"
env -u SCOPE_FORBIDDEN_NAMES bash -euo pipefail -c "$block"
printf "\nEmpty naming-policy CLI probe:\n"
empty=$(env SCOPE_FORBIDDEN_NAMES= node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract)
printf "%s\n" "$empty"
printf "%s\n" "$empty" | grep -Fq "PARTIAL naming check"
printf "%s\n" "$empty" | grep -Fq "0 injected names"
```

Exact scope-boundary payload (line breaks expanded):

```sh
set -eu
test "$(git rev-parse HEAD^)" = bcf28f0e0aa9f56d06f69a2f4e3fb994550da292
git merge-base --is-ancestor a316200c4781a844d6109a6f8c812c3be99e20b0 HEAD
test -z "$(git status --porcelain)"
git diff --check HEAD^ HEAD
git diff-tree --no-commit-id --name-only -r HEAD | while IFS= read -r p; do
  case "$p" in
    docs/design/*|test/fixtures/generic-recipient/*) printf "allowed: %s\n" "$p";;
    *) exit 1;;
  esac
done
printf "%s  %s\n" b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed test/fixtures/generic-recipient/policy-shapes.json f7c0ee04e3458e0a0fc8ffcc99573cb7807457f0253088ae65712e1ad4100bd5 test/fixtures/generic-recipient/scope-cases.json | sha256sum -c -
test -n "$SCOPE_FORBIDDEN_NAMES"
pattern=$(printf "%s" "$SCOPE_FORBIDDEN_NAMES" | tr , "|")
if grep -rniE --exclude=.git --exclude-dir=.git "$pattern" .; then
  exit 1
else
  result=$?; test "$result" = 1
fi
printf "PASS recursive worktree naming grep (git metadata excluded)\n"
git log -1 --format="%H%n%an <%ae>%n%b"
test -z "$(git status --porcelain)"
```

## Inherited evidence

- Earlier complete reads of the a316200c design/shared fixture specification, pinned A/B scope APIs and frozen old oracle remain valid static source context: these explicit source objects and the baseline policy fixture are unchanged. The named re-review is evidence to verify, not a reused runtime/gate receipt.
- This round directly re-read the two send paths and tools feature-set derivation at the full A/B pins listed above. Earlier blanket native-tools-grant wording is explicitly corrected, not inherited as proof.
- The CI block is unchanged from bcf28f0e and still requires no private list/secret. It was executed again against the new checker; absence and emptiness were tested separately. No prior PASS was implied by resuming.
- No verification gate failed. No production or hosted CI matrix/full suite was assigned/run; the new mutation witnesses are documentation-oracle checks only.

# Unmet Criteria

None for this bounded fix round. MEDIUM-1/2/3 and listed LOWs are addressed; all existing semantic cases remain, with the tool-policy control renamed/redescribed for the cited factual correction rather than deleted to make tests pass.

# Residual Risk

- These are snapshot/schema/document-oracle witnesses, not runtime native-fact authentication, AF object identity, actual storage/wake/publication effects, D behavior or recovery qualification. No old/new runtime parity result is claimed.
- AF-scope, native evidence production, projector/sidecar lifetimes and host mapping still require implementation/new integration artifact. Recursive/cyclic lineage remains prose-only with the existing T4 witness requirement.
- Public functional CI intentionally runs PARTIAL naming coverage without an external list and has no new secret dependency; full list-based publication hygiene is provided only by the injected dispatch gate and recursive grep above. Scanning ci.yml remains deliberate coupling, now documented.
- No permissions or owner-consent boundary changed. Existing owner transition/H1/H2/recovery/store/cutover/opt-in questions remain unresolved; no live activation or recovery-policy change is authorized.
