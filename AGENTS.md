# Repository Guidelines for AI Coding Agents

This file is the source of truth for Claude Code, Codex, Gemini CLI, Cursor agents, and other coding assistants that support repository instructions.

## Operating Model

Treat the agent as a fast assistant, not an autonomous engineer.

The agent may help with:

- codebase discovery
- implementation plans
- narrow code changes
- tests and fixtures
- documentation drafts
- PR descriptions
- local browser checks

The developer owns:

- architecture decisions
- security decisions
- commits and pushes
- production commands
- database writes and migrations
- deploys and rollbacks
- credentials and access scope

## Project Setup

Update this section for your project.

```text
Runtime: PHP 8.x
Package manager: Composer
Frontend: update if applicable
Database: update if applicable
Framework/CMS: update if applicable
```

## Common Commands

Replace these placeholders with commands that work in your repository.

```bash
composer install
composer test
composer lint
composer lint:fix
```

If a command is not verified in this project, mark it as an assumption instead of presenting it as fact.

## Code Style

- Follow standard PHP conventions.
- Use 4-space indentation.
- Prefer short array syntax.
- Keep changes scoped to the task.
- Prefer existing framework patterns over new abstractions.
- Do not refactor unrelated code while fixing a bug.
- Add tests for behavior changes when the project has a test path.

## File Boundaries

Agents must not edit these without explicit human approval:

```text
.env
.env.*
composer.lock        # unless dependency update is the task
vendor/
node_modules/
public/uploads/
storage/
backups/
```

Add project-specific generated files, third-party code, build output, or production data directories here.

## Security Rules

- Never request or store production secrets.
- Never print tokens, passwords, private keys, session cookies, or API keys.
- Never add real credentials to examples.
- Use read-only access for discovery.
- Use least-privilege tokens for MCP integrations.
- Do not run destructive commands without explicit human approval.
- Do not run production database operations.
- Do not rewrite git history unless the user explicitly asks and confirms the risk.

## Git Rules

- The developer owns `git commit`, `git push`, merges, deploys, and releases.
- The agent may draft commit messages and PR descriptions.
- Always inspect `git status` and `git diff` before suggesting a commit.
- Do not revert unrelated user changes.
- Do not remove files unless the task explicitly requires it.

## Subagent Delegation Policy

Use the subagents in `.claude/agents/` when the task matches their role.

- Use `explorer` before planning or editing unfamiliar code.
- Use `planner` only after `explorer` for risky or multi-file changes.
- Use `reviewer` after implementation when the change touches auth, billing, migrations, routing, caching, permissions, security, or shared abstractions.
- Use `browser-tester` for local UI verification when the task affects browser behavior.
- Use `docs-writer` for documentation based on existing code, diffs, or verified commands.

Anti-loop rules:

- Do not call the same subagent twice for the same unresolved question.
- If a subagent returns weak findings, refine the task brief instead of repeating the same call.
- Reuse existing planner or reviewer output unless the code materially changed.
- Stop delegating when two subagent calls produce overlapping information.

## MCP Rules

MCP servers extend the agent's capabilities. Treat them like dependencies with credentials.

Allowed by default:

- read-only GitHub queries
- route inspection
- local browser testing
- local logs
- documentation lookup

Requires explicit human approval:

- creating or updating issues/PRs in public repositories
- writing review comments
- running commands that mutate databases
- accessing production services
- anything with billing, auth, or credential scope

## Verification Expectations

For every code change, report what was verified:

- tests run
- lint/build commands run
- browser path checked
- screenshots captured
- commands that could not be run and why

Do not say "all tests pass" unless tests were actually run.
