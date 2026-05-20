# Safety Model

AI coding agents are useful because they are fast. They are dangerous for the same reason.

This repository assumes the following model:

> A coding agent can be trusted with bounded tasks, not with unbounded consequences.

## Non-Negotiable Rules

- No production credentials.
- No broad personal access tokens.
- No autonomous deploys.
- No autonomous database writes.
- No force pushes or history rewrites without explicit human approval.
- No destructive shell commands without explicit human approval.
- No hidden changes outside the requested task.

## Least Privilege

Give every agent the minimum access required for the current job.

Examples:

- code discovery: read-only repository access
- GitHub issue drafting: repository-scoped token, issue permissions only
- browser testing: local dev server only
- logs: local/staging logs, not production secrets

## Human-Owned Actions

The developer owns:

- commits
- pushes
- merges
- deploys
- production commands
- migrations
- credential changes
- security decisions

The agent may draft commands or explain tradeoffs. The human executes high-risk actions.

## Read-Only Specialist Agents

Planner and reviewer agents should usually be read-only.

A planner that can edit code is no longer just a planner.
A reviewer that rewrites code is no longer independent.

Keep the roles separate unless you have a specific reason to merge them.

## MCP Safety

MCP tools are capabilities, not decorations.

Before enabling an MCP server, ask:

- What can it read?
- What can it write?
- Which credentials does it use?
- Is it scoped to one project?
- Can a bad command affect production?

If the answer includes production data, billing, auth, or broad organization access, keep it outside the agent by default.
