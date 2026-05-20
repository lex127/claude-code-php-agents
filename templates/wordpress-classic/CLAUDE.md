# CLAUDE.md

@AGENTS.md

## Claude Code: AI Delegation Rules

These rules are Claude Code specific and complement `AGENTS.md`, which is shared across AI tools.

They configure how the main Claude Code session delegates work to subagents defined in `.claude/agents/`.

Copy the subagents from the root `.claude/agents/` directory into your project and adjust model names if needed.

See also: [docs/wordpress-notes.md](../../docs/wordpress-notes.md) for WordPress-specific caveats on caching, WP-CLI risk, multilingual slugs, and additive customization.

## Available Subagents

- `explorer` (Haiku) — fast file, symbol, hook, and pattern discovery. Returns `path:line` pointers. No writes.
- `planner` (Opus) — architecture analysis and implementation planning. No shell commands and no code writes.
- `reviewer` (Opus) — post-implementation review of high-risk changes. Diff-first. No rewrites.
- `browser-tester` (Haiku) — Chrome DevTools MCP-driven UI verification. Escalate manually for complex flows.
- `docs-writer` (Haiku) — documentation, changelog, and setup notes based on verified source material.

## Delegation Policy

- Main implementation stays in Sonnet unless the user explicitly asks for Opus.
- Opus agents (`planner`, `reviewer`) are for reasoning only, never for writing production code.
- Always call `explorer` before `planner` when the task needs codebase discovery.
- Skip subagents for trivial one-file edits, typo fixes, or simple documentation tweaks.

## Use `explorer` First When

- Locating theme template files, plugin hooks, custom post types, shortcodes, or REST endpoints.
- Mapping which actions or filters are hooked where.
- Checking whether a function, hook, or pattern already exists in the child theme or custom plugins.

## Use `planner` Before

- Permalink or rewrite rule changes.
- Caching changes affecting page cache or object cache.
- Auth, login, user roles, or capabilities changes.
- Changes to mu-plugins or files loaded on every request.
- Any script that touches the database directly or via WP-CLI.

Skip `planner` when the explorer output makes the change obvious and low risk.

## Use `reviewer` After

- Rewrite rule or slug changes that could break production URLs.
- Caching changes where wrong flush order could serve stale content.
- Any `wp eval-file` script before running on a real database.
- Multilingual plugin (Polylang/WPML) slug or translation-link changes.

## Use `browser-tester` For

- Theme frontend regressions after template or style changes.
- Form, login, or admin flow verification.
- Console and network error inspection.

Requires the local dev server to be running. Update this line for your project:

```text
Local dev server: update with your local URL (e.g. http://yoursite.local)
```

## Escalation Rules

- Escalate `explorer` to `planner` only when architectural uncertainty remains after discovery.
- Escalate `browser-tester` to `planner` only if reproduction points to a systemic design issue.
- Escalate `reviewer` to `planner` only when review uncovers design-level flaws rather than implementation bugs.

## Anti-Loop Protection

- Do not invoke the same subagent twice for the same unresolved question.
- Reuse existing `planner` or `reviewer` output unless the implementation materially changed.
- If two subagent calls in a row return overlapping content, stop delegating and act on what you have.

## Token Discipline

- Always run `explorer` first to narrow candidate files before `planner` reads them.
- Reviewer should inspect only changed surfaces first.
- Never send raw large logs or grep output to Opus agents. Summarize first.
- Do not invoke `planner` or `reviewer` for tasks where Sonnet alone is sufficient.

## Human-Owned Actions

The developer owns: `git commit`, `git push`, deploys, `wp db import`, `wp search-replace`, cache flush on production, and credential changes.