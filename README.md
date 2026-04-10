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

### 3. Configure `config.yaml`

The template includes a root-level `config.yaml` that `opensymphony run` reads by default.

Update it for your machine and deployment mode:

```yaml
control_plane:
  bind: 127.0.0.1:2468

openhands:
  tool_dir: ../../tools/openhands-server
```

- Keep the control-plane port on `2468` unless you have a reason to change it
- If you run against an external OpenHands agent-server, you can omit `openhands.tool_dir`
- Adjust relative paths so they resolve correctly from your target repo root

### 4. Set Up Linear

1. Create a Linear project for your repository
2. Note the project slug from the project URL (e.g., `my-project-abc123`)
3. Set `LINEAR_API_KEY` environment variable

### 5. Set Up GitHub

1. Create the required labels:
   - `symphony` - Applied to all PRs created by OpenSymphony
   - `review-this` - Triggers automated AI PR review
   
   Go to **Issues → Labels → New label** for each.

2. Set `GITHUB_TOKEN` environment variable with repo access

3. Configure repository secrets for AI PR Review (**Settings → Secrets and variables → Actions**):

   | Secret | Required | Description |
   |--------|----------|-------------|
   | `LLM_API_KEY` | Yes | API key for your LLM provider |
   | `GITHUB_TOKEN` | Auto | Provided automatically by GitHub Actions |

   > **Note**: For repositories that need to post review comments from a bot account, use `ALLHANDS_BOT_GITHUB_PAT` instead of the default `GITHUB_TOKEN`.

   See [OpenHands PR Review Plugin](https://github.com/OpenHands/extensions/tree/main/plugins/pr-review) for full documentation.

### 6. Generate Implementation Plan

Create a detailed implementation plan with structured tasks:

Use the repo's `create-implementation-plan` skill with your coding agent to
generate the initial planning docs and `docs/tasks/*.md` files. The skill
definition lives at `.agents/skills/create-implementation-plan/SKILL.md`.

OpenSymphony does not currently provide a built-in `opensymphony plan`
subcommand.

Or manually create `docs/tasks/` with one markdown file per task containing:
- Summary
- Scope (in/out)
- Deliverables
- Acceptance Criteria
- Test Plan
- Dependencies (blockedBy, blocks, parent)

### 7. Convert Tasks to Linear Issues

Use the repo's `convert-tasks-to-linear` skill with your coding agent to turn
`docs/tasks/` into Linear parent issues, sub-issues, and blocker
relationships. The skill definition lives at
`.agents/skills/convert-tasks-to-linear/SKILL.md`.

OpenSymphony does not currently provide a built-in
`opensymphony convert-tasks` subcommand.

### 8. Run OpenSymphony

```bash
# From the target repo root, run the orchestrator
opensymphony run

# Or point at an explicit runtime config
opensymphony run --config ./config.yaml

# Reattach to a managed issue conversation for debugging
opensymphony debug YOUR-ISSUE-ID
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
├── config.yaml               # Runtime config for `opensymphony run`
└── README.md                 # This file
```

## Workflow Process

1. **Brainstorm** - Design your project with LLM assistance (future tooling)
2. **Plan** - Use the `create-implementation-plan` skill, or write the docs/tasks manually
3. **Convert** - Use the `convert-tasks-to-linear` skill, or create the Linear issues manually
4. **Configure** - Update `WORKFLOW.md` and `config.yaml` with project details
5. **Execute** - Run OpenSymphony to implement issues autonomously

## Key Files to Customize

### WORKFLOW.md

The main orchestration configuration. Key sections:

- `tracker.project_slug` - Your Linear project
- `hooks.after_create` - How to clone your repo
- `openhands.conversation.agent.llm.model` - LLM model selection

### config.yaml

Runtime config for `opensymphony run`.

- `control_plane.bind` - Local control-plane HTTP/SSE address for the TUI
- `openhands.tool_dir` - Path to the local OpenHands agent-server checkout when running in supervised local mode

### AGENTS.md

Project-specific context for agents. Include:

- Coding conventions
- Architecture decisions
- Testing requirements
- File organization

### .agents/skills/

Repo-owned operational instructions for the coding agent. These are not
OpenSymphony CLI subcommands. Review and customize them for your project,
especially any validation commands, PR-body checks, or workflow assumptions.

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

These skills are intended to be used by the coding agent working in the target
repo. They are not commands implemented by the `opensymphony` CLI.

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
- GitHub authentication available to `gh` CLI
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
