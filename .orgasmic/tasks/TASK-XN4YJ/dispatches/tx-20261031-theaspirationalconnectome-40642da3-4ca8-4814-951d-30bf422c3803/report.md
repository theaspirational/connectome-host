# TASK-XN4YJ — revision v3 drafting report

PRIVATE / LOCAL ONLY. Operator Tarn; qualifier Soma 1793443746. Not an owner decision. **No implementation, runtime qualification, activation or completeness claim.** All source/test paths and signatures proposed below are review data, not current edit authority. Original v1/v2 drafts/reviews were preserved and treated as data.

## Changed

Current evidence root: `/home/orgasmic/.orgasmic/runs/run-01M68SABS9R5S8W22G6WG469XE/evidence`; artifacts under `drafts-v3/` only. Progress was written **only to `progress.log`**, never dispatch ask or progress.md.

| Artifact | SHA256 |
| --- | --- |
| [N11-EVALUATOR-CONTRACT.md](drafts-v3/N11-EVALUATOR-CONTRACT.md) | `122036f67fe702a2a7e2f24530525e42843525ed7569d03491f1f07066eb0cd5` |
| [N10-ISSUANCE-API.md](drafts-v3/N10-ISSUANCE-API.md) | `ca42c22e09936d828ce5a8270734fc02569749401cf037520ac00f076780d5e9` |
| [PROJECTOR-BRIEF-v2.1.md](drafts-v3/PROJECTOR-BRIEF-v2.1.md) | `d222f6c7e4e4f28496ba4a8965eac641b94abb2b63606881f7a238b02d69dccd` |
| [ISSUANCE-BRIEF-v2.1.md](drafts-v3/ISSUANCE-BRIEF-v2.1.md) | `3a5ca858853fc7c5295f8926f92779415af7c266df7a9388c103f1766f14de1d` |
| [CALLPATH-PASS-v2.1.md](drafts-v3/CALLPATH-PASS-v2.1.md) | `7a168a4fff0232396257ab7f271353fc368bf3983e727ae419a456110ad97f1f` |
| [CORRECTION-LOG-v2.1.md](drafts-v3/CORRECTION-LOG-v2.1.md) | `e185d81e51818f63780a8d13966f465de86ef250b75f6889710956905f70207c` |
| [CHANGED-CONTRACT-COVERAGE-v2.1.md](drafts-v3/CHANGED-CONTRACT-COVERAGE-v2.1.md) | `37fbe8d8753142ebf5a5cecba8a1a3e7f4eed9aa2cf4aa48d5c9208b109b43b2` |
| [V2-TO-V2.1.diff](drafts-v3/V2-TO-V2.1.diff) | `38758f83ee9842e0553ebd03416f5eb9842a95949bed59ec7a92f3a5054e2528` |
| [DELTA.md](drafts-v3/DELTA.md) | `4420d271a0140915fb81273d359b72e3659199fbbcc7eb650d30fa6c6ea6bd42` |
| [SHA256SUMS](drafts-v3/SHA256SUMS) | `98be81ddf9797e74b9788d3de5e4f6f3be14d247df71e47094f86f2e03d70d66` |

Manifest covers all nine non-manifest files in drafts-v3; its own digest is reported above, not circularly included in itself.

### Bounded delta summary

- **N11 written FIRST** (10:52:57Z): one concrete proposed private evaluator contract with separate read/wake/reply(scope,item), fixed existing admission_denied refusal, SAME-operation/SAME-item parent recursion, unknown/unverifiable -> deny, cycle detection, 32-node bound and explicit abort checks. READ fact never clears WAKE/REPLY; old shared reader has no production affirmative path. Leaving either operation uncovered is PARTIAL only, not a completion policy or owner menu. Bound=32 is a proposed engineering limit, not invented native evidence.
- **N10 written SECOND** (10:54:11Z): one private authority factory/API, mandatory trusted verifier, sole lazy issuance through accepted-current lookup, one access operation and retirement over AF identities; no public raw brand factory/decoder. Closed proposed future scope is six exact host paths/symbol groups. Host private brand construction is explicit P work without casts; AF RegistrationRef is imported, never transcribed. No duplicate generation mechanism; mappingGeneration opaque. No source edits now.
- **Real AF public type surface resolved positively:** at BOTH `a0970645d403a7a76449221fc36f240a7aa2464b` and candidate `f8af03a852c85daf9bb8079937b994fe5419ca20`, package root exports RegistrationRef, VerifiedRegistrationScope, RegistrationView and related types via `src/index.ts:226`; key/input/refusal at :221. Package exports only ".", with the root declaration target in dist. Package/root source bytes are identical across these pins. Do NOT invent a missing AF export or use deep imports/transcribed brands.
- **Precise remaining artifact gap:** no delivered exact-composition archive/integrity/emitted declaration closure or host-resolved artifact receipt; dist/src/index.d.ts is not a tracked git object at either pin (not proof a built package lacks types). Host T package.json:33 declares ^0.30.0, not an exact pin to inspected AF 0.31.0 source. Separate dependency/artifact scope required; no acquisition/package/lock changes here.
- **Optional host-R defence-in-depth DEFERRED.** Existing AF B baseline still requires **config.replyAuthority AND requiredAdmission.registrations** (registrationScope). Off that branch, connection-epoch isCurrent is not an R pin; other configured descriptor/grant/policy/ingress checks remain. No optional host-retention implementation/test/mutant gate is assigned here.
- Five affected **v2.1 LOW-only copies** and unified `V2-TO-V2.1.diff`: R09 retained in an unsupported/evidence-scope correction bucket outside contract coverage; hash-stream wording narrowed to the historical v2 read; VerifiedRegistrationScope body :13–21 and view-contract :1–8 aligned, with normative comments identified deliberately. Two earlier false claims explicitly CLOSED with v1-REVIEW1 and CHECK1 links/hashes.
- Corrected arithmetic: **31 retained entries = 30 contract rows + R09=1**. Contract classes **E24/I3/P2/S1**; source-status bookkeeping **covered9 + uncovered16 + model artifact2 + defence-in-depth3 = 30**. Separate unsupported/evidence-scope correction=1, outside covered/uncovered. These are not behavioral or qualification totals, and do not count the new proposals as implemented.

DELTA.md lists changed interfaces, statuses, dependencies and open choices without another whole-inventory pass. Eight projector truth tuples, erratum/counts 21/9/8/10/1, proof classes 19/11/19, historical §3a/committed-table locator split and native-input UNKNOWN status are preserved.

## Verification Gates

No source/test/build/toolchain/package execution, native adapter/network/API call or new external data acquisition was performed. No executable gate was named by this drafting brief; no dispatch gate receipt or previous PASS is claimed. Read-only source/content/hash observations only; all progress/check notes are in `progress.log`, new load-bearing source reads in DELTA.md and N10-ISSUANCE-API.md.

| Command / read-only method | Result | Evidence/log |
| --- | --- | --- |
| First `git rev-parse HEAD` plus sha256sum of v2 manifest and CHECK1 | Required frozen HEAD `f378eadffc9a02e2ecb5a4f30e266db9321db4f5`; hashes exactly `4c27dd62e48189b77326a3404d685d8e804ba3d87e655d4594741997cf79f771` and `fb3109763cc8416a3d18477896b773b3557938b4f02975f7f7dcc07ec489a6f9`. | FIRST progress.log line, 10:50:44Z. |
| `(cd /srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v2 && sha256sum -c SHA256SUMS)`; CHECK1 full read | 6/6 OK; review read completely as evidence. Original v1 review digest rechecked `066c11fdda0948102f3b437298543d7883c3692216cea7bbe24645f3016d9e7d`. | progress.log; closure section in CORRECTION-LOG-v2.1.md. |
| `git -C /home/orgasmic/src/agent-framework show a0970645:package.json`; same with src/index.ts, candidate source/hash reads; exact two-file diff with --no-ext-diff --no-textconv | Root public types present; diff empty. Package SHA256 `e149f0a8943238a62ac82fa561ed1732187815d9e4e60f68c76f47dc8b726bc2`; barrel SHA256 `a965ca6c8a9ef75b4cd7cf4cc843b94dbc70526928f2a96d612682eb8c29c4b2`, same at both pins. | N10 §1; DELTA targeted-read table. |
| `git -C ... show <a0970645/f8af03a8>:dist/src/index.d.ts` path probes | Both unresolvable as tracked git paths. Built artifact/declaration contents remain unprovided, not assumed missing from a built package. | N10 §1; DELTA. |
| `git show <fixed host SHA>:<path> \| nl -ba`; AF equivalent on exact new-contract/LOW locators | Re-read T1 same-op/item rules, frozen scope-only reader, H brand/consumer, provenance/nominal comment ranges, AF current/readiness/clone and fixed denial/deadline, conditional B baseline; exact read list in DELTA, no v3 source grep/global audit. | N11/N10 frozen citations; DELTA targeted table. |
| Python standard-library file/hash/text reads and difflib unified output | Original v2 hashes unchanged; eight-row truth table identical; 30 contract rows E24/I3/P2/S1 and statuses 9/16/2/3; R09 retained separately; both false-claim closures and LOW cite alignment present; three evaluator signatures and real root type exports checked as text. No repository source imported or executed. | V2-TO-V2.1.diff; corrected coverage table; progress.log. |
| `sha256sum -c drafts-v3/SHA256SUMS` from that directory | 9/9 non-manifest artifacts OK. | Manifest and tool output. |
| `GIT_OPTIONAL_LOCKS=0 git status --porcelain=v1`; `git rev-parse HEAD` | Tracked tree clean; frozen HEAD unchanged. No source/test edit, staging, git write or commit. | Final boundary check; progress.log. |

Valid inherited evidence: v2's fixed-pin source/call-path conclusions and exact row/native-input data remain unchanged in the hash-verified v2 input files. CALLPATH-PASS-v2.1 is explicitly a **corrected historical v2 read record**, not a new v3 18-stream/11-file audit. This round re-read only the concrete new-contract/export/LOW locators in DELTA. No earlier test/gate PASS, candidate qualification, global absence claim or uninspected module correctness is inherited.

## Unmet Criteria

No requested drafting artifact is omitted. No source implementation, test result or qualified affirmative behavior is claimed. N11/N10 APIs and six-path list are proposals awaiting review, not implementation permission. Current task's explicit no-git-write/no-commit rule overrides generic commit wording; terminal finalization is to omit --commit and stage nothing. No dispatch ask was used for progress.

### UNVERIFIED cites / unavailable evidence

- All required new **frozen-source** API/type/selector/currentness/package-root locators resolved. New proposed file paths/symbols are P declarations, not fabricated git objects or line cites.
- `A/C:dist/src/index.d.ts` does not resolve as a tracked object: emitted exact-composition declaration contents and host resolution are **UNVERIFIED / unprovided**. Positive public source exports remain verified; these are not equivalent claims.
- R09 remains explicitly **unsupported as an E/frozen-source coverage classification**, in its own bucket; inventory bookkeeping is not recited as a frozen requirement.
- Historical literal committed §3a heading remains absent/UNVERIFIED as a locator; actual allocation table `T:docs/design/dynamic-selector-core.md:25–77` and erratum remain preserved at their fixed cites, not silently reconciled.
- Native roots/relationships/account/access/provenance, authenticated payload/schema/attestation/freshness/qualified artifact receipt identities remain **UNKNOWN**, not invented values or claims of absent code. Slack qualifier timestamps are dispatch context, not network-authenticated here. Modules outside the bounded reads are not verified by this round.

## Residual Risk / open choices

1. Review acceptance of the **one concrete N11 contract**, especially 32-node bound, fixed private denial, abort/same-item semantics and captured immutable policy/access provenance. WAKE/REPLY must ultimately have their own parent witnesses; remaining uncovered is PARTIAL only, never a permanent completion choice. No behavioral evidence was produced.
2. Review acceptance of **one N10 private authority API/brand representation and six exact future paths/symbols**. Public AF root types are present; do not expand AF exports or cast/transcribe authority. Host issuer/accessor is still unimplemented.
3. Supply/review N4/N5/N7 schema/authentication/attestation/withdrawal contracts and the other native inputs N1–N9. A structurally typed callback or H brand cannot self-attest them. N6 remains the qualified attestation-row gap.
4. Choose/deliver the exact reviewed composed artifact/integrity/declaration closure and separately scope host dependency resolution (N12/N13). Source package exports do not imply runtime/package closure or candidate adoption. No acquisition/pin/lock edit occurred.
5. Optional host-R enhancement is **DEFERRED** and not a blocker/gate. AF's conditional baseline is retained. Owner 6/7 remain separate undecided activation/external-notice issues, not substitutes for engineering requirements.
6. All actual projector/native/lifecycle/send witnesses, F2 behavioral discrimination and production/loader qualification remain outside this drafting result. Existing barrier and publication/privacy holds unchanged. No grants, wire authority, native writes, external route, deployment or completeness claim.
