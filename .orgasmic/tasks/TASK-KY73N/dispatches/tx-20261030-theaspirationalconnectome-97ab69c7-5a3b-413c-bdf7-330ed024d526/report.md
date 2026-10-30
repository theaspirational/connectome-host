# Changed

LOW round 3 completed at **`bff8c46e3a4c403f62b738cead317863d43fca9c`**, direct parent `eba367392801c25e52b7793214fca80d2f817345`, branch `task-ky73n-impl`. Local commit only; no push/merge, production code, CI edit, dependencies, infrastructure, live data or threaded typing.

Author: Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>.
Trailer: `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.

Read `/srv/review-drop/TASK-KY73N-FIX2-DELTA-REVIEW.md` in full. Only its three LOWs were changed:

| LOW / file | Change |
| --- | --- |
| L1 — `docs/design/generic-recipient-scope-contract.md` | Documents tool-allow enforcement both at listing (A `src/framework.ts:35473`) and at dispatch (`dispatchMcplToolCall`, A `:35792–35822`, refusal at `:35811`, trace `denied by tool policy`). Keeps **without a call-time CapabilityGrant check**. Verified directly with `git show 869bb3add7b48893d48b2ce50ea423e4b33fdfbc:src/framework.ts` and numbered lines 35792–35825. Also updates the fixture hash. |
| L2 — `test/fixtures/generic-recipient/check-scope-contract.mjs` | Collects every mismatching id per oracle mutant, sorts the complete set and asserts `deepEqual(mismatches, [namedId])`. All four shipped mutants remain singleton kills. Adds a combined two-mismatch mutant, verifies its exact two-id set, and confirms the singleton assertion rejects it. |
| L3 — `test/fixtures/generic-recipient/scope-cases.json` | Only `dynamic-parent-claim-native-disagreement` changes its synthetic parent to `mailbox/other-joined` and updates its note. Its expectation is unchanged. Other uses of `mailbox/denied` are untouched. |

**No case/expectation/permission semantics changed.** The boundary gate compares the entire fixture against HEAD^ after restoring only that case's previous parent id and note in memory; equality proves all 57 cases, expectations, selectors, base facts, timelines and other inputs are unchanged.

Fixture SHA-256: `70a1827c21722b6056ba209928434877f4d4ea729338793c4d95a9e5c5126381`.

Unchanged baseline `policy-shapes.json` SHA-256: `b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed`.

Engineering asks belong to task operator **Tarn**, not human owner **Vasyl**. No ask or permission/consent decision was needed.

# Verification Gates

All four gates newly ran at the reported commit, clean before/after, exit 0, recorded receipts; no prior receipt was reused. Node `v22.23.3`. Log filenames are relative to this round's `$ORGASMIC_EVIDENCE_DIR`.

| Gate / command | Result | Log |
| --- | --- | --- |
| `orgasmic dispatch gate scope-fixtures -- node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: 57 cases, 45 existing rejection controls, 4 complete singleton mismatch sets, two-mismatch rejection control, name-policy parser controls. | `gate-scope-fixtures-01M67HV4BAA26A3GHFMGZ18JNS.log` |
| `orgasmic dispatch gate docs-contract -- env SCOPE_FORBIDDEN_NAMES=orgasmic,soma,tarn,alnold,alder node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | PASS: all 4 public files, 13 denial controls, 5 injected names. | `gate-docs-contract-01M67HV4GDWZQ7Y0A9WGQ8Z9PT.log` |
| `orgasmic dispatch gate scope-runner -- bash -euo pipefail -c '<payload below>'` | PASS: exact unchanged CI block, list absent, honest PARTIAL exit 0; 8 path/id/credential controls and 0 injected names. | `gate-scope-runner-01M67HV4NTNJ17HVX6W2E1BQ7D.log` |
| `orgasmic dispatch gate scope-boundary -- env SCOPE_FORBIDDEN_NAMES=orgasmic,soma,tarn,alnold,alder bash -c '<payload below>'` | PASS: parent eba36739, frozen ancestor, clean tree, allowed paths/whitespace, hashes, whole-fixture equivalence except L3, recursive neutrality grep, author/trailer. | `gate-scope-boundary-01M67HV4TVTJV3KSD6EDYJSHJF.log` |

Exact scope-runner payload:

```sh
block=$(sed -n "/^      - name: Scope documentation contract$/,/^$/p" .github/workflows/ci.yml | sed "1,2d;s/^          //"); test -n "$block"; printf "CI step commands:\n%s\n" "$block"; env -u SCOPE_FORBIDDEN_NAMES bash -euo pipefail -c "$block"
```

Exact scope-boundary payload:

```sh
set -eu
test "$(git rev-parse HEAD^)" = eba367392801c25e52b7793214fca80d2f817345
git merge-base --is-ancestor a316200c4781a844d6109a6f8c812c3be99e20b0 HEAD
test -z "$(git status --porcelain)"
git diff --check HEAD^ HEAD
git diff-tree --no-commit-id --name-only -r HEAD | while IFS= read -r p; do
  case "$p" in
    docs/design/*|test/fixtures/generic-recipient/*) printf "allowed: %s\n" "$p";;
    *) exit 1;;
  esac
done
printf "%s  %s\n" b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed test/fixtures/generic-recipient/policy-shapes.json 70a1827c21722b6056ba209928434877f4d4ea729338793c4d95a9e5c5126381 test/fixtures/generic-recipient/scope-cases.json | sha256sum -c -
python3 - <<'PY'
import json, subprocess
from pathlib import Path
path = "test/fixtures/generic-recipient/scope-cases.json"
old = json.loads(subprocess.check_output(["git", "show", "HEAD^:" + path]))
new = json.loads(Path(path).read_text())
case_id = "dynamic-parent-claim-native-disagreement"
before = next(c for c in old["cases"] if c["id"] == case_id)
after = next(c for c in new["cases"] if c["id"] == case_id)
assert before["input"]["parentId"] == "mailbox/denied"
assert after["input"]["parentId"] == "mailbox/other-joined"
after["input"]["parentId"] = before["input"]["parentId"]
after["note"] = before["note"]
assert new == old
print("PASS fixture equivalence: all 57 cases/expectations/policies unchanged except target parent id and note")
PY
test -n "$SCOPE_FORBIDDEN_NAMES"
pattern=$(printf "%s" "$SCOPE_FORBIDDEN_NAMES" | tr , "|")
if grep -rniE --exclude=.git --exclude-dir=.git "$pattern" .; then exit 1; else result=$?; test "$result" = 1; fi
printf "PASS recursive worktree naming grep (git metadata excluded)\n"
git log -1 --format="%H%n%an <%ae>%n%b"
test -z "$(git status --porcelain)"
```

## Inherited evidence

- Prior complete frozen design/shared-fixture reads and pinned A/B source evidence remain valid static context: source pins and read scope are unchanged. This round directly verified the added dispatch citation at full A SHA `869bb3add7b48893d48b2ce50ea423e4b33fdfbc`; no fetch or write to the shared AF clone.
- The prior API-gap table and narrow AF-scope conclusion remain unchanged in document §4: the proposed read-only registration view/projector clarifies the existing conditional AF-scope **8–16 h** item, not a new item. Static source/API-gap review is not runtime qualification.
- The inherited CI block is unchanged and executed again against the current checker. It has no private naming policy/secret dependency; PARTIAL coverage is intentional when no list is supplied.
- No earlier gate PASS is implied by resuming. No verification gate failed. No full production suite or hosted CI matrix was assigned/run.

# Unmet Criteria

None for the three-LOW bounded round.

# Residual Risk

- Still design/synthetic-only: no production implementation, native truth/authentication, actual storage/wake/publication effects or recovery qualification.
- Recursive/cyclic lineage and real AF/adapter integration still need later T4/runtime witnesses and the composed integration artifact; no new runtime claim.
- Public CI intentionally reports PARTIAL naming coverage without a list. Full injected-list publication hygiene is the separate dispatch gate above, not a new repository secret or implied CI enforcement.
- Permissions, recovery and owner-consent boundaries are unchanged; nothing authorizes live activation.
