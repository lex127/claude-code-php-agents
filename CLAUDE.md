# CLAUDE.md

@AGENTS.md

## Claude Code: AI Delegation Rules

These rules are Claude Code specific and complement `AGENTS.md`, which is shared across AI tools.

They configure how the main Claude Code session delegates work to subagents defined in `.claude/agents/`.

## Available Subagents

- `explorer` (Haiku) — fast file, symbol, route, test, and pattern discovery. Returns `path:line` pointers. No writes.
- `planner` (Opus) — architecture analysis and implementation planning. No shell commands and no code writes.
- `reviewer` (Opus) — post-implementation review of high-risk changes. Diff-first. No rewrites.
- `browser-tester` (Haiku) — Chrome DevTools MCP-driven UI verification. Escalate manually for complex state or design issues.
- `docs-writer` (Haiku) — documentation, changelog, release notes, and setup notes based on verified source material.

## Delegation Policy

- Main implementation stays in Sonnet unless the user explicitly asks for Opus.
- Opus agents (`planner`, `reviewer`) are for reasoning only, never for writing production code.
- Always call `explorer` before `planner` when the task needs codebase discovery.
- Planning without discovery wastes Opus tokens and increases the chance of hallucinated file paths.
- Skip subagents for trivial one-file edits, typo fixes, formatting, or simple documentation tweaks.

## Use `explorer` First When

- Locating routes, controllers, services, middleware, policies, commands, jobs, events, migrations, tests, templates, or frontend pages.
- Mapping dependencies between layers before any change.
- Checking whether a pattern, helper, trait, service, hook, filter, or existing abstraction already exists.
- Investigating an unfamiliar part of the codebase.

## Use `planner` Before

- Auth, permissions, policy, middleware, or session changes.
- Billing, subscriptions, checkout, webhook, payment, or transactional logic.
- Database migrations or cross-database writes.
- Multi-layer features touching route + controller + service + UI.
- Caching, routing, permalink, queue, cron, or i18n/multilingual behavior.
- Complex refactors or unclear bugs requiring multi-file impact analysis.

Skip `planner` when the explorer output makes the change obvious and low risk.

## Use `reviewer` After

- Auth, permissions, middleware, policy, or role changes.
- Billing, subscription, checkout, webhook, or payment changes.
- Database migrations affecting users, billing, permissions, or shared records.
- Transactional writes or multi-service workflows.
- Shared abstractions, framework-level utilities, caching, routing, or i18n changes.
- Any change where a subtle regression would be expensive.

## Use `browser-tester` For

- UI regressions and frontend bug reproduction.
- Checkout, account, subscription, login, admin, or dashboard flow verification.
- Responsive layout checks.
- Console and network error inspection.
- Screenshot capture after browser-facing changes.

Requires the local dev server to be running. Update this line for your project:

```text
Local dev server: document the command and URL here.
```

## Use `docs-writer` For

- README updates.
- Changelog entries.
- Release notes.
- Internal setup notes.
- Documenting verified commands or project conventions.

Do not let `docs-writer` invent commands, environment variables, features, or guarantees.

## Escalation Rules

- Escalate `explorer` to `planner` only when architectural uncertainty remains after discovery.
- Escalate `browser-tester` to `planner` only if reproduction points to a systemic design issue, not a localized bug fix.
- Escalate `reviewer` to `planner` only when review uncovers design-level flaws rather than implementation bugs.
- Avoid chaining multiple Opus agents back-to-back unless the task is high risk.
- If Sonnet can solve the task with a narrow diff and a clear verification path, do not invoke Opus.

## Anti-Loop Protection

- Do not invoke the same subagent twice for the same unresolved question.
- If a subagent did not answer, refine the brief or ask the user.
- Reuse existing `planner` or `reviewer` output unless the implementation materially changed.
- Prefer updating an existing plan over generating a new one.
- If two subagent calls in a row return overlapping content, stop delegating and act on what you have.

## Token Discipline

- Prefer one comprehensive `planner` invocation over several incremental ones.
- Always run `explorer` first to narrow candidate files before `planner` reads them.
- Reviewer should inspect only changed surfaces first; broader reads only when a safety question requires it.
- Never send raw large logs, exports, or grep output to Opus agents. Summarize first.
- Do not duplicate work the subagent is doing in the main session.
- Do not invoke `planner` or `reviewer` for tasks where Sonnet alone is sufficient.

## Human-Owned Actions

Claude Code may draft commands, summaries, commits, PR descriptions, and verification steps.

The developer owns:

- `git commit`
- `git push`
- merges
- deploys
- production commands
- destructive commands
- migrations against shared environments
- credential and permission changes
