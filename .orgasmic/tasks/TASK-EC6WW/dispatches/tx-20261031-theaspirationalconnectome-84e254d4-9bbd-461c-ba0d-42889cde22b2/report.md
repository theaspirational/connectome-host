# TASK-EC6WW fix round 1

## Changed

Local commit: `698bb5870116d14e45ae5e78ff1bdf37fb95c144`, on top of
`a5017576d3ad500f1fc59cbc6a97f18bb88fcbdd`. Author/committer:
Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>;
Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>.

Only `test/fixtures/generic-recipient/check-scope-contract.mjs` changed:
two sample lines replaced. The task sample now uses synthetic suffix
`0abc0`; the synthetic run sample uses `'0Z'.repeat(13)` (26 characters).
The latter control is honestly named “synthetic run ULID format”.
All expected accepted/rejected semantics remain unchanged. Predicate unchanged
(byte-for-byte comparison against the preceding commit passed).
[Net base-to-tip diff](net-change.diff).

Reviewed the entire net diff and all commit messages in
`c353e1c8d19a11697b5515bf3b03110de5dd85de..HEAD`, including arrays,
joins, repetition, template strings and concatenation. Reconstructed samples
are synthetic uppercase examples, numeric sample, synthetic alphanumeric sample,
repeated 0/Z sample, mixed-case example, or the four ordinary filenames.
No programme identifiers were found in this scoped tip-diff/message review.
The explicit literal-prefix/known-suffix grep also found no matches.

## Verification Gates

**H** = head `698bb5870116d14e45ae5e78ff1bdf37fb95c144`,
checker SHA-256 `568dc0f44bfe27aa45e7852d800a2f5b99b5afce19ce5ec32456657d31d9ed12`.
Every fresh gate below is H, clean tree, exit 0. Commands use
`orgasmic dispatch gate NAME -- E/bounded.sh NAME COMMAND...`.
E is this evidence directory; N is
`/opt/node-v22.23.3-linux-x64/bin/node`; C is the checker path above.
Wrapper: user scope MemoryMax=16G/MemorySwapMax=0,
timeout --signal=TERM --kill-after=60s 600s, 32 MiB cap, PIPESTATUS.
No timeouts, cap events or retries.

| Identity / gate | COMMAND | Result |
| --- | --- | --- |
| H / fix-self-test | N C --self-test | PASS: 57 cases, 45 existing rejection controls, 13 identifier controls |
| H / fix-docs-contract | N C --docs-contract | PASS: 18 files including unchanged H brief, 11 denial controls; naming PARTIAL |
| H / fix-m1 | N E/identifier-mutants.mjs m1 | KILLED: identifier detector control: ordinary filename run mutants |
| H / fix-m2 | N E/identifier-mutants.mjs m2 | KILLED: identifier detector control: lowercase with digits |
| H / fix-m3 | N E/identifier-mutants.mjs m3 | KILLED: identifier detector control: uppercase without digits |
| H / fix-m4 | N E/identifier-mutants.mjs m4 | KILLED: identifier scan control: ordinary filename run mutants |
| H / fix-diff-check | git diff --check c353e1c8d19a11697b5515bf3b03110de5dd85de HEAD | PASS |
| H / fix-diff-id-probe | python3 E/diff-id-probe.py | PASS: known suffixes absent; reconstruction listing and predicate equality |
| H / fix-prefix-probe | bash E/prefix-probe.sh | PASS: grep -nEi 'run-\|task-\|conv-\|01M6\|4zm52' on net diff and all commit messages, no matches |

Mutant checker failures are expected exit 1 with named assertions; the harness
exits 0 only on the required kill. Scripts, mutated source and raw logs retained.
The prefix-probe uses native ERE grouping-free syntax; it is the authoritative
literal-prefix grep (the first probe's optional-group expression is not relied
upon for prefix absence). Reconstruction review covers the printed net diff.

### Inherited base observations (REUSED, not rerun)

**B** = `c353e1c8d19a11697b5515bf3b03110de5dd85de`,
checker SHA-256 `aadccf28b8ed364c907d7e6141090293a5eb0824cfcbba336f6cdf3772a2ef0c`.

Original base receipts were read and copied to [inherited-base-logs.txt](inherited-base-logs.txt):
B self-test PASS exit 0; B docs-contract FAILED exit 1, internal identifier.
These remain unchanged historical base observations, not receipts for H.
Their original base inputs, scope, exact Node argv, clean tree and checker
identity are preserved in the logs. The expected failure remains a failure;
no retroactive pass is claimed. Prior head passes are not reused.

### Fresh receipt logs

- [gate-fix-diff-check-01M689AD5KGMBRFV3GG2X9NN00.log](gate-fix-diff-check-01M689AD5KGMBRFV3GG2X9NN00.log)
- [gate-fix-diff-id-probe-01M689B0YSG8MY6HAEFBTR0ADP.log](gate-fix-diff-id-probe-01M689B0YSG8MY6HAEFBTR0ADP.log)
- [gate-fix-docs-contract-01M689ACBA072HERY26QBTWS3Q.log](gate-fix-docs-contract-01M689ACBA072HERY26QBTWS3Q.log)
- [gate-fix-m1-01M689ACGBZP6V40K7DPTZ1K93.log](gate-fix-m1-01M689ACGBZP6V40K7DPTZ1K93.log)
- [gate-fix-m2-01M689ACNCRB70R7TY9V7D1E1G.log](gate-fix-m2-01M689ACNCRB70R7TY9V7D1E1G.log)
- [gate-fix-m3-01M689ACVDQ7HBBE0NA8PB193D.log](gate-fix-m3-01M689ACVDQ7HBBE0NA8PB193D.log)
- [gate-fix-m4-01M689AD0NVZCS988THA9BM5YN.log](gate-fix-m4-01M689AD0NVZCS988THA9BM5YN.log)
- [gate-fix-prefix-probe-01M689BAEK3YFJ4K1KTE4K9JFZ.log](gate-fix-prefix-probe-01M689BAEK3YFJ4K1KTE4K9JFZ.log)
- [gate-fix-self-test-01M689AC68MBBXGPGPK2ZH4CJ0.log](gate-fix-self-test-01M689AC68MBBXGPGPK2ZH4CJ0.log)


## Unmet Criteria

None in this fix round. Naming coverage remains **PARTIAL** without
SCOPE_FORBIDDEN_NAMES.

## Residual Risk

The real identifiers remain in the historical intermediate commit
`a5017576d3ad500f1fc59cbc6a97f18bb88fcbdd`. It was not rewritten, squashed,
deleted or cleaned; private refs remain. A corrected tip does **not** clean
history. PASS describes only tested predicates/inputs and the scoped
net-diff/message inspection, not universal privacy. No privacy-clean or
publication approval is claimed. LOCAL ONLY: no push/PR/merge/install/live.
