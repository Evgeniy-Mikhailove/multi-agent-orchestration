# Example: 3-Agent Parallel Code Review

## The Task

Review a pull request with 15 changed files across 3 modules (auth, API, database).

## Sequential Approach (slow)

One agent reviews all 15 files. Takes ~3 minutes. Context gets heavy by file 10.

## Parallel Wave Approach (fast)

### Wave 1: Three reviewers in parallel

```
Agent A (auth-reviewer):
  Context: only auth/ files (5 files)
  Skill: security-review
  Model: Sonnet (standard engineering)
  Returns: { issues: [...], severity: "medium", summary: "..." }

Agent B (api-reviewer):
  Context: only api/ files (6 files)
  Skill: api-design-review
  Model: Sonnet
  Returns: { issues: [...], severity: "low", summary: "..." }

Agent C (db-reviewer):
  Context: only db/ files (4 files)
  Skill: database-review
  Model: Opus (security-sensitive)
  Returns: { issues: [...], severity: "high", summary: "..." }
```

All three run simultaneously. ~1 minute total (not 3).

### Wave 2: Synthesis

```
Main Agent:
  Input: conclusions from A + B + C
  Task: combine into unified review
  Output: PR comment with prioritized issues
```

## Result

| Metric | Sequential | Parallel |
|---|---|---|
| Time | ~3 min | ~1.5 min |
| Context per agent | 15 files | 4-6 files |
| Cost | 1x Opus for everything | 2x Sonnet + 1x Opus (cheaper total) |
| Accuracy | Degrades with context size | Each agent focused on its domain |
