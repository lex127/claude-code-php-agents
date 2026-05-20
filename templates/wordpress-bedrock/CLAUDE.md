# CLAUDE.md

@AGENTS.md

## Claude Code: AI Delegation Rules

These rules are Claude Code specific and complement `AGENTS.md`, which is shared across AI tools.

They configure how the main Claude Code session delegates work to subagents defined in `.claude/agents/`.

Copy the subagents from the root `.claude/agents/` directory into your project and adjust model names if needed.

See also: [docs/wordpress-notes.md](../../docs/wordpress-notes.md) for WordPress- and Bedrock-specific caveats on caching, WP-CLI risk, multilingual slugs, and additive customization.

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

- Locating theme files, plugin hooks, mu-plugin loaders, custom post types, taxonomies, shortcodes, REST endpoints, or WP-CLI commands.
- Mapping how filters and actions connect across theme, plugin, and mu-plugin layers.
- Checking whether a hook, filter, helper function, or WP_Query pattern already exists before adding one.
- Investigating Bedrock config flow: `config/application.php`, `config/environments/`, `.env`.

## Use `planner` Before

- Routing, permalink, rewrite rule, or slug changes (especially with multilingual plugins like Polylang or WPML).
- Caching changes involving LiteSpeed, WP Super Cache, Redis, or object caching.
- Multi-layer changes touching templates + hooks + CPT registration.
- Auth or session changes in WordPress admin, login flow, or user roles/capabilities.
- Bedrock environment config changes affecting `WP_ENV` behavior.
- Scripts that run `wp eval-file` against a real database.

Skip `planner` when the explorer output makes the change obvious and low risk.

## Use `reviewer` After

- Rewrite rule, permalink, or slug changes that could break URLs in production.
- Caching changes where a wrong flush sequence could serve stale content.
- Polylang/WPML translation-linking changes or language-specific slug updates.
- Any `wp eval-file` script before it runs on a real database.
- Changes to must-use plugins or loader files that affect all requests.

## Use `browser-tester` For

- Theme frontend regressions (layout, navigation, forms, hero sections).
- Custom post type archive or single template verification.
- Login, admin, checkout, or contact form flow checks.
- Console and network error inspection after theme or plugin updates.

Requires the local dev server to be running. Update this line for your project:

```text
Local dev server: make up (or docker-compose up) — site at http://localhost:8880
```

## Use `docs-writer` For

- README updates for new commands or deployment steps.
- Changelog entries.
- Internal notes on Polylang slug conventions, caching behavior, or WP-CLI scripts.

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

The developer owns: `git commit`, `git push`, merges, deploys, `wp eval-file` against shared environments, `wp db import`, `wp search-replace`, cache flush on production, and credential changes.