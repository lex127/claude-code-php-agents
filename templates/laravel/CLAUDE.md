# CLAUDE.md

@AGENTS.md

## Claude Code: AI Delegation Rules

These rules are Claude Code specific and complement `AGENTS.md`, which is shared across AI tools.

They configure how the main Claude Code session delegates work to subagents defined in `.claude/agents/`.

Copy the subagents from the root `.claude/agents/` directory into your project and adjust model names if needed.

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

- Locating routes, controllers, services, middleware, policies, Form Requests, jobs, events, migrations, or tests.
- Mapping dependencies between service and controller layers before any change.
- Checking whether a pattern, trait, service class, or existing abstraction already exists.
- Investigating an unfamiliar part of the codebase.

## Use `planner` Before

- Auth, permissions, policy, middleware, guard, or Gate changes.
- Billing, subscriptions, checkout, Cashier/Stripe webhook, payment, or transactional logic.
- Database migrations, especially nullable changes or multi-step alterations on large tables.
- Multi-layer features touching route + controller + service + Livewire/Blade/Inertia.
- Queue, cron, event/listener, broadcast, or notification changes.
- Complex refactors or unclear bugs requiring multi-file impact analysis.

Skip `planner` when the explorer output makes the change obvious and low risk.

## Use `reviewer` After

- Auth, permissions, policy, middleware, guard, or Gate changes.
- Billing, subscription, Cashier, webhook, or payment changes.
- Database migrations affecting users, billing, permissions, or shared records.
- Service layer changes that touch multiple models or cross database boundaries.
- Queue, cron, or event-driven logic where silent failures are hard to detect.

## Use `browser-tester` For

- UI regressions in Livewire, Inertia/Vue, or Blade views.
- Login, registration, checkout, account, or admin flow verification.
- Console and network error inspection.
- Screenshot capture after frontend changes.

Requires the local dev server to be running. Update this line for your project:

```text
Local dev server: php artisan serve (or Sail: sail up) at http://localhost:8000
```

## Use `docs-writer` For

- README updates after adding commands or config.
- Changelog entries for releases.
- Internal setup notes for new team members.
- Documenting Artisan commands, env variables, or project conventions.

## Escalation Rules

- Escalate `explorer` to `planner` only when architectural uncertainty remains after discovery.
- Escalate `browser-tester` to `planner` only if reproduction points to a systemic design issue.
- Escalate `reviewer` to `planner` only when review uncovers design-level flaws rather than implementation bugs.
- Avoid chaining multiple Opus agents back-to-back unless the task is high risk.

## Anti-Loop Protection

- Do not invoke the same subagent twice for the same unresolved question.
- If a subagent did not answer, refine the brief or ask the user.
- Reuse existing `planner` or `reviewer` output unless the implementation materially changed.
- If two subagent calls in a row return overlapping content, stop delegating and act on what you have.

## Token Discipline

- Prefer one comprehensive `planner` invocation over several incremental ones.
- Always run `explorer` first to narrow candidate files before `planner` reads them.
- Reviewer should inspect only changed surfaces first; broader reads only when a safety question requires it.
- Never send raw large logs, exports, or grep output to Opus agents. Summarize first.
- Do not invoke `planner` or `reviewer` for tasks where Sonnet alone is sufficient.

## Human-Owned Actions

The developer owns: `git commit`, `git push`, merges, deploys, `php artisan migrate` against shared environments, production queue restarts, credential and permission changes.