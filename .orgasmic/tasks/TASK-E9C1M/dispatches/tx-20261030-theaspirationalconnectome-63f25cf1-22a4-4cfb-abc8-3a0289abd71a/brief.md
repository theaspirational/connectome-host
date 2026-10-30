F-host step 1: DESIGN DOC + stub contract only. No production code. Owner programme go `gen:exec`. Local commit on your branch; no push.

Base: connectome-host upstream main 2c39662289be4cd5a150222e8456ff5577ce7622 (your worktree starts there).
Framework side (read, don't edit): /home/orgasmic/src/agent-framework, at these commits:
- slice A, required inbound admission, at 1d990e4195e265f58be865719df2bb7e51f5c456
- slice B narrowed, in progress on branch task-hjv3j-r2: use its contract as specified: an optional sync `authorize(input, destination)` reply-authority hook; route resolution after A's verdict with its own timeout and a cap of 64; private-with-notice when there's no route; replyRouting dropped
- slice D, context-only delivery, at a5b02a07fd4c380ded751e01ada2ce3e6b242cca
- design doc docs/design/GENERIC-RECIPIENT-ROUTING.md at 3be0d77f.
Legacy oracle (read only): /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/src/{index.ts, framework-agent-config.ts, runtime-account-identity.ts, support-recipient.ts, host-recipient-protocol.ts}.
Inputs: /srv/review-drop/tarn-rtaln-20261030/TASK-EM8Q2-O4.md (the test list for F-host/F-Slack), TASK-Q2TVQ-MAPPING.md, TASK-KPY3G-SLACK-STATIC.md, TASK-EM8Q2-I3-I5.md.

Write `docs/design/generic-recipient-host.md` covering:
1. Host config surface (recipe/settings) mapping per connection to AF's `inboundAdmission`, the reply-authority hook, the `contextOnlyStorage` profile and grants, plus the bot/runtime identity. Defaults off; unconfigured hosts are unchanged.
2. The host's admission callback and reply-authority hook: what they check, using generic connection identity/grants only, with no connector names.
3. A legacy translator boundary for connectors that still speak `slackRecipientRouting` v1: either a narrow, time-limited compat adapter in the host, or none (if F-Slack lands first). Recommend one and give its retirement condition (slice G).
4. Store compatibility: (a) a SYNTHETIC old-reader/new-store test plan: old AF 0.22.2 + CM 0.14.1/Chronicle 0.4.0 (frozen fixture) opening a synthetic store written by new AF + CM 0.24/Chronicle 0.5.1. Measure refusal before any read or write and record any mutation. (b) Only if (a) shows no native barrier, a managed-launch compatibility fence in the host, with an explicit supported boundary and no claim to protect arbitrary SDK readers. No CM/Chronicle changes.
5. A shared synthetic connector stub contract (behaviours plus fixtures) covering the O4 F-host and F-Slack test lists, so F-host and F-Slack can be implemented in parallel.
6. A PR slicing plan for the host fork on current upstream, with estimates in hours.
No private data, no live recipes or env, no connector names in proposed host code (connector names may appear only in the translator section and in tests). Add a short test-fixture spec file if useful. Commit as `Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>` with the trailer `Co-Authored-By: GPT-6.1-Sol <noreply@openai.com>`.
Report: the doc path, head sha, and an open-questions list (split into owner decisions and engineering calls).