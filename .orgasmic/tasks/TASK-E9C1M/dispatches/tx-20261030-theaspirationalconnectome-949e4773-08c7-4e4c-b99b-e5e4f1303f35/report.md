# F-host design fix round 4

## Changed

Head: `a316200c4781a844d6109a6f8c812c3be99e20b0`, one local commit on `4326620b2c34c0101960c7bdd5e480ae20e422d6`, with the requested author and co-author trailer. No push or history rewrite.

| Item | Disposition |
| --- | --- |
| LOW-1 | Added `authorized-human-dm`: matching permitted human peer, addressed, store/wake true. Checker asserts it alongside both A04 cases; fixture SHA updated in both docs. |
| LOW-2 | Marked exact-list `readChannels` PROVISIONAL at definition and admission use, referencing §6 T1 and §4's gap. |
| LOW-3 | Scoped T02 author-access refusal to binding/wake; explicitly preserved Mapping X ingress storage ceiling and the existing DM-only counterpart/author check. |
| Observation | Added status/conversation changes, including enabling typing, to escalation examples. |

Changed paths:
- `docs/design/generic-recipient-host.md`
- `docs/design/generic-recipient-fixtures.md`
- `test/fixtures/generic-recipient/policy-shapes.json`

Public fixture SHA-256: `b633a9b7abe8729313f1a4f7bc442dd16e46e04c061d4feae70d9bcd695fd2ed`.

## Verification Gates

Both passing gates ran at the reported clean HEAD:

| Command | Result | Log (relative to this report) |
| --- | --- | --- |
| `orgasmic dispatch gate docs-contract -- python3 "$ORGASMIC_EVIDENCE_DIR/check-doc-contract.py"` | PASS, exit 0 | `gate-docs-contract-01M67BWSP1RT2TA3ZEQBB7V3BB.log` |
| `orgasmic dispatch gate source-pins -- python3 "$ORGASMIC_EVIDENCE_DIR/check-source-pins.py"` | PASS, exit 0 | `gate-source-pins-01M67BTTMEQW472TZPSMSH3MB2.log` |

Docs gate covers the four fixes, A04 controls, hashes, both JSON path/privacy checks, numbering and estimates. Source gate rechecked frozen predicates, pins, source trees/ancestry and 1,502 payload entries using independent bytewise inventories. `git diff --check` passed; tree clean after commit.

An initial docs-gate attempt failed because its inherited assertion expected “parent outside read scope” after the intended qualification to “parent outside Mapping X read scope”. Corrected the evidence-only checker and reran successfully; retained failed log `gate-docs-contract-01M67BTTERAZSYBZDT64HGFCBZ.log`. Stale checker expectation, not a document regression.

Inherited evidence: existing design and unchanged source/old-reader manifest inputs retained from the preceding round; review read in full, SHA-256 `04a93b3faaa98d0131b1c4c68674b69011215c6892042e7dd615fd2245b6e083`. No earlier gate receipt is claimed as a pass for this round. Both scripts were updated/copied into this run's evidence and executed anew. No runtime tests, build, store opens or live ordering performed; these are specification/source checks.

## Unmet Criteria

None for the narrowed round. No independent/manager review is claimed.

## Residual Risk

Dynamic scope/thread plumbing, D's rebase/composed AF integration/tarball and the old-reader store probe remain future implementation/qualification work, not runtime evidence supplied here. The exact-list surface is explicitly provisional. Branch history still contains private material: publication must use a fresh final-tree commit on the public base; this ancestry must not be pushed.
