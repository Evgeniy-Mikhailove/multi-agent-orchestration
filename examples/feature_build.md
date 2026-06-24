# Example: Parallel Feature Build

## The Task

Build a user notification system: frontend component + backend API + database migration.

## Wave Execution Plan

### Wave 1: Independent builds (parallel)

```
Agent A (frontend-builder):
  Worktree: .worktrees/notif-frontend
  Branch: feature/notif-frontend
  Skill: senior-frontend
  Model: Sonnet
  Task: Build NotificationBell component + NotificationList page
  Delivers: React components, CSS, unit tests

Agent B (backend-builder):
  Worktree: .worktrees/notif-backend
  Branch: feature/notif-backend
  Skill: senior-backend
  Model: Sonnet
  Task: Build /api/notifications endpoints (CRUD + mark-read)
  Delivers: API routes, service layer, unit tests

Agent C (db-builder):
  Worktree: .worktrees/notif-db
  Branch: feature/notif-db
  Skill: database-review
  Model: Sonnet
  Task: Create notifications table migration + indexes
  Delivers: Migration file, seed data
```

### Wave 2: Integration (sequential - depends on Wave 1)

```
Agent D (integrator):
  Context: conclusions from A + B + C
  Task: Wire frontend to backend, verify migration applies cleanly
  Model: Sonnet
  Delivers: Integration test, wiring code
```

### Wave 3: Review gate (parallel)

```
Agent E (spec-check):
  Task: Verify all notification requirements are met
  Model: Haiku (fast classification)

Agent F (quality-check):
  Task: Review code quality, check for security issues
  Model: Opus (security-sensitive)
```

## Git Flow

```bash
# Wave 1 creates 3 worktrees
git worktree add .worktrees/notif-frontend feature/notif-frontend
git worktree add .worktrees/notif-backend feature/notif-backend
git worktree add .worktrees/notif-db feature/notif-db

# After Wave 1 completes
git checkout main
git merge feature/notif-db        # DB first (no dependencies)
git merge feature/notif-backend   # API second
git merge feature/notif-frontend  # Frontend last

# Cleanup
git worktree remove .worktrees/notif-frontend
git worktree remove .worktrees/notif-backend
git worktree remove .worktrees/notif-db
```

## Cost Breakdown

| Agent | Model | Tokens (est.) | Cost |
|---|---|---|---|
| Frontend builder | Sonnet | ~20K | ~$0.06 |
| Backend builder | Sonnet | ~25K | ~$0.08 |
| DB builder | Sonnet | ~10K | ~$0.03 |
| Integrator | Sonnet | ~15K | ~$0.05 |
| Spec check | Haiku | ~5K | ~$0.005 |
| Quality check | Opus | ~20K | ~$0.10 |
| **Total** | | | **~$0.33** |

Compare: one Opus agent doing everything sequentially would use ~95K tokens at $0.48, take 3x longer, and have degraded context quality by the end.
