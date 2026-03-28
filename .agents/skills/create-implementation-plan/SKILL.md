---
name: create-implementation-plan
description: |
  Generate a structured implementation plan from project requirements with
  decomposed tasks, milestones, dependencies, and acceptance criteria.
  Use when starting a new project to create docs/tasks/*.md files suitable
  for conversion to Linear issues.
---

# Create Implementation Plan Skill

## Purpose

Generate a structured implementation plan from project requirements, suitable for conversion to Linear issues and autonomous execution.

## When to Use

Use this skill when starting a new project after:
1. Brainstorming requirements and design with LLM
2. Having a rough PRD or product vision
3. Ready to decompose work into actionable tasks

## Input Required

Before running this skill, gather:
- Project name and description
- Key requirements and features
- Technical constraints and preferences
- Any existing design documents or PRDs

## Process

### Step 1: Gather Context

Collect all relevant context:
- Existing design documents
- Requirements from stakeholders
- Technical research findings
- Reference implementations or specs

### Step 2: Generate Architecture Documentation

Create the following files:

**docs/architecture.md** - System architecture:
- High-level architecture diagram (text-based)
- Component breakdown
- Data flow
- Integration points
- Technology choices and rationale

**docs/decisions/** - Architecture Decision Records (ADRs):
- One file per major decision
- Context, Decision, Consequences format

### Step 3: Generate Implementation Tasks

Create tasks in `docs/tasks/` directory with **one file per task**:

**File naming**: `NNN-brief-description.md` (e.g., `001-bootstrap-project.md`)

**Required schema**:

```markdown
---
title: Human-readable task title
milestone: M1  # M1, M2, M3, etc.
priority: 1    # 1=Urgent, 2=High, 3=Normal, 4=Low
estimate: 3    # Story points (optional)
blockedBy: []  # List of task IDs this depends on
blocks: []     # List of task IDs this blocks
parent: null   # Parent task ID for sub-issues
---

## Summary

One or two sentence description of what this task accomplishes.

## Scope

### In scope

- Specific item 1
- Specific item 2

### Out of scope

- Explicitly excluded item 1

## Deliverables

- File or artifact 1
- File or artifact 2

## Acceptance Criteria

- [ ] Criterion 1: measurable outcome
- [ ] Criterion 2: measurable outcome
- [ ] Criterion 3: measurable outcome

## Test Plan

- Test command or verification step 1
- Test command or verification step 2

## Notes

Any additional context, references, or gotchas.
```

### Step 4: Organize Milestones

Group tasks into logical milestones:

```markdown
## docs/tasks/milestones.md

# Project Milestones

## M1: Foundation

Goal: Establish project skeleton, tooling, and core infrastructure.

Tasks:
- 001-bootstrap-project
- 002-setup-testing
- 003-configure-ci

## M2: Core Features

Goal: Implement primary functionality.

Tasks:
- 010-implement-auth
- 011-implement-data-layer
- 012-implement-api

## M3: Integration

Goal: Connect components and add polish.

Tasks:
- 020-integrate-components
- 021-add-documentation
- 022-performance-optimization
```

### Step 5: Define Dependencies

Ensure the dependency graph is valid:
- No circular dependencies
- Foundation tasks have no dependencies
- Each milestone builds on previous
- Critical path is clear

### Step 6: Validate Completeness

Check that:
- Each task is independently implementable
- Acceptance criteria are testable
- Dependencies are correctly specified
- No duplicate tasks
- Coverage of all requirements

## Output Files

After running this skill:

```
docs/
├── architecture.md          # System architecture
├── decisions/               # ADRs
│   ├── 001-technology-choice.md
│   └── 002-data-model.md
└── tasks/
    ├── milestones.md        # Milestone overview
    ├── 001-bootstrap.md
    ├── 002-setup-testing.md
    └── ...                  # One file per task
```

## Example Prompt

When using this skill with an LLM:

```
PROJECT: [Your Project Name] - [Brief Description]

Based on the links I've provided, the attached findings, and your additional research, explore and design the best way for me to proceed with implementing this project.

Create a detailed comprehensive spec that I can pass along to coding agents to implement the [Project Name] project.

Output as several downloadable markdown files. Some files will be universal/persistent context across the implementation of all components, including things like cross-cutting concerns and architectural guidance. Target some of this for an AGENTS.md that can be injected as persistent context. Other parts of this can be in a README.md and possibly some more specific documentation files that can be referenced from README.md and AGENTS.md and stored separately in a docs/ directory.

The other necessary file is an implementation plan in the form of scoped and decomposed tasks. These should have a structured schema that can be easily converted into Linear issues, with relevant hierarchy (parent issues and sub-issues), dependencies, milestones, descriptions, acceptance criteria, test plan, and any other relevant metadata. Note that Linear doesn't use Epics, but we can track hierarchy/encapsulation via parent issue/sub-issue, and sequential phases of development via milestones.

All of this will be used to convert the tasks to Linear issues and then implemented via OpenSymphony where each issue is taken by a subagent with independent context. This requires referencing project-wide context within Linear as well as the code repository where relevant, and having all of the relevant information in the task that would enable its implementation (akin to Agile "Definition of Ready").
```

## Next Steps

After creating the implementation plan:

1. Review the generated tasks for accuracy
2. Run `opensymphony convert-tasks --project your-project-slug`
3. Verify issues in Linear
4. Begin execution with `opensymphony run`