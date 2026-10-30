# Changed

Fix round 1 is complete at **`bcf28f0e0aa9f56d06f69a2f4e3fb994550da292`**, direct parent `60b506c6754f811b53c1d011a3b7067033297bc4`, on `task-ky73n-impl`. The frozen design base `a316200c4781a844d6109a6f8c812c3be99e20b0` remains an ancestor. Local commit only; no push, merge, production code, dependency/lockfile, native connector, live-data or threaded-typing changes.

Author: Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>.
Trailer: `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.

Read `/srv/review-drop/TASK-KY73N-REVIEW1.md` in full and handled the incoming binding refinements. Engineering asks belong to task operator **Tarn**, not human owner **Vasyl**; no new owner decision or blocking engineering ask was needed. Dynamic wake preservation is an existing requirement, not a proposed narrowing.

## Files and findings addressed

- `docs/design/generic-recipient-scope-contract.md`:
  - **M1:** Explicit wake-side Mapping X (`joinedReadable:true`, authorized-human DM user set, sub-conversations) and dynamic reply candidates when enabled, still bounded by addressedness/native effect ACLs. No wake narrowing proposed.
  - **M2:** Names the descriptor evidence path from the configured authenticated/attested adapter. Claimed parent is only a lookup key into accepted registrations on exact connection/epoch/generation; lookup success is not proof. Native facts independently verified by that adapter produce verifiedScope, never the inverse.
  - **M4:** Explicitly supersedes the incapable exact-list-only configuration, maps provisional read/wake/reply fields to selector members, identifies the historical policy block, and explains joined membership even for listed channels as parity with old oracle line 120.
  - LOWs: ingress lane grant moved from Live into Store; native explicit tool requires its own effective tools grant, not incoming/publish grants; trace-tap alternative rejected with pinned evidence; one-level oracle limitations and required T4 multi-level/cycle witness are explicit; task-operator versus human-owner roles clarified.
- `test/fixtures/generic-recipient/scope-cases.json`: **54 cases**. Adds/updates addressed post-startup-join and authorized-human first-contact-DM wake parity; distinguishes `parentAssertedInDescriptor` from `parentVerifiedNatively` with asserted-only denial; retains stale/post-await controls and adds admission-generation replacement refusal. Adds listed+joined positive/listed+not-joined negative, native explicit tool versus publish/incoming grant controls, own-tool-grant denial, configured ingress-lane denial, and `automaticReplies:false` behavior.
- `test/fixtures/generic-recipient/check-scope-contract.mjs`: **45 rejection mutations**, all requiring the expected AssertionError message rather than any throw. Adds inverse channel/user identity invariant and automatic verification of the fixture hash pinned in the document. **M5:** no literal private denylist; gate injects `SCOPE_FORBIDDEN_NAMES`. All four public files, including this checker and CI workflow, are scanned without source exemptions. Absent list explicitly reports partial naming coverage and runs path/id/credential checks only.
- `.github/workflows/ci.yml`: **M3**, five-line existing-CI step runs both Node checker modes alongside existing Node checks. This follows repository standalone-checker convention without a new runner, dependency or infrastructure. The exact extracted CI step was executed locally; Bun is not installed and no Bun runner was selected.

New fixture SHA-256:
`d60e5fae843bcae9c4f49377e5bbe795e0ffe9b12fe9a49853a556b35c9d43c8`.

Unchanged `policy-shapes.json` SHA-256:
`b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed`.

## API-gap conclusion

**Yes, narrowly: AF-scope is needed.** The refined read-only lookup/current view plus host registration projector/immutable sidecars and their acceptance controls clarify the **existing conditional AF-scope item (8–16 h)**. They are not a new item or additional estimate. Static API-gap review is not implementation/runtime qualification.

A = `869bb3add7b48893d48b2ce50ea423e4b33fdfbc`; B = `29edcf8b7f7b31d0ab35f59ab0e6aab75c0f4e04`. Full table remains in document §4; summary:

| Existing pinned surface | Remaining gap / disposition |
| --- | --- |
| A `src/mcpl/inbound-admission.ts:9–28,109–145` provides exact key/binder/closed verdict/raw-item input. | No accepted-registration identity, scope or lifecycle view exposed to bind/admit. Add narrow read-only lookup/current plus configured projector, not mutable registry access. |
| A `src/mcpl/inbound-admission.ts:147–155`; `src/mcpl/server-connection.ts:591–600,1146–1167` supply attestation, epoch invalidation and grant/readiness checks. | Reuse them; native facts remain authenticated adapter work. Scope generations/descendants must invalidate with the same lifetime. |
| A `src/mcpl/channel-registry.ts:1653–1687,1887–1951,2411–2413`; `src/mcpl/types.ts:1078–1105` provide accepted descriptors and post-await registration checks. | ACK/metadata/private lookup alone cannot prove parenthood. Need verified lineage and current generation sidecars, including protected channel-bearing push ordering. |
| A `src/mcpl/channel-registry.ts:1689–1694,1777` emits registration/rejection traces. | Trace shadow-registry is asynchronous/unordered and has no accepted generation/current-ref authority. Not a replacement for AF-scope. |
| B `src/mcpl/reply-routing.ts:6–20,106–140`; `src/framework.ts:34943–34953`; `src/mcpl/channel-registry.ts:5962–5986,6043–6045` provide bounded channel-only resolution, required sync authorize, original key/grant/descriptor checks. | Reuse B. Host checks current verified scope; adapter freshly validates exact native mapping/access. No outbound thread field or resolver rerun introduced. |

# Verification Gates

All **newly run** at reported head, clean before/after, exit 0, recorded receipts. Node `v22.23.3`. Log filenames below are relative to this round's `$ORGASMIC_EVIDENCE_DIR`.

| Gate / command | Result | Log |
| --- | --- | --- |
| `orgasmic dispatch gate scope-fixtures -- node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: 54 cases, 45 rejected mutations with expected assertion messages; current doc fixture digest verified. | `gate-scope-fixtures-01M67G50CRWEN0EW8AANEP02YS.log` |
| `orgasmic dispatch gate docs-contract -- env SCOPE_FORBIDDEN_NAMES=orgasmic,soma,tarn,alnold,alder node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | PASS: all 4 files, 13 denial controls, all 5 injected names. Private policy supplied only through gate argv/env. | `gate-docs-contract-01M67G50HVMQ3JK2ZKSCBJAAEJ.log` |
| `orgasmic dispatch gate scope-runner -- bash -euo pipefail -c '<payload below>'` | PASS: executes the actual CI step; schema/hash/outcomes and both checker modes pass. Without private names it explicitly reports PARTIAL naming coverage, 8 path/id/credential controls. | `gate-scope-runner-01M67G50Q8XCXXCPGMDJME5FC2.log` |
| `orgasmic dispatch gate scope-boundary -- env SCOPE_FORBIDDEN_NAMES=orgasmic,soma,tarn,alnold,alder bash -c '<payload below>'` | PASS: direct reviewed parent, frozen ancestor, clean tree, allowed paths including CI, whitespace, both fixture hashes, recursive name grep over worktree (only git metadata excluded), identity/trailer. | `gate-scope-boundary-01M67G50WKQXPB0FPMEKWAWZ1A.log` |

Exact scope-runner payload:

```sh
block=$(sed -n "/^      - name: Scope documentation contract$/,/^$/p" .github/workflows/ci.yml | sed "1,2d;s/^          //")
test -n "$block"
printf "CI step commands:\n%s\n" "$block"
env -u SCOPE_FORBIDDEN_NAMES bash -euo pipefail -c "$block"
```

Exact scope-boundary payload (line breaks expanded):

```sh
set -eu
test "$(git rev-parse HEAD^)" = 60b506c6754f811b53c1d011a3b7067033297bc4
git merge-base --is-ancestor a316200c4781a844d6109a6f8c812c3be99e20b0 HEAD
test -z "$(git status --porcelain)"
git diff --check HEAD^ HEAD
git diff-tree --no-commit-id --name-only -r HEAD | while IFS= read -r p; do
  case "$p" in
    docs/design/*|test/fixtures/generic-recipient/*|.github/workflows/ci.yml) printf "allowed: %s\n" "$p";;
    *) exit 1;;
  esac
done
printf "%s  %s\n" b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed test/fixtures/generic-recipient/policy-shapes.json d60e5fae843bcae9c4f49377e5bbe795e0ffe9b12fe9a49853a556b35c9d43c8 test/fixtures/generic-recipient/scope-cases.json | sha256sum -c -
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

## Inherited evidence and scope

- Prior full reads of the design/fixture baseline at a316200c, pinned policy fixture, A/B source and old storage oracle remain valid **static source evidence**: those explicit objects/payloads and their read scope did not change. No old gate pass was reused or implied by resuming; every assigned gate was rerun above at the new commit.
- This round re-read old oracle lines 94–129 to check joined membership and addressed wake, and read A's trace emissions at `channel-registry.ts:1689–1694,1773–1779` using `git show` at the full A pin. No fetch/write to the shared AF clone. Current checker, fixture and CI wiring were verified directly, not assumed from the review.
- No gate failed. No full production suite or hosted CI matrix was assigned/run; the narrow real runner here is the exact new CI command block, not an AF/native integration harness.

# Unmet Criteria

None for this bounded fix round: M1–M5 and the listed LOW fixes are addressed. Recursive/cyclic lineage remains explicitly prose-only with T4 witness required, as requested; it is not claimed qualified.

# Residual Risk

- Snapshot/oracle tests establish schema/document coherence, not authenticated native truth, actual object identity, storage/wake/publication effects, D behavior or recovery. No old/new runtime parity receipt is claimed.
- The projector/view and native evidence payload remain future AF-scope/host/adapter implementation and integration work. Accepted registration alone is never authority; T3/T4 must prove the native-facts-to-sidecar path and multi-level/cyclic lineage using real registration/native-fake witnesses.
- Hosted CI/OS matrix was not run. CI without a private name list intentionally checks only paths/ids/credentials plus schema/hash/outcomes; full naming coverage is the injected dispatch gate plus the recursive grep recorded above.
- Existing human-owner transition/H1/H2/recovery/store/cutover/automatic-reply opt-in questions remain unresolved. Nothing authorizes live activation, permission narrowing/widening, root fallback or uncertain-effect replay.
