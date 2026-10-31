# TASK-XN4YJ v3.1 — drafts-only correction report

PRIVATE / LOCAL ONLY. Operator Tarn; qualifier Soma 1793445107. Not an owner decision, implementation authorization, gate PASS or completeness claim.

## Changed

Evidence-only output: `drafts-v3.1/`. Four affected copies, unified diff, finding-to-hunk map and manifest. Original v3 drop preserved; no source/test edit, git write, commit, build/test/toolchain, acquisition or network/adapter/API call. HEAD remains `f378eadffc9a02e2ecb5a4f30e266db9321db4f5`; tracked tree clean. Git commands used `GIT_OPTIONAL_LOCKS=0`. Finalization must commit NOTHING.

### Deliverables / SHA256

| File | SHA256 |
| --- | --- |
| [drafts-v3.1/CHANGED-CONTRACT-COVERAGE-v2.1.md](drafts-v3.1/CHANGED-CONTRACT-COVERAGE-v2.1.md) | `20ed9effe4f1d469db7f13546b3b936cab2b35365300cef3524c36d5af1b28fa` |
| [drafts-v3.1/CHANGES.md](drafts-v3.1/CHANGES.md) | `605610af0d81b07947c159426135fe669ba03c9a0fafa9f91ece8f59b921d934` |
| [drafts-v3.1/DELTA.md](drafts-v3.1/DELTA.md) | `f939eec5bb9c48f0dda7177f348c9fb31bc0d54a6f1cbb3e101a5d18c87b01f4` |
| [drafts-v3.1/N10-ISSUANCE-API.md](drafts-v3.1/N10-ISSUANCE-API.md) | `cad2fe25f38fc50137baf7c74088e359c02cd3b2826438bf735a6498feaf9f3c` |
| [drafts-v3.1/N11-EVALUATOR-CONTRACT.md](drafts-v3.1/N11-EVALUATOR-CONTRACT.md) | `40925298cc1273130a3925b7da6e1f7e2477b61c3366929b5b14bf326c65104e` |
| [drafts-v3.1/SHA256SUMS](drafts-v3.1/SHA256SUMS) | `32c1248587feee044f6fc46ffca50c940f3c649334c1e3dfc7034af229336a32` |
| [drafts-v3.1/V3-TO-V3.1.diff](drafts-v3.1/V3-TO-V3.1.diff) | `2678d9f22f8e064b96f28d1952d3dfed30c7a24bc106f14db967fea4a6d3e8c1` |

### CHANGES map summary

Exact 14-hunk mapping and input hashes are in [CHANGES.md](drafts-v3.1/CHANGES.md).

| Finding | Affected copy / correction |
| --- | --- |
| H1 | N10, DELTA: ^0.30.0 excludes >=0.31.0; inspected v0.30.0 lacks registration-view/root exports. Target source ≠ current host. A2 TASK-ZE2GZ is plan-only; closed six-path scope BLOCKED. Host-R deferral premise requires actually reaching target baseline. Declaration/archive identity separately unknown. |
| M1 / SC7 | N11 §4, DELTA: 16 check/branch correspondence rows; steps and identity sources, same scope/original input, key equality, channelId deliberately NOT forwarded/re-derived, mismatch denies; exact DM author-equality paired discriminator. |
| M2 | N10, N11, DELTA: actual separately authorized/delivered A3 harness/adapters/controls is a prerequisite; existing v1 runners unchanged and unable to run proposed v2/src mutants. No runnable/killed-mutant claim. |
| L1 | N11, DELTA: 32-node refusal is a DOCUMENTED DIVERGENCE from AF current() cycle protection with no explicit depth bound, not equivalence. |
| L2 | N10, DELTA, coverage: active host scope/admission cites at T; whole-file diff against historical H4 empty. Historical H4 search/brief wording retained as history, not a fresh binding pin. |
| L3 | Coverage: R11/R22 retarget v2.1; R14/R15 likewise aligned. Historical ledger explicitly not current-host qualification. |
| Gate split | N10, N11, DELTA: every SC1–SC6 split into (a) schema/trust/API semantics BEFORE any H implementation assignment and (b) native values/qualified runtime evidence that may wait for enablement. Accepted A2 + actual A1 + actual A3 + all SC(a) remain prerequisites. |

Optional host-R defence-in-depth stays DEFERRED. The target AF baseline remains conditional on **config.replyAuthority AND requiredAdmission.registrations present** (from registrationScope), after actual dependency/artifact resolution. Absent the R branch only connection-epoch isCurrent among these lifetime checks is inferred, not original-target R protection. No duplicate generation mechanism; mappingGeneration opaque. Owner 6/7 remain separate. Eight projector rows/actual parent WAKE/REPLY stay uncovered; original tuples, erratum and 21/9/8/10/1 allocation unchanged. Coverage retains 30 contract rows (E24/I3/P2/S1; 9 covered/16 uncovered/2 model/3 defence) plus R09 unsupported bucket, without promotion.

## Verification Gates

No executable gate was named for this drafting-only correction; none ran and no dispatch gate receipt or inherited PASS is claimed. No tests/builds/typechecking were run. Verification consists solely of input hashing, bounded read-only git-object probes/diff, and document text/identity checks.

- First `progress.log` line records required HEAD and both exact input digests. Progress is only in [progress.log](progress.log).
- `sha256sum` of original v3 SHA256SUMS and REVIEW1 matches `98be81ddf9797e74b9788d3de5e4f6f3be14d247df71e47094f86f2e03d70d66` and `057a7fafd0f330a60758d9924bc69599c1cd00c27ce083f056e36d6f829f40f0`; `(cd original-v3 && sha256sum -c SHA256SUMS)` 9/9 OK. Review read in full, treated as claims/data. Rechecked after drafting. Log: [verification.log](verification.log).
- `git rev-parse --verify` resolves T/T1/A/C; AF v0.30.0 resolves `630e2a3a0768be031a4329959ab422297bb3857f`. `git show ... | nl -ba` reads listed exactly in DELTA's v3.1 targeted-check section; [source-recheck.log](source-recheck.log) captures the load-bearing rechecks. Current host range, both target versions/root exports, full v0.30.0 barrel and absent module, full frozen :86–99 conjuncts, four-argument seam, AF current(), conditional reply chain, same-op T1 requirements and hardwired v1 harness paths checked.
- Only source search: whole `v0.30.0:src/index.ts` stream with `grep -n -E 'RegistrationRef|VerifiedRegistrationScope|RegistrationScopeConfig|RegistrationView|AdmissionInput'` -> no matches, rc=1. Absent module probe returns expected path failure; not a missing required frozen pin. No host-wide/global absence conclusion or other-version/backport inference.
- `git diff --no-ext-diff --no-textconv H4 T -- src/recipient-scope.ts src/recipient-admission.ts` empty: reanchors preserve bytes. No git writes.
- Document-only text/hash comparisons: all inherited code fences unchanged (no new code); identical six SC(a)/(b) rows in the three contracts/delta; 16 correspondence rows; class/status recount; every exact diff hunk mapped; diff regenerates from exactly four copies. Log: [document-checks.log](document-checks.log). A transient prose lookup mismatch before completing the copies was corrected; it changed no source and is recorded in progress.log.
- `(cd drafts-v3.1 && sha256sum -c SHA256SUMS)` 6/6 OK. Manifest excludes itself; its own digest is listed above. Final read-only HEAD / `git status --porcelain --untracked-files=no` records unchanged HEAD and empty tracked status in verification.log.

**Inherited evidence:** hash-verified preserved v3 documents/ledger and their fixed T/T1/A/C inputs remain historical scoped source records, not fresh qualification. Unchanged v2.1 brief/callpath/correction-log copies and V2-TO-V2.1.diff stay only in the preserved v3 drop. Existing classes/counts and false-claim closures are retained as that history. This round rechecked the concrete correction-bearing facts above, not the whole inventory; it does not inherit earlier gate passes. Relative historical-document references resolve against the preserved drop as stated in CHANGES.

## Unmet Criteria

No drafting STOP encountered. This output does not deliver A2 acceptance/realized dependency transition, actual A1 archive/integrity/declaration closure, actual A3 versioned harness, or SC1(a)–SC6(a) schema/trust/API settlements. None may be presumed from this correction. Only SC(b) real native values/qualified runtime evidence may wait for enablement; a partial/model milestone does not waive the required same-operation wake/reply conjuncts.

## Residual Risk / UNVERIFIED / open choices

- **UNVERIFIED:** exact composed archive, integrity, emitted declaration contents and host-resolved artifact identity; real native identities/root/parent/account/access/DM/author facts, freshness events, I6 attestation and runtime/effect receipts; code/versions outside bounded reads. TASK-ZE2GZ plan-only identity is supplied brief context, not independently acquired task status. No newly introduced frozen-source locator is unresolved; expected v0.30.0 path absence is a finding. The historical literal §3a locator is not supplied by the committed document; its allocation/committed-locator split is inherited unchanged, not re-audited here.
- Open engineering review choices: proposed evaluator depth/refusal/abort policy and deliberate channel argument divergence; the single private issuer/accessor/no-cast H-brand contract; exact artifact/dependency plan and versioned harness scope; native schema/trust/API semantics and invalidation actors. Values remain UNKNOWN, not invented. No source assignment is authorized by these drafts.
- Existing code fences are copied historical design data only. No behavioral property, native witness, release qualification or completeness is claimed. Optional host-R remains deferred, and owner 6/7 are not engineering substitutes.
