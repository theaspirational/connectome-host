# TASK-EC6WW implementation report

## Changed

Local commit: `a5017576d3ad500f1fc59cbc6a97f18bb88fcbdd`.
Author/committer: Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>.
Trailer: Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>.

Only changed file: `test/fixtures/generic-recipient/check-scope-contract.mjs`
(35 insertions, 1 deletion). Full diff: [change.diff](change.diff), also included below.
No inputs removed, no name/path exemptions, no fixture/oracle changes.
Added `docs/design/f-host-h-brief.md` to docs mode; the brief passed unchanged.
No installs, build roots, pushes, PRs, merges, live operations, or parallel .4 branch changes.
Read supplied project baseline; conventions/gotchas explicitly absent.

### Predicate before / after

Before:

```js
/\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/iu.test(content)
```

After, one shared function used by the actual scan and direct controls:

```js
function internalIdentifier(content) {
  return /\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/u.test(content) ||
    /\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]*[0-9][A-Z0-9]*\b/iu.test(content);
}
```

This implements the proposed rule: uppercase prefix/suffix is reserved even
without digits; any prefix case is reserved when its alphanumeric suffix
contains a digit. Ordinary lowercase words no longer match.
The supplied base actually contains one predicate copy, at line 233, not two.
The new shared function prevents duplicate policy definitions; controls exercise
both that function and the production `neutral()` scan consumer.

### Discriminating controls

Every new identifier sample is assembled at runtime. Both modes run the controls;
each new case checks both the shared detector and the actual neutral scan.

| Sample | Required / observed at H |
| --- | --- |
| TASK-EXAMPLE | Rejected, internal identifier |
| run-0123456789 | Rejected, internal identifier |
| task-4zm52 | Rejected, internal identifier |
| run-01M68913ZJTD9137NBTA1RGTXS (26-character ULID-format suffix) | Rejected, internal identifier |
| AgEnT-a1b | Rejected, internal identifier |
| RUN-EXAMPLE, AGENT-EXAMPLE, PROJECT-EXAMPLE | Rejected, internal identifier |
| run-mutants | Accepted |
| agent-overlay | Accepted |
| agent-config | Accepted |
| project-root | Accepted |
| Existing runtime UUID control | Rejected, internal UUID |

Existing TASK/run/UUID denial controls at the end of docs mode remain unchanged.
H docs mode scans 18 public files, rejects all 11 existing denial controls, and
also passes all 13 new discriminating cases through detector and scan.

### Mutants with named kills

All mutant runs are labeled H below. Source transformations and exact assertion
verification are in [identifier-mutants.mjs](identifier-mutants.mjs).
Each runs the real self-test against mutated source in a Node module evaluation,
preserving input URL resolution. No worktree edits are made. The harness requires
checker exit 1 and the named assertion; its own exit 0 means the mutant was killed.
m4 deliberately substitutes an old independent predicate at the scan consumer,
simulating divergent copies while leaving the direct detector correct.

| Mutant | Change | Named failure observed | Result |
| --- | --- | --- | --- |
| m1 | Restore old /iu detector | identifier detector control: ordinary filename run mutants | KILLED |
| m2 | Uppercase-only detector | identifier detector control: lowercase with digits | KILLED |
| m3 | Remove detection (return false) | identifier detector control: uppercase without digits | KILLED |
| m4 | Scan consumer diverges from shared detector | identifier scan control: ordinary filename run mutants | KILLED |

Mutant checker SHA-256 (source artifact names `m1.checker.mjs` through `m4.checker.mjs`):

- m1: `520b2b5e972c10bec62359d88644f3c4da02bb4edc85f5ff7e562756ad6f6a0b`
- m2: `b97e9e29f80dfc36ffeb1da9c27862987e65b911ad641f62cc1dbd972eabe7b8`
- m3: `5f7994f145c7bb3d5c5ec3400ddd1693cfe33c0a2d4b6de31ccda4161481da73`
- m4: `4871b35ac9d2bf1276f4ca6b66c5d6992afa7b9bab8bd310c418834a95194ee1`

## Verification Gates

Exact identity labels (every row below uses one):

- **B**: head `c353e1c8d19a11697b5515bf3b03110de5dd85de`;
  checker SHA-256 `aadccf28b8ed364c907d7e6141090293a5eb0824cfcbba336f6cdf3772a2ef0c`.
- **H**: head `a5017576d3ad500f1fc59cbc6a97f18bb88fcbdd`;
  checker SHA-256 `fc8734cc996b460aac84ceb1e880a572f635d020a349855519b36a8f5d787bac`.

All gates ran once in the assigned worktree through
`orgasmic dispatch gate NAME -- "$ORGASMIC_EVIDENCE_DIR/bounded.sh" NAME COMMAND...`.
The retained [bounded.sh](bounded.sh) records head, checker hash, argv, PID/PGID,
and raw log. It runs in a user systemd scope with `MemoryMax=16G`,
`MemorySwapMax=0`, `timeout --signal=TERM --kill-after=60s 600s`,
32 MiB output cap and explicit `PIPESTATUS` capture.
124/137/141 or cap is INCONCLUSIVE. None occurred; no retries.
All receipts show clean trees at invocation.

Let N = `/opt/node-v22.23.3-linux-x64/bin/node` (verified v22.23.3),
C = `test/fixtures/generic-recipient/check-scope-contract.mjs`,
E = this evidence directory.

| Identity | Gate | Command after bounded wrapper | Outcome |
| --- | --- | --- | --- |
| B | base-self-test | N C --self-test | PASS, exit 0; 57 cases, 45 rejection controls |
| B | base-docs-contract | N C --docs-contract | FAILED, exit 1; expected base observation: internal identifier |
| H | head-self-test | N C --self-test | PASS, exit 0; 57 cases, 45 existing rejection controls, 13 new identifier cases |
| H | head-docs-contract | N C --docs-contract | PASS, exit 0; 18 files, 11 denial controls; naming PARTIAL |
| H | head-m1 | N E/identifier-mutants.mjs m1 | PASS, mutant killed; harness exit 0 |
| H | head-m2 | N E/identifier-mutants.mjs m2 | PASS, mutant killed; harness exit 0 |
| H | head-m3 | N E/identifier-mutants.mjs m3 | PASS, mutant killed; harness exit 0 |
| H | head-m4 | N E/identifier-mutants.mjs m4 | PASS, mutant killed; harness exit 0 |
| H | head-diff-check | git diff --check c353e1c8d19a11697b5515bf3b03110de5dd85de HEAD | PASS, exit 0 |

B's failure is pre-existing, not a head regression. The original source's first
old-predicate match is `run-mutants` at checker line 343 in
`../../stubs/f-host-3/run-mutants.mjs`; see
[base-token-observation.txt](base-token-observation.txt).
The failure stack is neutral:233 / docsContract:361.
The earlier 6JAEQ and .4 FAILED receipts remain historical failures: no
retroactive PASS is claimed, nor was the parallel .4 branch inspected or changed.

### Receipt logs

- [gate-base-docs-contract-01M6892M5JVDSTDJVW4G4ES1FH.log](gate-base-docs-contract-01M6892M5JVDSTDJVW4G4ES1FH.log)
- [gate-base-self-test-01M6892M0H864ZCER62E5E93MH.log](gate-base-self-test-01M6892M0H864ZCER62E5E93MH.log)
- [gate-head-diff-check-01M6893E3374W3CDMGDRNWGCRN.log](gate-head-diff-check-01M6893E3374W3CDMGDRNWGCRN.log)
- [gate-head-docs-contract-01M6893DXCDQKF0WT5CWF8CS4T.log](gate-head-docs-contract-01M6893DXCDQKF0WT5CWF8CS4T.log)
- [gate-head-m1-01M6893XJHPK1KXKCCMK1GKSQQ.log](gate-head-m1-01M6893XJHPK1KXKCCMK1GKSQQ.log)
- [gate-head-m2-01M6893XQRAX5N7NYQEN99PFQR.log](gate-head-m2-01M6893XQRAX5N7NYQEN99PFQR.log)
- [gate-head-m3-01M6893XX5Q3AY6JP3JYJZQV72.log](gate-head-m3-01M6893XX5Q3AY6JP3JYJZQV72.log)
- [gate-head-m4-01M6893Y2HS0KJ5TPECAJ8WTMC.log](gate-head-m4-01M6893Y2HS0KJ5TPECAJ8WTMC.log)
- [gate-head-self-test-01M6893DRC63X64VQHW88RVTNX.log](gate-head-self-test-01M6893DRC63X64VQHW88RVTNX.log)


Raw command output is additionally retained in `base-*.raw.log` and
`head-*.raw.log`. Final worktree status is clean.

## Unmet Criteria

None for this scoped implementation. Full naming coverage is explicitly
**PARTIAL**, because no nonempty `SCOPE_FORBIDDEN_NAMES` was supplied.

## Residual Risk

This is a documentation-only Node checker, not production/runtime qualification
or publication approval. Private ancestry remains LOCAL ONLY.
The H brief's historical prose about its former exclusion was intentionally not
edited: this task permits only a minimal flagged-token correction, and no
correction was needed. Current scan coverage is established by this head's
receipt, not those historical statements.

## Full diff

```diff
diff --git a/test/fixtures/generic-recipient/check-scope-contract.mjs b/test/fixtures/generic-recipient/check-scope-contract.mjs
index b47d8aed..b2a3101b 100644
--- a/test/fixtures/generic-recipient/check-scope-contract.mjs
+++ b/test/fixtures/generic-recipient/check-scope-contract.mjs
@@ -224,13 +224,19 @@ const toolingStem = ['org', 'asmic'].join('');
 const toolingEnvironment = new RegExp(`\\b${toolingStem}_[A-Z0-9_]+\\b`, 'iu');
 const toolingCli = new RegExp(toolingStem, 'iu');
 const programmeIntegration = new RegExp(`\\b${['IN', 'T'].join('')}-[0-9]+\\b`, 'iu');
+// Uppercase identifiers remain reserved; other prefix cases require a digit.
+// Keep one detector for public-text scanning and the discriminating controls.
+function internalIdentifier(content) {
+  return /\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/u.test(content) ||
+    /\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]*[0-9][A-Z0-9]*\b/iu.test(content);
+}
 function neutral(content) {
   assert(!toolingEnvironment.test(content), 'tooling environment identifier');
   assert(!toolingCli.test(content), 'tooling CLI identifier');
   assert(!programmeIntegration.test(content), 'programme integration identifier');
   for (const name of forbiddenNames) assert(!content.toLowerCase().includes(name.toLowerCase()), `non-neutral name: ${name}`);
   assert(!/(?:\/(?:home|srv|tmp|etc|var|Users|mnt|opt|private)\/|[A-Za-z]:\\)/u.test(content), 'machine-local path');
-  assert(!/\b(?:TASK|RUN|AGENT|PROJECT)-[A-Z0-9]+\b/iu.test(content), 'internal identifier');
+  assert(!internalIdentifier(content), 'internal identifier');
   assert(!/\b[0-9a-f]{8}(?:-[0-9a-f]{4}){3}-[0-9a-f]{12}\b/iu.test(content), 'internal UUID');
   assert(!/\b(?:sk-[A-Za-z0-9_-]{12,}|Bearer\s+[A-Za-z0-9._-]+)\b/u.test(content), 'credential-shaped text');
 }
@@ -247,7 +253,33 @@ function rejectsMutation(mutator, message, checker = validateSchema) {
   const changed = structuredClone(fixture); mutator(changed);
   expectRejected(() => checker(changed), message); rejectionChecks++;
 }
+function identifierControls() {
+  // Assemble samples so the source remains a scan input without exemptions.
+  const cases = [
+    ['uppercase without digits', ['TASK', 'EXAMPLE'], true],
+    ['lowercase with digits', ['run', '0123456789'], true],
+    ['lowercase alphanumeric', ['task', '4zm52'], true],
+    ['run ULID format', ['run', '01M68913ZJTD9137NBTA1RGTXS'], true],
+    ['mixed-case prefix with digit', ['AgEnT', 'a1b'], true],
+    ...['TASK', 'RUN', 'AGENT', 'PROJECT'].map(prefix =>
+      ['uppercase prefix ' + prefix, [prefix, 'EXAMPLE'], true]),
+    ...[['run', 'mutants'], ['agent', 'overlay'], ['agent', 'config'], ['project', 'root']]
+      .map(parts => ['ordinary filename ' + parts.join(' '), parts, false]),
+  ];
+  for (const [name, parts, rejected] of cases) {
+    const sample = parts.join('-');
+    assert.equal(internalIdentifier(sample), rejected, `identifier detector control: ${name}`);
+    if (rejected) {
+      assert.doesNotThrow(() => expectRejected(() => neutral(sample), 'internal identifier'),
+        `identifier scan control: ${name}`);
+    } else {
+      assert.doesNotThrow(() => neutral(sample), `identifier scan control: ${name}`);
+    }
+    console.log(`PASS identifier control: ${name} -> ${rejected ? 'rejected' : 'accepted'} (detector and scan)`);
+  }
+}
 function selfTest() {
+  identifierControls();
   rejectsMutation(d => { d.extra = true; }, 'unknown member');
   rejectsMutation(d => { delete d.version; }, 'missing member');
   rejectsMutation(d => { d.version = 2; }, 'unsupported version');
@@ -331,12 +363,14 @@ function selfTest() {
   console.log('PASS naming-policy parser: missing/empty/blank lists select PARTIAL; invalid nonempty entry refuses');
 }
 function docsContract() {
+  identifierControls();
   // Supplemental input artifacts belong only to this mode, not the frozen oracle.
   const texts = [...publicTexts];
   for (const path of [
     './check-scope-contract.mjs',
     '../../../.github/workflows/ci.yml',
     '../../../docs/design/f-host-2a-brief.md',
+    '../../../docs/design/f-host-h-brief.md',
     '../../../docs/design/recipient-policy-staging.md',
     '../../stubs/f-host-3/check-compiler-controls.mjs',
     '../../stubs/f-host-3/check-v1-compiler-controls.mjs',

```
