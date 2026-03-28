# OpenSymphony Template

A GitHub template repository for projects using OpenSymphony for autonomous agent-driven development.

## What is OpenSymphony?

OpenSymphony is an autonomous orchestration system that runs coding agents (via OpenHands) to implement Linear issues end-to-end. Each issue gets its own workspace, and agents follow a structured workflow to plan, implement, validate, and submit PRs.

## Quick Start

### 1. Create Your Repository

Click **"Use this template"** at the top of this page to create a new repository from this template.

### 2. Configure WORKFLOW.md

Update the following placeholders in `WORKFLOW.md`:

```yaml
project_slug: "YOUR-PROJECT-SLUG"  # From Linear project URL
```

```yaml
after_create: |
  git clone --depth 1 https://github.com/YOUR-ORG/YOUR-REPO.git .
```

### 3. Set Up Linear

1. Create a Linear project for your repository
2. Note the project slug from the project URL (e.g., `my-project-abc123`)
3. Set `LINEAR_API_KEY` environment variable

### 4. Set Up GitHub

1. Ensure your repository has the `symphony` label (create it if needed)
2. Set `GITHUB_TOKEN` environment variable with repo access

### 5. Generate Implementation Plan

Create a detailed implementation plan with structured tasks:

```bash
# Use the create-implementation-plan skill
opensymphony plan --skill create-implementation-plan
```

Or manually create `docs/tasks/` with one markdown file per task containing:
- Summary
- Scope (in/out)
- Deliverables
- Acceptance Criteria
- Test Plan
- Dependencies (blockedBy, blocks, parent)

### 6. Convert Tasks to Linear Issues

```bash
opensymphony convert-tasks --project your-project-slug
```

### 7. Run OpenSymphony

```bash
# Run the orchestrator
opensymphony run

# Or with a specific issue
opensymphony run --issue YOUR-ISSUE-ID
```

## Directory Structure

```
.
├── .agents/
│   └── skills/
│       ├── commit/           # Commit formatting skill
│       ├── convert-tasks-to-linear/  # Task → Linear conversion
│       ├── create-implementation-plan/  # Generate implementation plan
│       ├── land/             # PR merge workflow
│       ├── linear/           # Linear GraphQL operations
│       ├── pull/             # Branch sync workflow
│       └── push/             # PR creation workflow
├── .github/
│   ├── CODEOWNERS            # Review ownership
│   ├── pull_request_template.md
│   └── workflows/
│       └── ai-pr-review.yml  # Automated PR review
├── docs/
│   └── tasks/                # Implementation task files
├── AGENTS.md                 # Agent context and conventions
├── WORKFLOW.md               # Orchestration configuration
└── README.md                 # This file
```

## Workflow Process

1. **Brainstorm** - Design your project with LLM assistance (future tooling)
2. **Plan** - Generate PRD, architecture, and implementation plan
3. **Convert** - Transform tasks into Linear issues with dependencies
4. **Configure** - Update WORKFLOW.md with project details
5. **Execute** - Run OpenSymphony to implement issues autonomously

## Key Files to Customize

### WORKFLOW.md

The main orchestration configuration. Key sections:

- `tracker.project_slug` - Your Linear project
- `hooks.after_create` - How to clone your repo
- `openhands.agent.llm.model` - LLM model selection

### AGENTS.md

Project-specific context for agents. Include:

- Coding conventions
- Architecture decisions
- Testing requirements
- File organization

### docs/tasks/

Implementation tasks in markdown format. Each task should have:

```markdown
---
title: Task Title
milestone: M1
priority: 1
blockedBy: [PARENT-ISSUE-ID]
---

## Summary
Brief description

## Scope
### In scope
- Item 1

### Out of scope
- Item 2

## Deliverables
- File 1
- File 2

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Test Plan
- Test command 1
```

## Skills

| Skill | Purpose |
|-------|---------|
| `create-implementation-plan` | Generate structured task breakdown |
| `convert-tasks-to-linear` | Create Linear issues from markdown tasks |
| `commit` | Format commits with proper messages |
| `push` | Create/update PRs |
| `pull` | Sync branch with origin/main |
| `land` | Merge PR after approval |
| `linear` | Direct Linear GraphQL operations |

## Requirements

- OpenSymphony CLI installed
- Linear API key
- GitHub personal access token
- OpenHands agent-server running locally or remotely
- LLM API key (model specified in WORKFLOW.md)

## Troubleshooting

### Agent can't find Linear tools

Ensure `opensymphony linear-mcp` is available or configure MCP stdio server in WORKFLOW.md.

### PR not linked to Linear issue

The agent uses `linear_save_issue(links=[...])` to attach PRs. Check that the Linear MCP has write permissions.

### Workpad comment not updating

Only one active workpad comment is used per issue. Check for resolved comments that might be interfering.

## License

MIT