# Changed

- Head: `60b506c6754f811b53c1d011a3b7067033297bc4` on `task-ky73n-impl`.
- Direct parent: frozen `a316200c4781a844d6109a6f8c812c3be99e20b0`.
- Local commit only; no push, merge, production changes, dependencies or lockfile changes.
- Author: Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>.
  Trailer: `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.

Files (the entire committed delta):

1. `docs/design/generic-recipient-scope-contract.md` — connector-neutral scope predicate, authenticated evidence/parent boundary, independent read/store/wake/reply permissions, registration ordering, lifecycle/epoch invalidation, source-pinned API-gap table and minimal proposed interface. No new owner decision; existing transition/recovery decisions remain open in the parent design.
2. `test/fixtures/generic-recipient/scope-cases.json` — 46 invented, independent snapshots covering A04, T01–T03, dynamic registration/join/DM cases and negative controls; explicit store/wake/new-binding/reply/exact-destination/private-notice expectations.
3. `test/fixtures/generic-recipient/check-scope-contract.mjs` — Node-built-in-only closed-schema checker, tiny pure documentation oracle, coverage and mutation self-tests, neutral-naming/path/internal-id gate. Reads public JSON/text only: no store opens, network, subprocesses, writes, production imports or environment data.

New fixture SHA-256:
`137cbd86e15e246fa05b47a99f0ededc4e51a539d2e704d83114b09c417c8e57`.

Unchanged pinned `policy-shapes.json` SHA-256:
`b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed`.

Read the supplied project baseline; no applicable AGENTS.md, conventions or gotchas were supplied/found. Read the complete frozen host design and shared fixture specification, pinned policy fixture, old storage oracle, and relevant AF source via `git show <explicit-sha>:<path>`. No fetch or write to the shared AF clone.

## API-gap table

A = `869bb3add7b48893d48b2ce50ea423e4b33fdfbc`.
B = `29edcf8b7f7b31d0ab35f59ab0e6aab75c0f4e04`.
All file:line references below are AF-repository relative at the indicated pin.

| Surface | Existing support | Missing / engineering disposition |
| --- | --- | --- |
| A binder/input | `src/mcpl/inbound-admission.ts:9–28,109–145`: exact issued key, closure, detached raw item, signal, closed verdict. | No accepted-registration/scope view in bind/input. Add narrow opt-in AF-scope view/projector, not a mutable registry. |
| A attestation | `src/mcpl/inbound-admission.ts:147–155`; `src/mcpl/server-connection.ts:1550–1559,1618`: result validation before bind. | Reuse for authenticated account/scope-producer agreement. Host/adapter must verify native facts; initialize echo alone is not authentication. |
| A epoch/grants | `src/mcpl/inbound-admission.ts:101–127`; `src/mcpl/server-connection.ts:591–600,1146–1167`. | Existing invalidation, cancellation, epoch/readiness/grant checks suffice for K; scope generations need the same lifetime. |
| Registration | A `src/mcpl/channel-registry.ts:1568–1572,1653–1687`; `src/framework.ts:34974–34999`. | Existing dynamic connection-qualified registration/itemized ACK before reconciliation; acceptance of nonempty id is not permission. Need host scope hook and accepted generation identity. |
| Changes/ingress race | A `src/mcpl/channel-registry.ts:1703–1784,1887–1951`. | Existing registration-before-channel-ingress and post-await descriptor checks. Missing verified parent/counterpart/membership lineage and descendant invalidation; protected channel-bearing push cannot lazy-register. |
| Descriptor lookup | A `src/mcpl/types.ts:1078–1105`; `src/mcpl/channel-registry.ts:2411–2413`. | Address/metadata can carry opaque data, not authority. Private framework registry lookup is not a supported binder scope interface. |
| B resolution | `src/mcpl/reply-routing.ts:6–20,106–140`; `src/mcpl/reply-admission.ts:28–44`. | Reuse bounded post-A channel-only resolution and required synchronous authorize. At this B pin authorize is required. Native thread adapter/dynamic reply ceiling remain missing. |
| B frozen send | `src/framework.ts:34943–34953`; `src/mcpl/channel-registry.ts:5962–5986,6043–6045`; `src/mcpl/reply-routing.ts:72–78`. | Existing K/grant/descriptor/all-causes checks; host must check current scope generation and adapter must freshly validate exact native root/access. |

**AF-scope plumbing: yes, narrowly.** Proposed minimum: an epoch-bound read-only view passed to bind, `lookup(channelId) -> {ref, detachedDescriptor, verifiedScope?}` and synchronous `current(ref)`, plus an opt-in bounded/cancellable host registration projector producing private immutable verified-scope sidecars. Opaque refs become usable only after accepted registration and scope readiness. Invalidation propagates across parent/child registrations and epoch changes; AF repeats current checks after A/B awaits before effects. The projector binds authenticated adapter facts to AF-issued K/R; no peer mints runtime identities. No new wire capability, thread field, production interface or implementation is shipped.

# Verification Gates

Runtime: Node `v22.23.3`. All three gates ran at the reported head, clean before/after, exit 0; the wrapper recorded reusable receipts. No receipt reuse was attempted.

| Gate / exact command | Result | Log (relative to this evidence directory) |
| --- | --- | --- |
| `orgasmic dispatch gate scope-fixtures -- node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: 46 schema/oracle cases; 39 rejection mutations. | `gate-scope-fixtures-01M67EY1SPRN4ZC2RKTWSF4BSF.log` |
| `orgasmic dispatch gate docs-contract -- node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | PASS: new public document/data; all five forbidden names, machine paths, internal ids/UUIDs and credential-shaped text rejected; 13 denial controls. | `gate-docs-contract-01M67EY1YQW0322XZ30VAY0B96.log` |
| `orgasmic dispatch gate scope-boundary -- bash -c '<script below>'` | PASS: direct frozen parent, clean tree, allowed changed paths, whitespace, old/new fixture digests, commit identity/trailer. | `gate-scope-boundary-01M67EY2438NAQY8WAX9H8M003.log` |

Exact scope-boundary shell payload (formatting expanded for readability):

```sh
set -eu
test "$(git rev-parse HEAD^)" = a316200c4781a844d6109a6f8c812c3be99e20b0
test -z "$(git status --porcelain)"
git diff --check HEAD^ HEAD
git diff-tree --no-commit-id --name-only -r HEAD | while IFS= read -r p; do
  case "$p" in
    docs/design/*|test/fixtures/generic-recipient/*) printf "allowed: %s\n" "$p";;
    *) exit 1;;
  esac
done
printf "%s  %s\n" b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed test/fixtures/generic-recipient/policy-shapes.json 137cbd86e15e246fa05b47a99f0ededc4e51a539d2e704d83114b09c417c8e57 test/fixtures/generic-recipient/scope-cases.json | sha256sum -c -
git log -1 --format="%H%n%an <%ae>%n%b"
test -z "$(git status --porcelain)"
```

Development-only failures, fixed before commit/gates: `python` was unavailable (used `python3`); the initial checker document URL went one parent too far (corrected to the repository-relative URL). Two context-mismatched patch attempts changed nothing. No gate failed and no production diagnostics were encountered. The above logs are the final raw gate output, not runtime conformance receipts.

# Unmet Criteria

None for this bounded design/public-fixture slice. No production implementation, native adapter, threaded typing, live data, new infrastructure or integration build was attempted or claimed.

# Residual Risk

- Fixtures validate schema and documentation consistency, not actual native facts, authentication, object identity, storage writes, wakes, publication, D behavior or recovery.
- AF-scope interface remains a proposal requiring implementation/testing in the authorized AF slice, then a newly pinned composed integration artifact. T2–T5 still need host/adapter implementation and real-path/native-fake qualification.
- Old storage expectations are source-derived only; no old/new production-path parity run is claimed. The snapshot oracle abstracts valid provenance/current access; it cannot prove an adapter implements those checks.
- No existing owner transition/H1/H2/recovery/store/cutover/opt-in decision is resolved; no live activation or changed permission/recovery guarantee is authorized.
