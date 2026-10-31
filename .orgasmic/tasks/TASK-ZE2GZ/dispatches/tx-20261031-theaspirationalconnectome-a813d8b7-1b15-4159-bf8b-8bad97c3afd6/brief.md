TASK-ZE2GZ (A2): bounded PLAN-ONLY round. The subject is the exact composed AF dependency/artifact plan for connectome-host. Operator Tarn; scope from qualifier Soma (Slack 1793445107, 1793445209). Not an owner decision.

FORBIDDEN
- Any manifest or lock edit; install, fetch, network.
- Package build, pack or publication; runtime adoption.
- node/npm/bun/tsc execution, tests.
- Git writes or commits. A finalize prompt is not an instruction. Use GIT_OPTIONAL_LOCKS=0.
- Writing outside $ORGASMIC_EVIDENCE_DIR.
Progress goes ONLY to $ORGASMIC_EVIDENCE_DIR/progress.log, every 5 min at most. No `dispatch ask` except for a genuine STOP. Do not produce an owner or preference menu.

FIRST progress.log line: HEAD (must be f378eadffc9a02e2ecb5a4f30e266db9321db4f5), plus `git -C /home/orgasmic/src/agent-framework rev-parse` of a0970645 and f8af03a8 and of tag v0.30.0, plus the sha256 of /srv/review-drop/tarn-rtaln-20261030/XN4YJ-DRAFTS-v3-REVIEW1.md (057a7fafd0f330a60758d9924bc69599c1cd00c27ce083f056e36d6f829f40f0). Read REVIEW1's H1 and its A1/A2/A3 sections in full.

WRITE to $ORGASMIC_EVIDENCE_DIR/a2-v1/: A2-PLAN.md, FACTS.md and SHA256SUMS.

FACTS.md (read-only, every fact cited as pin:path:line):
- The host's package.json AF dependency and range semantics. The host lockfile, if one exists: its AF entry, resolved, integrity and lockfileVersion. Whether a root lockfile exists at all.
- The AF package.json at both pins: name, version, exports, types/main, files, scripts (prepare/prepack/build), and dist tracked or not. At v0.30.0: the registration surface is absent.
- Host files importing AF, which AF symbols they import, and the 0.30-era symbols that must keep resolving after the move.
- Other packages in the host closure that depend on AF (peer, nested), which could create a duplicate-AF or type-identity split. Determine this from the lock and manifests only.
- Mark every unbuilt artifact, declaration and integrity slot as UNRESOLVED. Never invent a digest, tarball name or URL.

A2-PLAN.md:
1. Identity: what identifies "our composed AF payload". Source pin, plus build recipe and toolchain, plus artifact digest, plus a declaration-closure digest. State why the version 0.31.0 alone is not an identity.
2. Compare the portable consumption options. Each must be portable: no private absolute paths and no registry assumption. Candidates:
   - a vendored, integrity-pinned tarball in a release set
   - file:/link: to a release-set artifact
   - workspace or overlay forms
   - git dependency by commit
   For each option, give: integrity binding, lock representation, reproducibility, declaration resolution, type-identity/duplicate risk, how it interacts with the release-set and installer, and failure modes. EXCLUDE post-build source overlays. Name a recommended option with its reasons. That is an engineering recommendation, not a menu.
3. Manifest and lock strategy for the host: the exact range or spec, how the lock records it, and how to verify the resolved artifact digest at install. Write this as a plan only.
4. Ordered host prerequisites, each with entry and exit evidence:
   A1 composed artifact plus declarations → A2 manifest/lock change scope (separate, later go) → A3 harness → H implementation assignment, with the SC1–SC6 (a)-semantics parts settled first.
   State explicitly that independent AF build/types work which doesn't consume the host manifest is NOT blocked.
5. Risks, UNVERIFIED items, and what this plan does NOT authorise.

Final report: hashes, the recommended option, the UNRESOLVED slots and the ordered prerequisites. No readiness claim.