# TASK-EC6WW fix round 3

## Changed

Final local commit **3e7034f74741da87630bbf1983f9c456ed331567**, following
**3ea6441156a8af0c1354925bfa8f3c9b7dbc74b4**, on requested base
**8d211982eaaf59b83713ebe236e5c5c91715e00e**.
Both use Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>
as author/committer and the GPT-6.1-Sol co-author trailer.
Only changed file: `test/fixtures/generic-recipient/check-scope-contract.mjs`.
[Round diff](round3.diff); [whole base-to-tip diff](final-net.diff).

Read REVIEW2 in full; verified SHA-256
`ae9e909e1c3b7b94d1ec986ca02b64615deac1d76efcf26ed7ccdb0675d99562`.
No detector added or changed; the scoped probe verifies detector branches and
`neutral()` byte-identical to the round base.

- Collision is a **class**: any 5-character alphanumeric token after
  task-/conv- without i/l/o/u fits the alphabet (minted stems require a letter).
  Ordinary words cannot be distinguished without exemptions. Named
  task-state/task-graph rejection controls supplement task-based; no exclusions.
- Recounted **seven** uncovered source-minted families, not six. Read-only
  `/home/orgasmic/src/orgasmic/shipped/` prefix declarations:
  ART- (`plugins/artifacts/plugin.org:20`),
  CMT- (`schema/node-types/comment.org:7`),
  SEC- (`schema/node-types/secret.org:7`),
  dec_ (`plugins/decisions/plugin.org:13`),
  term_ (`plugins/glossary/plugin.org:13`),
  gotcha- (`plugins/gotchas/plugin.org:13`),
  MEET- (`skills/orgasmic-plugin-author/SKILL.md:158`).
  Each has a synthetic “remaining limitation:” MISS control.
  AGENT/PROJECT are legacy rules, not source-minted families.
- Added all-digit stem negative control, killing m10's deleted lookahead.
  Added explicit %2F-adjacency limitation/comment and pinned MISS control.
- Docs-mode benign identifier samples now assert the identifier predicate only;
  self-test retains their full scan checks to preserve consumer-divergence coverage.
  The injected naming policy remains active and `neutral()` is unchanged.
- Because uncovered families, collisions and encoded adjacency remain, the
  comment now says this **cannot be the sole publication privacy gate**.
  Fresh public ranges still need complementary checks and manual findings.

### Corrected impossible point 4

The brief's requested full docs PASS with forbidden name `model` was impossible:
real inputs contain that substring. Tarn explicitly replaced that expectation
(ask `ask-858760c05ce843d49d81f12b2485df17`):
retain genuine full-scan rejection; test identifier-only controls under model;
run successful full docs with absent synthetic name zebra; retain injected-name
denial control. No input edits, exemptions or weakened naming checks.

The first real naming hit is
`docs/design/generic-recipient-scope-contract.md:359`:
the word “models”. Full docs under model correctly exits **1** there.
Identifier-only controls under model exit **0**, and a synthetic sample containing
the injected name is still rejected. Zebra full docs exits **0** with
**1 injected names**, 12 rejected controls.

## Verification Gates

Final identity **H**: head `3e7034f74741da87630bbf1983f9c456ed331567`;
checker SHA-256 `842b5634609911d495dc13ac236e6818d7cc4f699efec3c6b8f739c5af954226`.

Every H gate ran once after the qualifier's additional comment requirement.
All attempts retained: [receipt index with exact commands, identities, exits and logs](receipt-index.md).
No retries at either head; the new head received fresh gates rather than borrowing
the pre-comment receipts. No timeout/cap/inconclusive outcome.

Wrapper: `orgasmic dispatch gate NAME -- E/bounded.sh NAME COMMAND...`;
user scope MemoryMax=16G/MemorySwapMax=0,
timeout --signal=TERM --kill-after=60s 600s, 32 MiB cap, PIPESTATUS.
N = `/opt/node-v22.23.3-linux-x64/bin/node`; C = checker; E = evidence directory.
Except named variants, COMMAND starts `env SCOPE_FORBIDDEN_NAMES= N`.

| H gate | Command suffix / result |
| --- | --- |
| r3-final-self-test | C --self-test: exit 0; 57 cases, 45 existing rejections, 72 identifier cases |
| r3-final-docs-contract | C --docs-contract: exit 0; 18 files, 11 rejections, 0 names; naming PARTIAL |
| r3-final-model-expected-rejection | env SCOPE_FORBIDDEN_NAMES=model N C --docs-contract: **expected exit 1**, actual non-neutral name: model; receipt remains FAILED, correct rejection |
| r3-final-identifiers-model | env SCOPE_FORBIDDEN_NAMES=model N E/identifier-only-probe.mjs: exit 0; identifiers pass, synthetic injected name rejected |
| r3-final-docs-zebra | env SCOPE_FORBIDDEN_NAMES=zebra N C --docs-contract: exit 0; 18 files, 12 rejections, 1 injected names |
| r3-final-diff-check | git diff --check c353e1c8d19a11697b5515bf3b03110de5dd85de HEAD: exit 0 |
| r3-final-scope-probe | E/shapes-scope-probe.mjs: exit 0; actual first naming hit, unchanged detector/neutral, reconstructed literals, whole diff/messages |

For each mutant below: gate `r3-final-mN`, command
`env SCOPE_FORBIDDEN_NAMES= N E/identifier-mutants.mjs mN`.
All harness exits 0; every mutated checker exits 1 with its named kill.

| Mutant | Named kill (prefix “identifier detector control:” unless marked scan) |
| --- | --- |
| m1 old broad detector | ordinary filename run mutants |
| m2 uppercase-only | lowercase with digits |
| m3 detector removed | uppercase without digits |
| m4 divergent scan | **identifier scan control:** ordinary filename run mutants |
| m5 CONV removed | uppercase prefix CONV |
| m6 node shape removed | minted task all-letter shape |
| m7 run shape removed | compact run leading underscore shape |
| m8 node boundaries broken | minted node leading underscore task |
| m9 run boundaries broken | compact run leading underscore shape |
| m10 letter lookahead removed | non-shape node all-digit stem task |

### Earlier/inherited evidence

Earlier round-3 attempts remain at head
`3ea6441156a8af0c1354925bfa8f3c9b7dbc74b4`, checker SHA-256
`76a347efe28bc8b278a39fc4e91faf7950b15d846d6bf86823480ec1935915d0`:
same required checks/probes passed, except the expected model full-scan exit 1.
They are indexed separately, not relabelled as H receipts. The only follow-up
code change was the qualifier-requested three-line sole-gate warning.

Prior reviews/receipts are historical context, not reused head proof. Original
base docs failure and earlier failed m1/m4 harness-expectation receipts remain
failures/observations at their original identities; no retroactive passes.

## Unmet Criteria

None under Tarn's corrected point 4. The original model full-PASS expectation
was explicitly replaced, not silently claimed satisfied. Zero-name coverage
remains **PARTIAL**; zebra is one synthetic name, not the complete private list.

## Residual Risk

**History is not cleaned:** a5017576 remains a known positive finding carrying
real identifiers. No rewrite/squash/deletion; private refs retained. Prior
reports/evidence and the shared report drop were not sanitized.

The seven uncovered families, collision class, %2F adjacency, legacy-family gaps,
and encoded/reconstructed forms remain limitations. Therefore this checker
**cannot be the sole publication privacy gate**. A fresh public range still
needs complementary checks and manual findings. PASS describes only the tested
predicate/inputs, not universal privacy, production qualification or publication
approval. Reconstructed synthetic fixtures and all commit messages in the net
range were reviewed; no ledger values were copied.

LOCAL ONLY. No push/PR/merge/install/live operation or parallel .4 changes.
