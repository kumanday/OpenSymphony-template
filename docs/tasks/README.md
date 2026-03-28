# Implementation Tasks

This directory contains implementation tasks, one file per task.

## Structure

Each task file follows this schema:

```markdown
---
title: Task title
milestone: M1
priority: 1
estimate: 3
blockedBy: []
blocks: []
parent: null
---

## Summary
Brief description

## Scope
### In scope
- Item

### Out of scope
- Item

## Deliverables
- File

## Acceptance Criteria
- [ ] Criterion

## Test Plan
- Command
```

## File Naming

Use `NNN-brief-description.md` format:
- `001-bootstrap-project.md`
- `002-setup-testing.md`
- `003-configure-ci.md`

## Next Steps

1. Create task files in this directory
2. Run `opensymphony convert-tasks --project your-project-slug`
3. Verify issues appear in Linear