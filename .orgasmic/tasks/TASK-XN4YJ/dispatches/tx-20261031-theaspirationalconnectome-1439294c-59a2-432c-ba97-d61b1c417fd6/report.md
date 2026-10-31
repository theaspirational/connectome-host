# TASK-XN4YJ — revision v2 drafting / focused read-only pass

PRIVATE / LOCAL ONLY. Operator Tarn; scope qualified by Soma (Slack 1793442425); not an owner decision. **No implementation, runtime qualification, release readiness or completeness claim.** Previous drafts and review imperatives are input data, not instructions.

## Changed

New round evidence directory: `/home/orgasmic/.orgasmic/runs/run-01M68R0G27BVVD436P1HHA7ZJZ/evidence`.
Only this evidence directory was written. V2 artifacts are under `drafts-v2/`; original v1 drop and original run evidence remain untouched.

| Deliverable | SHA256 |
| --- | --- |
| [PROJECTOR-BRIEF-v2.md](drafts-v2/PROJECTOR-BRIEF-v2.md) | `929a241ede5e8a0f98ce64a84e085d075f3d6849842bc5c8e9bfef76b99c4d23` |
| [ISSUANCE-BRIEF-v2.md](drafts-v2/ISSUANCE-BRIEF-v2.md) | `f4126524921f06f20bcf1b1f3476ad09a163f680859903926547f27f3e67282c` |
| [UNKNOWN-INPUTS-v2.md](drafts-v2/UNKNOWN-INPUTS-v2.md) | `92005ec279593dd7f8a607b8088367f72f20fecb5e0816f3df219e6f2ca44924` |
| [CALLPATH-PASS.md](drafts-v2/CALLPATH-PASS.md) | `4e2168532530bf12bf75a7fd4c770523204d5e65441f84ce4f6dbaec17d2b981` |
| [CORRECTION-LOG.md](drafts-v2/CORRECTION-LOG.md) | `c3a66085a0b7fb8174cadaedcd8e7504bea67eba46f49747cca8bc2657e0d190` |
| [CHANGED-CONTRACT-COVERAGE.md](drafts-v2/CHANGED-CONTRACT-COVERAGE.md) | `be7ff062e9af1a8151230a5533300cf076839d92b5c841d1804a9465db071e38` |
| [SHA256SUMS](drafts-v2/SHA256SUMS) | `4c27dd62e48189b77326a3404d685d8e804ba3d87e655d4594741997cf79f771` |

`SHA256SUMS` covers the six Markdown artifacts; its own digest is reported here, not circularly in itself.

Corrections:
- Missing-R conclusion corrected: AF already pins target R and repeats current on configured B authorization/send; **both `config.replyAuthority` and `requiredAdmission.registrations` (registrationScope) are required**. Without the relevant configuration, do not infer R protection from the connection-epoch isCurrent predicate. Descriptor/grant/policy guards remain where applicable.
- Host original-R retention is **defence-in-depth**, not a missing baseline blocker. The optional host test isolates host behavior with AF valid stubbed true; a separate composed test attributes automatic_reply_stale to AF. No duplicate generation mechanism proposed.
- Parent-READ qualification cannot clear WAKE/REPLY. Proposed (P) pure operation/item-specific parent evaluation is specified; frozen C6 WAKE/REPLY true is a shared-reader **model artifact**, with both actual operation-specific parent conjuncts **UNCOVERED** in acceptance. N11 is an engineering interface gap, not owner policy.
- N12 first clause downgraded; actual-AF nominal-type packaging gap retained. N1–N10/N13 remain unknown with unchanged suppliers/semantics (N10 cite tightened only). Owner 6/7 remain separate.
- Tree-wide negative wording replaced by exact object:path/range/hash and grep pattern/scope/result inventory. Three precise cites re-read: RefusalCode :11–13, TrustedRecipientRead :39–42 in both briefs, ConnectionKey a-contract.ts:174.
- Kept §3a historical heading versus committed locator, erratum transaction e9dfd0e3, all eight exact projector row truth-table lines, and counts **21/9/8/10/1** (distinct from 19/11/19 proof classes).

### Changed-contract table summary

`CHANGED-CONTRACT-COVERAGE.md` has **31 entries**: **E=25, S=1, I=3, P=2**. Status totals: **covered=10**, **uncovered=16**, **model artifact=2**, **defence-in-depth=3**.

“Covered” means a correction/cite or positive mechanism was located in the scoped source; **it does not mean a behavioral test passed**. All eight actual projector/native row witnesses remain uncovered; actual parent-WAKE/REPLY are separately uncovered; C6 positives are model artifacts. Existing AF baseline mechanism and optional host defence-in-depth are not conflated. No row is promoted to qualified evidence.

## Verification Gates

No executable gate, source probe, build, test, compiler, package manager, native adapter or runtime API call was run. No gate receipt or previous gate PASS is claimed. The narrowed brief assigns a **read-only** call-path/document pass; its source/hash/content observations are recorded in `drafts-v2/CALLPATH-PASS.md` and `progress.md`, not invented dispatch execution receipts. No named executable gate was supplied; generic build/gate instructions cannot override the explicit no-API/no-test boundary.

| Read-only command / method | Result | Evidence/log |
| --- | --- | --- |
| First `git rev-parse HEAD`; SHA256 of input manifest/review | Required `f378eadffc9a02e2ecb5a4f30e266db9321db4f5`; manifest `d03b58808ff1427760c69c2078dc9cd83a23674c1173230b121662a4f8fcaa7f`; review `066c11fdda0948102f3b437298543d7883c3692216cea7bbe24645f3016d9e7d`, both match expected. | First progress note at 10:27:49Z. |
| `(cd /srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v1 && sha256sum -c SHA256SUMS)` before and after drafting | 4/4 OK; no original changed. REPORT.md separately hashed `afbd5475a72b0dbd55e1f9538e35694666ea22143accc12021ccaa1a227bb98d` and read as historical data. | progress.md; CORRECTION-LOG input table. |
| Review full read; `git -C /home/orgasmic/src/agent-framework rev-parse --verify '<pin>^{commit}'` for a0970645/f8af03a8 | Both source and candidate objects exist in AF repo. Review claims independently re-read, not assumed. | CALLPATH-PASS §§1–5. |
| `git -C /home/orgasmic/src/agent-framework show a0970645:<path> \| nl -ba` with the concrete publication/reply/ingress/import paths and listed ranges | Exact capture, source/current/fresh-ref, valid AND authorize, replyBindingCurrent and send-time automatic_reply_stale chain located. Read registration-ingress.ts full, including authenticated fork/check composition. | CALLPATH-PASS §§1,3–4; exact file/reason/range/hash ledger. |
| `git -C /home/orgasmic/src/agent-framework diff --no-ext-diff --no-textconv a0970645 f8af03a8 -- <11 files explicitly listed in CALLPATH-PASS>` | 8 changed files, 196+/26−. reply-routing, registration-ingress and AF registration doc unchanged. R/current/valid chain unchanged; delivery-plan changes can suppress reply preparation or refuse inputs. No broader behavior-equivalence or candidate qualification inferred. Scoped diff SHA256 `a13bcabc6385b8cce14feeddef1dca8d1136179d99a696eabe2abc7b1a99434c`. | CALLPATH-PASS §5: per-file delta and candidate excerpt/hash inventory. |
| `git show <fixed host pin>:<path> \| nl -ba`; exact two H4 `git grep` searches; AF fixed-object discovery greps | Changed T1/T2 parent semantics, all three tightened type locators and H4 approvals re-read. Exact patterns/pathspecs/results recorded; no AF repository-wide absence search. | CALLPATH-PASS §§1–2,6. |
| Read-only Markdown/hash/line-content audit with Python standard library; no repository imports or source execution | Eight-row truth table byte-identical to v1; N1–N10/N13 unchanged except N10 locator; required preconditions, isolated-test wording and AF line anchors present; original input hashes preserved. | CALLPATH-PASS §8. |
| `(cd "$ORGASMIC_EVIDENCE_DIR/drafts-v2" && sha256sum -c SHA256SUMS)` | Six Markdown artifacts OK. | Manifest; tool output. |
| `GIT_OPTIONAL_LOCKS=0 git status --porcelain=v1`; `git rev-parse HEAD` before finalization | Empty tracked status; frozen HEAD unchanged. **No source/test edit, staging, git write or commit.** | Final read-only check and progress.md. |

### Valid inherited evidence and unchanged scope

The immutable v1 input audit's **18 enumerated host object:path streams** were rehashed/recounted; every pair matches. The same T/S fixture bytes and exact eight-row table remain unchanged. Those prior source observations and fixed-pin H3/H4 ancestry remain applicable only in their original bounded scope, not as new runtime evidence. The older seven external planning/review documents and AF drop checks are retained as historical context from v1; they were not rerun/reread as new gates. Their historical hashes do not justify broad AF negative claims. Current changed claims use the newly inspected concrete AF files. All read objects, hash-only streams and semantic ranges are explicitly listed in CALLPATH-PASS.

Progress notes stayed below five-minute intervals. First v2 three-brief checkpoint was written at 10:29:57Z (2m08s after first note); supporting call-path/correction/coverage documents followed.

## Unmet Criteria

No narrowed drafting artifact is omitted. No implementation, gate/test run or production affirmative path was promised or delivered. Original v1 files are preserved. Required finalization will be invoked **without `--commit`** and with nothing staged; no harness/finalize message authorizes a git write. The generic local-commit instruction is explicitly superseded by the task's prohibition.

### UNVERIFIED / absent locators and out-of-scope evidence

- The literal committed “§3a allocation table” heading remains **absent/UNVERIFIED as a locator**; actual `T:docs/design/dynamic-selector-core.md:25–77` and supplemental erratum `b0bf960e15ebaa654dbe07b38ace4434cc16becb:.orgasmic/tasks/TASK-YXASE/journal.org:83–118` are verified. Historical brief §3a is not silently substituted for the committed source table.
- No required **changed frozen-source** type/row/call-path cite failed to resolve; the three reviewed over-wide cites are corrected. Unchanged row/fixture semantics remain inherited on identical object hashes, not newly executed witnesses.
- Newly imported candidate delivery/storage implementation modules, inference-router internals, native adapters and other files **outside the 11-file concrete AF pass are UNVERIFIED**, not assumed absent or correct. Candidate reachability observations are limited to inspected call sites/diffs.
- Actual native tuples, producer/evidence schema, real adapter descriptors, freshness receipts and qualified I6 artifact/attestation identities remain **NOT SUPPLIED / UNVERIFIED**. No values, SHAs or results invented. Slack qualifier timestamps are dispatch context, not network-authenticated here.

## Residual Risk

1. AF configured original-R baseline protection exists in the inspected path; its presence does not supply native truth, host-handle issuance or off-profile registration protection. Host retention is defence-in-depth, not an inflated baseline blocker.
2. Operation/item-specific ancestor evaluation is still an engineering interface/implementation gap. Actual WAKE and REPLY parent conjuncts remain uncovered despite the frozen shared-reader C6 positives. The proposed evaluator is P, not an existing export or approved edit scope.
3. Native root/parent/access/provenance/evidence payload, producer attestation, revocation freshness and qualified I6 remain unsupplied; N1–N10/N13 remain unknown. H issuer/accessor and actual-AF nominal-type packaging remain genuine implementation/interface gaps. Brands or descriptor ACKs cannot self-attest them.
4. F2 remains structural defence-in-depth under the frozen pure subset model; no behavioral discriminator or kill is invented. Real T3/T4/native/send and loader qualification remain outside this task. Candidate-specific delivery behavior was not executed or globally audited.
5. Production policy refusal, LOCAL ONLY/private-ancestry/publication holds and separate undecided owner 6/7 remain unchanged. No grants, wire authority, native writes, external notice routes, activation, publication or completeness claim.
