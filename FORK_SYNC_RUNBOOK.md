# Fork Sync Runbook

## Scope
This runbook defines how to resolve `sync-conflict` issues when weekly upstream merge automation cannot merge cleanly.

## One-Time GitHub Settings
1. Protect `main` and `upstream-main`.
2. Disable force pushes.
3. Require pull requests before merge to `main`.
4. Allow merge commits.
5. Set required checks on `main`:
   - `agents-ci`

## Manual Conflict Resolution
1. Create a conflict branch from `main`:
   - `git checkout main`
   - `git pull`
   - `git checkout -b sync/conflict-YYYYMMDD`
2. Merge the upstream mirror:
   - `git merge --no-ff upstream-main`
3. Resolve conflicts with this policy:
   - Prefer upstream behavior first.
   - Re-apply local extensions only where required.
4. Validate:
   - `npm ci`
   - `npm run build`
   - `npm test -- --testPathIgnorePatterns=title.memory-leak.test.ts --testPathIgnorePatterns=llm.spec.ts --testPathIgnorePatterns=integration.test.ts`
5. Open PR to `main` and reference the `sync-conflict` issue.
6. After merge, close the `sync-conflict` issue.

## Emergency Rollback
1. Find the backup tag created by sync automation, e.g. `backup/main-YYYYMMDD-HHMM`.
2. Revert the merge commit if needed:
   - `git revert -m 1 <merge_commit_sha>`
