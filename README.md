# Claude Code PHP Agents

Best-practice Claude Code agent setup for PHP projects: WordPress, Bedrock, Laravel, Symfony-style applications, and smaller custom PHP codebases.

This repository gives you a practical starting point for:

- `AGENTS.md` project rules
- `CLAUDE.md` project memory for Claude Code
- reusable Claude Code subagents in `.claude/agents/`
- model routing between Haiku, Sonnet, and Opus
- MCP safety rules
- WordPress/Bedrock and Laravel templates

The goal is not to make an autonomous developer. The goal is to make coding agents safer, cheaper, and more predictable.

## Quick Start

Copy the base files into your project:

```bash
cp AGENTS.md CLAUDE.md /path/to/your-project/
mkdir -p /path/to/your-project/.claude/agents
cp .claude/agents/*.md /path/to/your-project/.claude/agents/
```

Then edit `AGENTS.md` for your project:

- real commands for test, lint, build, and local dev
- framework versions
- deployment rules
- folders the agent must not edit
- MCP tools available in your environment
- manual steps that must stay human-owned

For framework-specific examples, start from one of these templates:

```text
templates/generic-php/
templates/wordpress-bedrock/
templates/wordpress-classic/
templates/laravel/
```

## Recommended Claude Code Setup

Use one source of truth for project instructions:

```md
# CLAUDE.md
@AGENTS.md
```

Keep the durable rules in `AGENTS.md`. Keep tool-specific wrappers thin.

## Included Subagents

```text
.claude/agents/
  explorer.md        # Haiku, read-only codebase discovery
  planner.md         # Opus, implementation planning without edits
  reviewer.md        # Opus, diff-first code review
  browser-tester.md  # Haiku, browser regression checks via MCP
  docs-writer.md     # Haiku, documentation from verified source material
```

## Model Routing Pattern

| Model | Use for | Avoid |
| --- | --- | --- |
| Haiku | discovery, docs, log summaries, browser smoke checks | architecture, security-sensitive logic |
| Sonnet | normal implementation, tests, bug fixes | purely mechanical work or deep architecture |
| Opus | planning, high-risk review, unfamiliar legacy code | broad exploration, raw logs, formatting |

## Safety Rules

- Agents do not get production credentials.
- Read-only agents stay read-only.
- Planner and reviewer agents do not edit code.
- The developer owns commits, pushes, deploys, database writes, and destructive commands.
- MCP servers are treated like dependencies with credentials.
- Raw logs and huge outputs are summarized before reaching expensive models.

Read more in [docs/safety-model.md](docs/safety-model.md) and [docs/model-routing.md](docs/model-routing.md).

## Repository Philosophy

This is intentionally small. Copy the files, adapt them, and delete anything that does not match your project.

Good agent instructions are specific to the repository. These files are a starting point, not a universal policy.
