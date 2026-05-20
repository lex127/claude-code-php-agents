# Model Routing

Different development tasks need different reasoning depth.

Using the strongest model for everything is expensive and often unnecessary. Using the cheapest model for everything lowers quality when judgement matters.

The practical answer is routing.

## Default Routing

| Task | Suggested model | Agent |
| --- | --- | --- |
| Find files and existing patterns | Haiku | explorer |
| Summarize docs or logs | Haiku | docs-writer or explorer |
| Browser smoke checks | Haiku | browser-tester |
| Normal implementation | Sonnet | main session |
| Tests for normal behavior | Sonnet | main session |
| Architecture planning | Opus | planner |
| High-risk review | Opus | reviewer |

## Explorer Before Planner

Do not ask an expensive reasoning agent to discover the codebase from scratch.

Use this flow:

1. `explorer` finds relevant files and returns path:line references.
2. `planner` reasons over the known surface area.
3. The main session implements the plan.
4. `reviewer` reviews the changed files.

## Avoid Raw Output

Do not send these directly to Opus:

- huge log files
- full database exports
- long grep output
- entire generated files
- raw API payload dumps

Summarize first. Pass the smallest useful context into the expensive model.

## Anti-Loop Policy

Subagents can loop if the main session keeps asking the same specialist the same unresolved question.

Use these rules:

- Do not call the same subagent twice for the same unresolved question.
- If output is weak, improve the brief or ask for missing context.
- Reuse planner/reviewer output unless the implementation materially changed.
- Stop delegating when two subagent calls return overlapping content.
