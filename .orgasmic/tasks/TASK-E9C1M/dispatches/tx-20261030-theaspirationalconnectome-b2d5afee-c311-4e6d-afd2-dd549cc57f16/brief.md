Your worktree is this work chain's shared tree, checked out detached at 9f153044ce63b8da719a0132c0817901f1031216 — the commit you review. Leave it exactly as you found it: no commits, no edits, no new files (ignored build output is fine). The next round of the chain runs here, and the daemon records anything you leave behind when your dispatch is closed.

Reviewed implementer evidence ($ORGASMIC_REVIEWED_EVIDENCE_DIR): /home/orgasmic/.orgasmic/runs/run-01M676Y8DFF5Q14C9VNXBEX5AP/evidence

Independent review of the F-host DESIGN (documentation only): docs/design/generic-recipient-host.md and docs/design/generic-recipient-fixtures.md at 9f153044ce63b8da719a0132c0817901f1031216, on connectome-host upstream 2c39662289be4cd5a150222e8456ff5577ce7622. The worker's report is at /srv/review-drop/TASK-E9C1M-DESIGN-REPORT.md.
Context inputs (read as needed): the framework programme design docs/design/GENERIC-RECIPIENT-ROUTING.md in /home/orgasmic/src/agent-framework at 3be0d77f; slice A at 1d990e41 (docs/required-inbound-admission.md); B narrowed at bf6e8e0e (docs/reply-routing.md); D at a5b02a07 (docs/context-only-delivery.md); /srv/review-drop/tarn-rtaln-20261030/{TASK-EM8Q2-O4.md, TASK-EM8Q2-I3-I5.md, TASK-KPY3G-SLACK-STATIC.md}; legacy host oracle /srv/ci-fixtures/pr361-v1-8153e7b84442cc11/host/src/.
Judge:
1. Is the host API correct and minimal against the ACTUAL A/B/D APIs (names, types, required vs optional)? Flag any API it invents that the framework doesn't have.
2. Does it preserve upstream behaviour for unconfigured hosts? Any operator-surface changes?
3. Does the stale-epoch loss gate hold up: connector outbox/ACK, commit only after host acceptance, known refusal vs uncertain ACK, no retro-replay, no exactly-once claims?
4. Reconnect turn rule: same frozen destination only, private notice otherwise.
5. Old-reader/new-store probe plan: is it really synthetic, does it measure refusal before any read/write, and does it record mutations? Is the conditional fence's boundary explicit and honest?
6. The no-translator recommendation: is the risk to the existing legacy Slack connector stated correctly, and what breaks if F-Slack is late?
7. Is the fixture contract (H01–H12, S01–S06) sufficient for F-host and F-Slack to proceed in parallel? What's missing compared with the O4 test lists?
8. PR slicing and hour estimates: plausible?
9. No private paths, hosts or names that shouldn't be public.
Verdict APPROVE or REJECT; findings HIGH/MEDIUM/LOW with file:line. Read-only.

## Gates already passed at this commit

orgasmic recorded these gate runs in the implementer's worktree, each on a clean tree it left clean, at 9f153044ce63b8da719a0132c0817901f1031216 — the commit your worktree is cut at — or at an ancestor of it whose gate inputs are identical to this commit's: the two trees differ only in paths this project marks as not gate inputs. Read the log instead of running the gate again. Run scoped probes and required gates that lack a reusable receipt. The manager names required gates; project policy also applies. Missing receipts do not assign a full suite.

- `docs-contract` — exit 0 — `python3 /home/orgasmic/.orgasmic/runs/run-01M676Y8DFF5Q14C9VNXBEX5AP/evidence/check-doc-contract.py` (cwd `.`) — at `9f153044ce63b8da719a0132c0817901f1031216` — log `/home/orgasmic/.orgasmic/runs/run-01M676Y8DFF5Q14C9VNXBEX5AP/evidence/gate-docs-contract-01M677M156AFHCX44AX5KRZWZB.log`
