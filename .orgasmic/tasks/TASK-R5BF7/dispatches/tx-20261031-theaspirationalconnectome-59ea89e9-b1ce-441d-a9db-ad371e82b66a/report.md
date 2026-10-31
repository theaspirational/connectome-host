# TASK-R5BF7 — LOW-only fix round

## Changed

- Local commit `d7154d79b161e0827637e715b0952001b010b393`, parent
  `0f18209b673058bab2b7c5ac695409d6ab632020`.
- Only tracked change: `docs/design/f-host-h-brief.md`: split CI attribution by
  claim/job in the toolchain row, distinguishing Node 22.12.0 build/typecheck
  baseline from Node 24 + Bun 1.4.3 lockfile-agreement job; added the requested
  one-sentence separate-checker-fix deferral near the publication section.
- Verified each revised claim directly against unchanged ci.yml:25,28;26–29;
  49–50;82–85;87–90;92–93;139–147;151–152;154–155. Checked checker:233 directly.
- Updated this run's untracked `verify-brief.py`: independently extracts
  id-shaped inline literals (including off/full), excludes explicit non-case
  vocabulary and hexadecimal commit/digest literals, then ASSERTS
  `set(cited) <= fixture_ids`. Also verifies fabricated kebab-case and single-word
  ids are rejected. All 18 actual cited ids pass; the script no longer merely
  prints the intersection.
- Review input `/srv/review-drop/TASK-R5BF7-REVIEW1.md` read in full and SHA-256
  verified: `0cbc1dda57233de2fe39244fd023f3e1d32b3ed3a8b4b577484d5b30129672e4`.
- No checker/code/fixture/lock/CI edits. Required Vasyl noreply author/committer
  and GPT-6.1-Sol coauthor trailer verified. Clean worktree; no push.

## Verification Gates

All rerun through `orgasmic dispatch gate` at final commit `d7154d79` with clean
worktree. No previous-round PASS receipt was reused. Common bounded command:
`bash test/stubs/f-host-2a/bounded-gate.sh 60s <command>` with this run's evidence
folder as BOUNDED_LOG_DIR. Timeout `--signal=TERM --kill-after=60s`, 65536-byte
cap; 124/137/141/cap = INCONCLUSIVE. Every invocation exited 0 under the cap.
Log filenames below are relative to this report's evidence directory.

| Gate | Command after bounded wrapper | Result / log |
| --- | --- | --- |
| self-test | `node test/fixtures/generic-recipient/check-scope-contract.mjs --self-test` | PASS: 57 cases, 45 rejection controls, four oracle mutants and parser/singleton controls. `gate-self-test-01M67ZRDJ5JYQ0QRCWS9ZQ3R4Q.log`; raw `bounded-output.wj9gAq.log` (876 bytes) |
| docs-contract | `node test/fixtures/generic-recipient/check-scope-contract.mjs --docs-contract` | Exit 0, explicitly PARTIAL naming coverage; existing 9 inputs, 11 controls, 0 injected names. `gate-docs-contract-01M67ZRDRR5WA5AW3BYTXD29Y4.log`; raw `bounded-output.qc2hsE.log` (230 bytes) |
| diff-check | `git diff --check 0f18209b673058bab2b7c5ac695409d6ab632020 HEAD` | PASS. `gate-diff-check-01M67ZRDY2FWSDNEZWHC3BRDQA.log`; raw `bounded-output.94JNLP.log` (0 bytes) |
| brief-source-check | `python3 "$ORGASMIC_EVIDENCE_DIR/verify-brief.py"` | PASS: original table/goal, unchanged input bytes/digests, asserted 18 cited IDs subset, two fabricated-ID rejection controls, targeted privacy checks. `gate-brief-source-check-01M67ZRE3MAPW9H4T4J5G08XXV.log`; raw `bounded-output.gXsPaj.log` (2133 bytes) |

Inherited evidence scope: prior design/source reads and review findings remain
context for unchanged inputs, not new gates. The previous evidence script was
copied from the prior run into this run's evidence directory and corrected here.
Its unchanged base/hash assertions were rerun against all 12 original input
files, confirming their bytes still match the original baseline. Standing checker
logic and fixture inputs remain unchanged; the regex source was directly reread.
No additional production/runtime/build qualification was assigned or claimed.

## Unmet Criteria

None for this LOW-only round.

## Residual Risk

- As requested, the brief remains outside the standing docs-contract scan until
  a separate regex fix. No checker changes or exemptions were introduced.
- Full private naming-policy input remains absent; docs-contract is explicitly
  PARTIAL. Targeted checks are not full privacy/publication qualification.
- LOCAL ONLY/private ancestry hold unchanged. No push or live operation; existing
  H toolchain/release/runtime qualifications remain PENDING.
