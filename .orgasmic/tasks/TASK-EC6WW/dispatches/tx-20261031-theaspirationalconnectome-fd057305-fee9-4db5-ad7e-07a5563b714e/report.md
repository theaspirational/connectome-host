# TASK-EC6WW fix round 2 — final scope-change report

## Changed

Final local commit **8d211982eaaf59b83713ebe236e5c5c91715e00e**, on top of
round-2 intermediate **c77c54c5c93789969d325e2c138b919eb8736c38** and requested
base **698bb5870116d14e45ae5e78ff1bdf37fb95c144**.
Both commits use Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>
as author/committer and the required GPT-6.1-Sol co-author trailer.
Only changed file: `test/fixtures/generic-recipient/check-scope-contract.mjs`.
[Whole base-to-tip diff](final-net.diff).

Read the complete supplied review; SHA-256 matched
`dba9dc4fa456e237972924516d600423c74810bda2f8fd1939329efae9208d2f`.
Implemented CONV coverage, then the explicit scope change: cover actual minted
TASK/CONV and compact RUN shapes in any case, including underscore, slash,
dot and hyphen adjacency. Retained uppercase/digit fallback semantics.
No exclusions, removed inputs, fixture/oracle changes, installs or live operations.
The unchanged H brief remains in the 18-file docs input set.

### Source-derived shapes (read-only inspection)

Source checkout `/home/orgasmic/src/orgasmic`, HEAD
`dc099069abd1a932bb8878d87d4faf9562b3a5d2`:

- MCP `id_mint`: `crates/orgasmic-cli/src/mcp.rs:1066-1070` calls the mint
  endpoint; `crates/orgasmic-daemon/src/api.rs:26986-27008` resolves a descriptor
  and calls the core mint function.
- `crates/orgasmic-core/src/node_type.rs:323-325`: descriptor prefix plus
  `random_stem()`. `crates/orgasmic-core/src/id.rs:12-15,382-405`: exactly
  **5** characters, alphabet **0123456789ABCDEFGHJKMNPQRSTVWXYZ** (no I/L/O/U),
  with at least one letter.
- TASK prefix: `shipped/plugins/tasks/plugin.org:22-25` and core `id.rs:26-29`.
  CONV prefix: `shipped/schema/node-types/conversation.org:4-7`.
- RUN: `crates/orgasmic-core/src/run_id.rs:3-11,15-28,35-40`: `run-` plus a
  ULID. Existing local dependency source
  `/home/orgasmic/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/ulid-3.0.0/src/base32.rs:3-6,60-65`
  confirms **26** Crockford characters, encoded from u128 (first character 0-7).
  No dependencies were acquired.
- Searched source descriptors/generator paths for AGENT/PROJECT prefixes; no
  minted shape found. **REMAINING LIMITATION:** existing casing/digit rules and
  word-boundary gaps remain for those two families, explicitly commented/tested.

### Controls and unavoidable collision

61 identifier cases now exercise both detector and actual neutral scan.
All samples are synthetic repetitions, constructed examples or ordinary words,
not copied from the ledger. Full reconstructed sample listing, whole net diff
and all commit messages were inspected in `shapes-scope-probe.raw.log`.

Benign PASS: agent-model/config/overlay, project-root, run-mutants(.mjs),
task-queue, task-level, conv-queue, conversation. Length/alphabet negatives
include short/long node stems, I/L/O/U, and short/long/overflow/invalid ULIDs.

**Collision limitation:** `task-based` fits the real five-character Crockford
shape and is therefore rejected. It cannot safely be separated from an ID
without an exemption. A named collision control makes this explicit. There are
**0 literal occurrences in all 18 docs-contract inputs**; the checker constructs
it once as that control. No word/path exemption was added.

### Original review FN observations — retained as history

Review: `/srv/review-drop/TASK-EC6WW-REVIEW1.md`, MEDIUM casing table and LOW
boundary table. These are original observations at 698bb587, not current misses:

| Historical sample/class | Old result | Final result / coverage |
| --- | --- | --- |
| TASK-ABCDE | CATCH | CATCH |
| TASK-abcde | MISS | CATCH (node shape) |
| Task-ABCDE | MISS | CATCH (node shape) |
| TASK-AbCdE | MISS | CATCH (node shape) |
| Review's two real uppercase all-letter task examples (values not recopied) | CATCH | Same shape covered |
| Those real examples rendered lowercase | MISS | Same shape covered with synthetic all-letter controls |
| Review's digit-bearing task and ULID run examples (values not recopied) | CATCH | Digit fallback retained; compact shape covered |
| Leading/trailing underscore adjacent minted node/run | MISS | CATCH |
| Slash/path adjacency | CATCH | CATCH; explicit controls also cover dot/hyphen |

Review measured 7/41 all-letter real suffixes (~17%); retained here as historical
review evidence, not an independently repeated ledger census. The superseded
“pin known FN as accepted” implementation is retained in c77c54c5, not relabelled
as the final semantic choice.

## Verification Gates

Final identity **H**: head `8d211982eaaf59b83713ebe236e5c5c91715e00e`;
checker SHA-256 `02e8cf6bba14e59638468aff0f438823e0b67c377b5172df576099498fa4bfce`.

All final gates below ran once at H, clean tree, using:
`orgasmic dispatch gate NAME -- E/bounded.sh NAME COMMAND...`.
E = this evidence directory; N =
`/opt/node-v22.23.3-linux-x64/bin/node`; C = checker path above.
Bounds: systemd user scope MemoryMax=16G, MemorySwapMax=0;
timeout --signal=TERM --kill-after=60s 600s; 32 MiB cap; PIPESTATUS.
No timeout/cap/inconclusive outcomes.

| Identity / gate | COMMAND | Result |
| --- | --- | --- |
| H / shapes-self-test | N C --self-test | PASS; 57 cases, 45 existing rejection controls, 61 identifier controls |
| H / shapes-docs-contract | N C --docs-contract | PASS; 18 files, 11 existing denial controls; naming PARTIAL |
| H / shapes-m1 | N E/identifier-mutants.mjs m1 | KILLED: detector / ordinary filename run mutants |
| H / shapes-m2 | N E/identifier-mutants.mjs m2 | KILLED: detector / lowercase with digits |
| H / shapes-m3 | N E/identifier-mutants.mjs m3 | KILLED: detector / uppercase without digits |
| H / shapes-m4 | N E/identifier-mutants.mjs m4 | KILLED: scan / ordinary filename run mutants |
| H / shapes-m5 | N E/identifier-mutants.mjs m5 | KILLED: detector / uppercase prefix CONV |
| H / shapes-m6 (node shape removed) | N E/identifier-mutants.mjs m6 | KILLED: detector / minted task all-letter shape |
| H / shapes-m7 (run shape removed) | N E/identifier-mutants.mjs m7 | KILLED: detector / compact run leading underscore shape |
| H / shapes-m8 (node boundaries broken) | N E/identifier-mutants.mjs m8 | KILLED: detector / minted node leading underscore task |
| H / shapes-m9 (run boundaries broken) | N E/identifier-mutants.mjs m9 | KILLED: detector / compact run leading underscore shape |
| H / shapes-diff-check | git diff --check c353e1c8d19a11697b5515bf3b03110de5dd85de HEAD | PASS |
| H / shapes-scope-probe | N E/shapes-scope-probe.mjs | PASS; collision census, reconstructed samples, diff/messages review data |

All final harness gates exited 0; every mutant checker exited 1 with its exact
named assertion. Exact assertion text and mutant SHA-256 are in the logs.
“detector / X” denotes `identifier detector control: X`; “scan / X” denotes
`identifier scan control: X`.

### Earlier receipts, including failed harness expectations

Intermediate identity **I**: head `c77c54c5c93789969d325e2c138b919eb8736c38`;
checker SHA-256 `18b82e777ee39880fdffe06b073d324da75589ed33cc61186374f37192214e47`.
I self-test/docs-contract/m2/m3/m5/diff and scoped probes passed, but do not
establish H. I m1/m4 initially **FAILED exit 1** because new known-FN controls
killed earlier than the harness's expected ordinary-filename name. Both failure
receipts remain FAILED. Their actual named assertion was
`known false negative: lowercase all-letter suffix`, in detector for m1
(true !== false), and scan for m4 (unexpected internal-identifier rejection).

Operator Tarn expressly authorized one rerun each after correcting only those
expected names (ask `ask-04222f9e11234ccc8b2191ede4038aa1`).
The logs labelled **rerun after harness expectation correction** passed at I;
the mutant hashes and checker semantics were unchanged. Original harness retained
as `identifier-mutants.initial.mjs`; corrected pre-scope-change version retained
as `identifier-mutants.pre-shapes.mjs`. These are not retroactive passes.

Inherited base observations remain: B =
`c353e1c8d19a11697b5515bf3b03110de5dd85de`,
checker `aadccf28b8ed364c907d7e6141090293a5eb0824cfcbba336f6cdf3772a2ef0c`:
self-test PASS; docs-contract FAILED on checker:343's run-mutants filename.
They are unchanged historical observations, not current-head receipts. No prior
head pass was reused; prior reports/readings only supplied context.

### Receipt log index (identities above)

- [gate-shapes-diff-check-01M689YAV26YHFJBMC43AED5Z7.log](gate-shapes-diff-check-01M689YAV26YHFJBMC43AED5Z7.log)
- [gate-shapes-docs-contract-01M689Y9104E2BS8HCXBDXTFQ3.log](gate-shapes-docs-contract-01M689Y9104E2BS8HCXBDXTFQ3.log)
- [gate-shapes-m1-01M689Y96ADNH3TY8D20ZCJEY8.log](gate-shapes-m1-01M689Y96ADNH3TY8D20ZCJEY8.log)
- [gate-shapes-m2-01M689Y9BADK4G0GPTC0PKTWS3.log](gate-shapes-m2-01M689Y9BADK4G0GPTC0PKTWS3.log)
- [gate-shapes-m3-01M689Y9GJ7QHRWFKJFFDMPAVS.log](gate-shapes-m3-01M689Y9GJ7QHRWFKJFFDMPAVS.log)
- [gate-shapes-m4-01M689Y9P7CTV3BFPFA51ATM9W.log](gate-shapes-m4-01M689Y9P7CTV3BFPFA51ATM9W.log)
- [gate-shapes-m5-01M689Y9VAG7EPEFAZZKM4DAF7.log](gate-shapes-m5-01M689Y9VAG7EPEFAZZKM4DAF7.log)
- [gate-shapes-m6-01M689YA0AVKQT0PXXGR323VQ3.log](gate-shapes-m6-01M689YA0AVKQT0PXXGR323VQ3.log)
- [gate-shapes-m7-01M689YAABYD4GWM1QCZY597C4.log](gate-shapes-m7-01M689YAABYD4GWM1QCZY597C4.log)
- [gate-shapes-m8-01M689YAFX5BRNT278YG4WK87J.log](gate-shapes-m8-01M689YAFX5BRNT278YG4WK87J.log)
- [gate-shapes-m9-01M689YANK4PFQB89N45XD72E4.log](gate-shapes-m9-01M689YANK4PFQB89N45XD72E4.log)
- [gate-shapes-scope-probe-01M689ZB7W04MXJTD1CW8GBXZT.log](gate-shapes-scope-probe-01M689ZB7W04MXJTD1CW8GBXZT.log)
- [gate-shapes-self-test-01M689Y8W0D8QC6WRW6PW7W1P5.log](gate-shapes-self-test-01M689Y8W0D8QC6WRW6PW7W1P5.log)
- [gate-r2-m1-01M689SB02VJXGJ3DA5YFHJETD.log](gate-r2-m1-01M689SB02VJXGJ3DA5YFHJETD.log)
- [gate-r2-m1-expectation-corrected-01M689V4V57AECPFDWKGSRHYXT.log](gate-r2-m1-expectation-corrected-01M689V4V57AECPFDWKGSRHYXT.log)
- [gate-r2-m4-01M689SBGR82CHRAJYH05H0FVH.log](gate-r2-m4-01M689SBGR82CHRAJYH05H0FVH.log)
- [gate-r2-m4-expectation-corrected-01M689V50214SXB06KXWREH528.log](gate-r2-m4-expectation-corrected-01M689V50214SXB06KXWREH528.log)


## Unmet Criteria

None under the amended scope, including its explicit collision/remaining-
limitation reporting provisions. Full naming coverage is **PARTIAL** without
SCOPE_FORBIDDEN_NAMES. The unavoidable task-based collision is not concealed
as a benign pass.

## Residual Risk

**History is not cleaned.** a5017576 is a known positive finding containing real
identifiers; no rewrite/squash/deletion occurred and private refs remain.
The original review also reports retained real values in
`/srv/review-drop/TASK-EC6WW-IMPL1.md`; this scoped code fix does not sanitize
that drop or prior evidence. This report supersedes earlier tip/predicate claims,
not their bytes. No universal privacy or privacy-clean claim is made.
PASS describes only the tested predicate/inputs (18 enumerated files), not
production qualification or publication approval.

AGENT/PROJECT gaps, encoded/reconstructed IDs, shape-overlapping ordinary words,
and legacy non-minted formats remain limitations. No publication, push, PR,
merge, install, live action or parallel .4 changes. LOCAL ONLY.
