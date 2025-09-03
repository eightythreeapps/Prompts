## Summary
<!-- What does this change do? Keep it short. -->

## Why
<!-- Problem statement; user impact; link to PRD/issue. -->

## Scope / Areas Touched
- [ ] api
- [ ] web
- [ ] desktop
- [ ] shared-types
- [ ] data-access
- [ ] config/devops
- [ ] db (migrations included)

## Screenshots / Demos
<!-- CLI output, UI screenshots/GIFs, curl examples. -->

## Testing Notes
- [ ] Unit tests added/updated
- [ ] Integration/e2e relevant and passing
- [ ] Manual test steps included below

**Manual test steps**
1.
2.

## Security & IG Checklist
- [ ] No secrets committed (checked)
- [ ] Auth paths, roles verified
- [ ] PII handled per policy (none added / documented)
- [ ] CORS/Headers validated (api changes)
- [ ] DB migrations reviewed & reversible

## Performance
- [ ] Avoids N+1 queries
- [ ] Bundle size impact noted (web)
- [ ] Uses pagination/limits where relevant

## Backwards Compatibility
- [ ] No breaking API changes
- [ ] If breaking, migration steps included

## Links
- Issue:
- ADR:
- Session doc:


# Source Control Policy (Git/GitHub)

## Default Merge Strategy
	•	Use “Squash and merge” on all PRs to keep a linear, readable history.
	•	Rebase locally as you like, but never force-push to main.
	•	Protected branches: main, release/*. Require:
	•	Status checks: build, lint, test
	•	1+ approving review
	•	Up-to-date with base (or enable “auto-merge” after checks pass)

## Branch Naming
feature/<scope>-<short-description>         e.g., feature/web-auth-flow
fix/<scope>-<issue|bugid>-<short-description>  e.g., fix/api-1234-bad-paging
chore/<scope>-<what>                        e.g., chore/repo-dependabot
docs/<scope>-<what>                         e.g., docs/api-v1-contracts
hotfix/<scope>-<what>                       e.g., hotfix/api-null-pointer


## Commit Style (Conventional Commits, pragmatic)
<type>(<scope>): <short summary>

[body - why, not just what]
[breaking-change footer or issue refs]

Allowed <type>: feat, fix, chore, docs, refactor, test, perf, build, ci, revert

### Examples
feat(api): add /sessions endpoint with pagination

Fixes #231. Adds Service and controller; returns ApiResponse<T>.

fix(web): handle 401 by clearing token and redirecting to /login

Prevents confusing infinite reload loop.

refactor(shared-types): split Media types into separate module

BREAKING CHANGE: MediaItem renamed to LibraryItem and fields normalized.

```bash
# <type>(<scope>): <subject in imperative mood, ≤72 chars>
# Types: feat|fix|chore|docs|refactor|test|perf|build|ci|revert
# Scope: api|web|desktop|shared-types|data-access|config|devops|db|docs
```

## Commit Message Template (.gitmessage.txt)
```bash
<type>(<scope>): <subject>

# Why
# - What drove this change?
# - Alternative options considered?

# What changed
# - Major codepaths touched, flags, env, migrations?

# Risk & Rollback
# - Known risks and a quick rollback step

# Links
# - Closes #ISSUE_ID
# - PRD/ADR/Session doc URLs

# BREAKING CHANGE:
# - Description of breaking behavior and migration steps
```