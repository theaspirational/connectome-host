** Description
Rebase the WO-2 fix (#470: apply agent.retry to framework turn policy), commit cc0d1f7e47c438ff9cdb7542a43492ef68e42643 on branch wo2-agent-retry (old base 3e4cf9b3), onto current upstream main 8f2bb10353757921b8b9a49c570537a764bc68de (0.17.0; fork main was fast-forwarded to it). A plain rebase conflicts in README.md and src/recipe.ts.
** Acceptance
- One commit on 8f2bb103 with the same intent; conflicts resolved against upstream's current code (check whether upstream changed or partly fixed retry handling meanwhile, and say so).
- bun install --frozen-lockfile, bun test, typecheck green on base and result; the WO-2 tests still fail without the fix.
- Upstream-ready (no internal names); author Vasyl Dovhanchyn <48733736+theaspirational@users.noreply.github.com>. No push.

The worktree starts at cc0d1f7e; fetch/resolve upstream main 8f2bb103 (already present locally as upstream/main in the clone; fork origin/main equals it). Report the new sha, conflict resolutions, diff stat vs 8f2bb103, test counts base/final.