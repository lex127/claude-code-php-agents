# Claude Code PHP Agents

Best-practice Claude Code agent setup for PHP projects: WordPress (Bedrock and classic), Laravel, and smaller custom PHP codebases.

This repository gives you a practical starting point for:

- shared `AGENTS.md` rules for coding assistants
- Claude Code-specific `CLAUDE.md` delegation rules
- reusable Claude Code subagents in `.claude/agents/`
- model routing between Haiku, Sonnet, and Opus
- MCP safety rules
- WordPress/Bedrock and Laravel templates

The goal is not to make an autonomous developer. The goal is to make coding agents safer, cheaper, and more predictable.

## Prerequisites

- [Claude Code](https://code.claude.com) installed and authenticated (not just the Claude API)
- A PHP project with Git initialized
- Optional but recommended: Chrome DevTools MCP configured for the `browser-tester` agent (see [docs/mcp-setup.md](docs/mcp-setup.md))

Subagent model routing requires Claude Code. The `.claude/agents/` convention is a Claude Code feature, not the Claude API.

## Read the Full Guides

This repository is the copy-paste companion to two practical articles:

- [AI Agents in the Development Workflow](https://alexsinyaev.com/ai-agents-in-development-workflow/) — the safety model: permissions, MCP, browser verification, review gates, and what should stay human-owned.
- [Claude Code Subagents: Copy-Paste Agents for Safer, Cheaper Workflows](https://alexsinyaev.com/claude-code-subagents-token-optimization/) — the subagent setup: explorer, planner, reviewer, browser tester, model routing, and token discipline.

Start with the workflow article if you are defining what an agent should be allowed to do. Use this repository when you are ready to copy the files into a real PHP, WordPress, Bedrock, or Laravel project.

## Quick Start

**Framework-specific projects:** start from a template instead of the root files.

```bash
# WordPress Bedrock
cp -r templates/wordpress-bedrock/. /path/to/your-project/

# WordPress Classic
cp -r templates/wordpress-classic/. /path/to/your-project/

# Laravel
cp -r templates/laravel/. /path/to/your-project/

# Generic PHP
cp -r templates/generic-php/. /path/to/your-project/
```

Then copy the subagents:

```bash
mkdir -p /path/to/your-project/.claude/agents
cp .claude/agents/*.md /path/to/your-project/.claude/agents/
```

**Generic setup** (no specific framework):

```bash
cp AGENTS.md CLAUDE.md /path/to/your-project/
mkdir -p /path/to/your-project/.claude/agents
cp .claude/agents/*.md /path/to/your-project/.claude/agents/
```

After copying, open `AGENTS.md` and fill in the **Project Setup** and **Common Commands** sections with real commands for your project. Then open `CLAUDE.md` and update the local dev server URL.

## File Roles

Use `AGENTS.md` for rules that should apply across tools:

- Claude Code
- Codex
- Gemini CLI
- Cursor
- Copilot-style agents
- other coding assistants that read repository instructions

Use `CLAUDE.md` for Claude Code-specific behavior:

- subagent delegation
- `.claude/agents/` routing
- Opus/Sonnet/Haiku policy
- Claude Code MCP tool assumptions
- anti-loop and token discipline for subagents

Recommended setup:

```md
# CLAUDE.md
@AGENTS.md

## Claude Code: AI Delegation Rules

Claude-specific rules go here.
```

This keeps shared project policy portable while still giving Claude Code detailed subagent instructions.

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

Read more in [docs/safety-model.md](docs/safety-model.md), [docs/model-routing.md](docs/model-routing.md), and [docs/mcp-setup.md](docs/mcp-setup.md).

## Repository Philosophy

This is intentionally small. Copy the files, adapt them, and delete anything that does not match your project.

Good agent instructions are specific to the repository. These files are a starting point, not a universal policy.

## License

MIT
