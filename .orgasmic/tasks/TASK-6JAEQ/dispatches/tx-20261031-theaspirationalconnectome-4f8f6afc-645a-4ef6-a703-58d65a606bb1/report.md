# Review — TASK-6JAEQ, F-host.3 compiler-core scaffolding

Range `d7154d79..9c6f6ea1` (2 commits, 18 files, +1064/-6). Independent bounded review
against the frozen H brief, the T1 contract, and the operator boundaries from
ask-ee068755 / ask-9795ca2a plus the Soma qualifier.

## Verdict

**APPROVE** split-acceptance part **(a) compiler-core scaffolding**. No HIGH findings.
Part **(b) runtime-binding** is correctly left PENDING and must not be read as accepted.

All nine assigned checks pass. The stub↔source comparison is byte-exact, fail-closed
coverage is broad and survives adversarial probing, profile-off is byte-identical and
structurally (not just empirically) guaranteed, and the docs/changelog claim no finished
H.3 and no deployability. One substantive design finding (MEDIUM) concerns a lane that
escapes the compiled ceilings the report invites T2 to build on.

## Findings

### MEDIUM — `src/recipient-admission.ts:112`: the `inference/request` lane consults none of the compiled ceilings

`admit` returns `{ store: true, mayWake: true }` unconditionally for `inference/request`,
bypassing `policy.read`, `policy.wake`, `trusted.lookup`/`trusted.readable`,
`wakeWithoutChannel`, and the `chat:addressed` tag requirement. Reproduced on the real
production function with a policy whose ceilings deny everything
(`read: () => false`, `wake: () => false`, `wakeWithoutChannel: false`) and a `trusted`
seam that returns no scope:

```
inference/request  => {"store":true,"mayWake":true}
push/event (no ch) => {"store":true,"mayWake":false}
```

Channel-less `push/event` correctly requires `wakeWithoutChannel && addressed`
(line 124); the equally channel-less `inference/request` requires nothing. The behaviour
is deliberate and asserted (`test/recipient-policy.test.ts:211`) and mentioned in one
clause of `docs/design/recipient-policy-staging.md:48` ("Channel-less push and inference
have separate lane rules and no destination"), so this is not an undetected bug. It is a
contract-clarity defect in the seam being handed over: `docs/design/recipient-policy-staging.md:45`
advertises "separate host-compiled read/wake ceiling functions", and the report offers
`CompiledRecipientAdmissionPolicy` to T2 as the ceiling interface. A T2 author compiling
selectors against that interface would reasonably believe `read`/`wake`/`wakeWithoutChannel`
bound every lane; for this lane they bound nothing. No mutant covers it (mutating the
lane's ceiling use is impossible — there is none to remove).

Impact is confined today: the lane must be explicitly listed in `policy.lanes`, and the
whole compiler is unreachable in production because every configured `recipientPolicy`
fails closed. The risk is inherited at T2 integration, not now.

*Fix direction:* either route the lane through `wakeWithoutChannel && addressed` for
symmetry with channel-less push, or state explicitly in the interface doc comment on
`CompiledRecipientAdmissionPolicy` (`src/recipient-admission.ts:16-24`) that `read`/`wake`/
`wakeWithoutChannel` do not apply to `inference/request`, and add a mutant/assertion
pinning that exemption so a later ceiling change cannot silently widen it.

### LOW — `src/mcpl-config.ts:344-345`: a tombstone overlay carrying `recipientPolicy` is silently dropped, not loudly refused

`readOverlayEntry` returns at line 344 on `disabled === true`, before the
`recipient_policy_operator_only` check at line 345. Probed:

```
TOLERATED  TOMBSTONE carrying policy {disabled:true,recipientPolicy}
  -> tombstone reading: {"entry":{"disabled":true},"notes":[]}
TOLERATED  tombstone replaces protected server     (composeOverlay)
```

No policy is installed, so this is fail-safe, not fail-open — the field is discarded.
But the operator boundary is "any configured production `recipientPolicy` → loud
fail-closed", and `docs/design/recipient-policy-staging.md:10` states "Any configured
`recipientPolicy` ... refuses". This corner silently ignores operator intent instead.
*Fix direction:* move the `Object.hasOwn(e, 'recipientPolicy')` check above the
`disabled === true` early return.

### LOW — `src/mcpl-config.ts:265`: `readAgentOverlayFile` bypasses the source-text scanner

Agent-overlay files are parsed with plain `parseHandWrittenJson`, not
`parseRecipientConfigJson`. A `recipientPolicy` hidden behind a duplicate `mcplServers`
map or duplicate entry id in an overlay is therefore discarded by `JSON.parse`
last-wins rather than refused. Safe in effect (overlays can never install a policy —
`readOverlayEntry` refuses the visible case), but it is the one remaining loader where a
hidden policy is dropped silently. The operator servers file and both recipe paths are
covered. *Fix direction:* use the scanner here too, for uniformity of the loud refusal.

### LOW — `test/stubs/f-host-3/v1`: no compiler-control analogue pins the v1 opacity diagnostics

`test/stubs/f-host-2a/v0` has `check-compiler-controls.mjs`, which removes each
`@ts-expect-error` and pins the resulting diagnostic code (including an isolated TS2542).
The new v1 stub's two directives (`typecheck.ts:13,15`) have no such control, so the gate
proves only that *some* error occurs. I pinned them myself (see Verification Gates); they
are load-bearing and correct, but nothing in-tree prevents a future refactor from
satisfying them with an unrelated error.

## Verification Gates

Reused the implementer's receipts for the 18 recorded gates at `9c6f6ea1`. Independently
reproduced or newly ran the following, all bounded
`timeout --signal=TERM --kill-after=60s 900s`, on the existing installed head/base roots
(`tmp-recipient/build/{head,base}`, ignored build output) with no new install:

| Check | Command / probe | Result |
| --- | --- | --- |
| (1) Contract integrity | `sha256sum -c SHA256SUMS` in `/srv/review-drop/af-a869bb3a-contract/` | 5/5 OK |
| (1) STUB↔SOURCE | concatenated the 7 cited source ranges and `diff`ed against the stub body | **IDENTICAL — zero drift**; all citations exact |
| Report identity | `sha256sum /srv/review-drop/TASK-6JAEQ-FHOST3.md` | `aa7b8deb…b42b0c`, matches brief |
| Head-root fidelity | 8 changed `src/` files vs `git show HEAD:` | byte-identical |
| (2) Fail-closed coverage | scanner probe, 11 cases | 6/6 hidden-policy cases **REFUSED** (plain, duplicate map, duplicate id, null policy, `r`-escaped key, BOM); 5/5 benign cases **TOLERATED** |
| (2) Mutants (reproduced) | own runner, `fail-closed removed` + `protected replacement sheds policy` | **2/2 KILLED**, named discriminating witnesses, sources restored |
| (2) Focused suite | `bun test recipient-policy / recipe-unknown-keys / mcpl-agent-overlay` | **90 pass, 0 fail** (matches claim) |
| (3) Overlay contract change | read + probe of `composeOverlay` / `applyRecipeServerOverrides` / `mergeRecipeServers` | **no change for policy-free configs** — `protectedIds` empty, new `readOverlayEntry(entry)` call throws only on `recipientPolicy`; drop-with-note retained |
| (3) Write paths | all 6 `saveMcplServers` callers (`commands.ts:1493,1517,1546`, `web-ui-module.ts:1806,1823,1842`) | every one reads via scanner-guarded `readMcplServersFile` first → fail-closed before write; no silent policy drop |
| (4) Presentation feature-off | sole production caller `src/index.ts:499` passes no `recipientAccounts` → `[]` → `appendRecipientAccounts` returns `normalized` at line 51 **before** any validation | prompt bytes structurally unchanged |
| (5) VerifiedScope opacity | removed the v1 directives and ran `tsc` | forged literal → **TS2353**; field read → **TS2339**; bare `{}` → **TS2741**. Opaque, no production constructor |
| (5) Reply authority | `policy.automaticReplies !== false` → `recipient_reply_authority_pending`; no `replyIntent` returned on any path | positive auto-reply refuses |
| (6) Feature-off byte-identity | reran the probe on base and head, `cmp` | **BYTE-IDENTICAL**, sha256 `685a9496…2154ec` — matches the implementer's claim exactly |
| (6) Changed fetch path (probe gap) | own base/head probe of `loadRecipeDetailed` over HTTP: plain, BOM, invalid, unknown-keys | **identical** (only the ephemeral port differs) — the `res.json()` → `parseRecipientConfigJson` substitution is behaviour-preserving |
| (7) Docs/changelog honesty | read both in full | no finished-H.3 or deployability claim; split acceptance, INPUT GAP, no-cycle argument and LOCAL ONLY all present |
| (8) Closure honesty | `sha256sum` of bun archive vs official `SHASUMS256.txt`; TS version in both roots | archive `3e730528…` **matches official checksum**; bun 1.4.3; TS **7.0.2** in base and head |
| (8) No borrowing | every symlink under both build roots resolved | **no symlink escapes either root** |
| (9) Privacy | internal-name grep over the changed files and the whole tracked tree at HEAD | **clean, no hits** |

Scope-only static schema: `readChannels`/`wakeChannels`/`replyChannels` appear in **no
`src/` file** — only in design docs, the pre-existing fixture, the test, and
`test/stubs/f-host-3/v1/static-policy.ts`, which is labelled "Superseded by T1 (scope
contract §1:72)". `src/index.ts` exports nothing recipient-related, so no AF-scope or wire
surface leaked.

I left the tracked worktree clean at `9c6f6ea1` and restored every file I touched in the
ignored build roots (verified by hash); no stray artifacts remain. My probes and logs are
in this evidence directory (`reviewer-mutants.mjs`, `reviewer-scanner-probe.ts`,
`reviewer-inference-probe.ts`, `reviewer-fetchpath-probe.ts`, `rv-off-*.json`, `rv-fetch-*.json`).

## Unmet Criteria / test gaps

The report's own "Unmet Criteria" list (6 items) is accurate and I found nothing it
overstates. Additional gaps I identified:

1. **The protected-overlay guards have no production-path coverage.** `recipient_policy_protected_binding_changed`,
   `recipient_policy_ambiguous_ownership` and `recipient_policy_protected_replacement` are
   reachable only by direct unit calls from `test/recipient-policy.test.ts`. In production
   `loadMcplServers` and `mergeRecipeServers` refuse any policy first, so no protected
   definition can ever reach them. This is inherent to interim fail-closed, not a defect —
   but these are precisely the guards that must hold when fail-closed is lifted, and
   nothing yet proves they are wired into the path that will then exist.
2. **No mutant or assertion pins the `inference/request` ceiling exemption** (MEDIUM above).
3. **The feature-off probe does not cover the changed recipe loaders.** It exercises
   `validateRecipe`/`mergeRecipeServers`/`loadMcplServers`/`composeOverlay`/`buildFrameworkAgentConfig`,
   but not `loadRecipeDetailed`/`loadSavedRecipe`, where the parser substitution happened.
   I closed this gap with my own base/head probe (identical); it is not in-tree.
4. **v1 opacity diagnostics unpinned** (LOW above).
5. `--docs-contract` is PARTIAL by the implementer's own statement and does not cover the
   new document; the private naming policy was never supplied, so my privacy result is a
   targeted grep, not a qualification.

## Residual Risk

- **`VerifiedScope` opacity is compile-time only.** `JSON.parse('{}')` typed `any`, or any
  cast, yields a usable value — the test double itself does this
  (`Object.freeze({}) as VerifiedScope`). This is the documented "trusted seam", not a
  defect, but it means no runtime guarantee exists until the reviewed scope integration
  lands with a real verifier.
- **The whole compiler is unreachable in production.** Everything accepted under (a) is
  dead code behind the fail-closed gate. Acceptance rests on unit/stub evidence plus the
  stub↔source pin, not on any executed integration — exactly as the report states.
- **A configured policy now blocks unrelated flows.** `mergeRecipeServers` is also called
  from `src/bug-report.ts:346`, so a config carrying a `recipientPolicy` cannot produce a
  bug report. Consistent with loud fail-closed, but worth knowing operationally.
- **Private ancestry retained; LOCAL ONLY.** I confirmed no push and no main-checkout
  edits. Scrubbed additions do not clean history; public submission still needs a fresh
  tree on a public base plus a whole-range privacy review.
- Installed AF remains **0.30.0**, not INT-1, so no callback shape is proven against a
  composed runtime release — only against the pinned declarations, which I verified.

## Fix Directions

1. Resolve the `inference/request` ceiling asymmetry — either apply
   `wakeWithoutChannel && addressed`, or document the exemption on the interface and pin it
   with a mutant before T2 builds against this seam. (MEDIUM, do before handing (a) to T2.)
2. Move the `recipientPolicy` check above the `disabled === true` return in
   `readOverlayEntry` so tombstone-carried policy refuses loudly. (LOW, one line.)
3. Use `parseRecipientConfigJson` in `readAgentOverlayFile` for a uniform loud refusal. (LOW.)
4. Add a v1 compiler-control mirroring the v0 harness to pin TS2353/TS2339/TS2741. (LOW.)
5. Fold a `loadRecipeDetailed`/`loadSavedRecipe` case into the in-tree feature-off probe so
   the changed parse path is covered by the committed gate, not only by this review.
