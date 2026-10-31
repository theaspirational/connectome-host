TASK-XN4YJ — DRAFTING ONLY, two implementer briefs. PRIVATE. Operator Tarn; scope by qualifier Soma (Slack 1793441074). Not an owner decision.

FORBIDDEN: implementation, any source or test edit, any git write (no commit; leave the tracked tree clean), node/npm/bun/tsc/build/test/install, network, real adapter or API calls, reading credentials, writing outside $ORGASMIC_EVIDENCE_DIR. Allowed: read files and git objects, sha256sum the inputs, write drafts to $ORGASMIC_EVIDENCE_DIR/drafts/. Progress note every <=5 min to $ORGASMIC_EVIDENCE_DIR/progress.md; first draft checkpoint written within 20 minutes.

FROZEN SOURCE CONTRACTS (cite only via `git show <sha>:<path>`, verify that each sha exists in this repo; any that is missing means STOP and ask):
- T2 core (TASK-YXASE) head f378eadffc9a02e2ecb5a4f30e266db9321db4f5 (your worktree base). Its test/stub-only `compileDynamicSelectors(config, facts, parentAuthority)` is in test/stubs/f-host-t2/v1/dynamic-policy.ts (verify).
- H / F-host .4 compiler-core aecd4a8cafe470ad2bbe47b5087c1ec454eef62f; .3 c353e1c8d19a11697b5515bf3b03110de5dd85de (verify, and say whether each is an ancestor of f378eadf).
- T1 scope contract (KY73N) bff8c46e: verify its full sha and path, or mark UNVERIFIED.
- AF I7 a0970645d403a7a76449221fc36f240a7aa2464b is in a DIFFERENT repo (/home/orgasmic/src/agent-framework). Read it there only via `git -C /home/orgasmic/src/agent-framework show a0970645:<path>`, read-only. The AF registration-view contract drop is /srv/review-drop/af-a0970645-view/ (verify SUMS).
Documents (verify the sha256, then read in full):
- /srv/review-drop/tarn-rtaln-20261030/T2CORE-BRIEF-v4.3.md — 6482ad0684a699c4c227d6413e5aa46b20b6dd38970263804e68b9ec69e91730
- /srv/review-drop/tarn-rtaln-20261030/EM8Q2-NEXT-SOURCE-WORK-v1.md — e6990ae4ecfbce24cba296cd57a62e56da20b9d45536c0c0b5b31693152ac5a9
- /srv/review-drop/tarn-rtaln-20261030/EM8Q2-DAG-v2.md — 4139a6bd68b728046bc1c1b304e0020cf9e54f449a4493d5b85b7f92e9ad1cc6
- /srv/review-drop/TASK-YXASE-REVIEW1.md and TASK-YXASE-DOCS2.md, plus TASK-KY73N-T1.md and TASK-R5BF7-H-BRIEF.md (report their sha256)
- The YXASE erratum is task comment tx e9dfd0e3 on TASK-YXASE in this project: projector = 8 rows (7 parent-family + `dynamic-unknown-native-root`). Responsibility counts: compiler-core 21, cc+T4 9, projector 8, T4 10, I6 attestation 1. Verify these against the §3a allocation table in f378eadf; report any mismatch rather than reconciling it.

DELIVERABLES ($ORGASMIC_EVIDENCE_DIR/drafts/):
1. PROJECTOR-BRIEF-v1.md: an implementer brief for the projector.
   - Name all 8 rows with their exact source cites.
   - Inputs: which frozen interfaces it consumes, by exact type and file:line.
   - Outputs.
   - The truth table per row.
   - Tests and mutants as specs.
   - Acceptance.
   - Explicit non-goals: no issuance, no loader, no self-attestation, no new grants or wire authority, no real adapter calls, test/stub only unless a contract says otherwise.
2. ISSUANCE-BRIEF-v1.md: issuance/generation binding between H's opaque `VerifiedScope` (recipient-scope.ts) and I7's `VerifiedRegistrationScope` / `RegistrationRef` / `current` / `project`.
   - What binds the two today: per earlier analysis, NOTHING; verify this.
   - The minimal binding contract.
   - Who issues, and from which trusted fact.
   - Generation and epoch invalidation.
   - Refusal codes using only existing names, or proposals marked as NEW.
   - Tests as specs, acceptance, non-goals.
3. UNKNOWN-INPUTS.md: every native-truth or attestation input either brief needs that is NOT supplied by a frozen contract. Include: native root identity, parent authority provenance, the I6 attestation row, real adapter descriptors.
   - For each: who would have to supply it, and whether it is an owner item (6/7) or engineering.
   - Do NOT invent values.
4. SHA256SUMS.
Final report: deliverables with their sha256, every cite you couldn't verify (UNVERIFIED), and the blockers.