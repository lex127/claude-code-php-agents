# Tool-Specific Instruction Files

Different AI coding tools read different instruction files and support different capabilities.

This repository separates shared project rules from tool-specific behavior.

## Shared Rules

Use `AGENTS.md` for rules that should apply to all coding assistants:

- project structure
- commands
- coding style
- security rules
- git rules
- verification expectations
- file boundaries

This file should avoid tool-specific concepts such as Claude Code subagents, Cursor background agents, or Copilot-specific UI behavior.

## Claude Code

Use `CLAUDE.md` for Claude Code-specific behavior:

- `@AGENTS.md` import
- `.claude/agents/` subagent routing
- model routing between Haiku, Sonnet, and Opus
- Claude Code MCP assumptions
- anti-loop and token-discipline rules

Recommended pattern:

```md
# CLAUDE.md

@AGENTS.md

## Claude Code: AI Delegation Rules

Claude-specific rules go here.
```

## Gemini CLI

If your project uses Gemini CLI and it supports repository instruction files, keep Gemini-specific rules in the relevant Gemini instruction file and point back to `AGENTS.md` where possible.

Avoid copying Claude Code subagent rules into Gemini instructions unless Gemini has equivalent behavior.

## Cursor

Cursor may use project rules differently from Claude Code. Keep shared rules in `AGENTS.md`, then add Cursor-specific rules in the Cursor-supported location for your project.

Cursor background agents should follow the same safety model:

- scoped credentials
- branch isolation
- no production access
- human-owned merge and deploy

## Copilot-Style Agents

Copilot-style agents may not understand Claude Code subagents or `@AGENTS.md` imports.

Keep instructions plain and tool-neutral when targeting them:

- what to change
- what not to touch
- how to verify
- what commands are safe
- what requires human approval

## Why This Separation Matters

A shared `AGENTS.md` should not assume one tool's feature model.

Claude Code subagents are useful, but they are not portable to every assistant. Keeping them in `CLAUDE.md` makes the repository easier to reuse with Codex, Gemini, Cursor, Copilot, and future tools.
