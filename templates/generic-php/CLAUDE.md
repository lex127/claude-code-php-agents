# CLAUDE.md

@AGENTS.md

## Claude Code: AI Delegation Rules

These rules are Claude Code specific and complement `AGENTS.md`, which is shared across AI tools.

They configure how the main Claude Code session delegates work to subagents defined in `.claude/agents/`.

Copy the subagents from the root `.claude/agents/` directory into your project and adjust model names if needed.

## Available Subagents

- `explorer` (Haiku) — fast file, symbol, and pattern discovery. Returns `path:line` pointers. No writes.
- `planner` (Opus) — architecture analysis and implementation planning. No shell commands and no code writes.
- `reviewer` (Opus) — post-implementation review of high-risk changes. Diff-first. No rewrites.
- `docs-writer` (Haiku) — documentation, changelog, and setup notes based on verified source material.

## Delegation Policy

- Main implementation stays in Sonnet unless the user explicitly asks for Opus.
- Opus agents (`planner`, `reviewer`) are for reasoning only, never for writing production code.
- Always call `explorer` before `planner` when the task needs codebase discovery.
- Skip subagents for trivial one-file edits, typo fixes, or simple documentation tweaks.

## Use `explorer` First When

- Locating classes, services, handlers, tests, or config files.
- Checking whether a pattern or helper already exists before adding one.
- Mapping dependencies before any multi-file change.

## Use `planner` Before

- Auth, session, permissions, or access-control changes.
- Database queries, schema changes, or migrations.
- Multi-file refactors or unclear bugs requiring cross-file analysis.
- Changes to shared utilities or bootstrap files loaded on every request.

Skip `planner` when the explorer output makes the change obvious and low risk.

## Use `reviewer` After

- Auth or permissions changes.
- Database-touching changes.
- Any script that writes to shared state.

## Anti-Loop Protection

- Do not invoke the same subagent twice for the same unresolved question.
- Reuse existing `planner` or `reviewer` output unless the implementation materially changed.
- If two subagent calls in a row return overlapping content, stop delegating and act on what you have.

## Token Discipline

- Always run `explorer` first to narrow candidate files before `planner` reads them.
- Reviewer should inspect only changed surfaces first.
- Never send raw large logs or grep output to Opus agents. Summarize first.

## Human-Owned Actions

The developer owns: `git commit`, `git push`, deploys, database writes against shared environments, and credential changes.
