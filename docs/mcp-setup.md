# MCP Setup Notes

MCP servers give agents access to external systems. Use them deliberately.

## Useful MCP Categories

### GitHub

Good for:

- reading issues and pull requests
- searching code
- drafting issue comments
- checking CI status

Use a fine-grained token scoped to one repository when possible.

### Browser Testing

Good for:

- local UI smoke tests
- console inspection
- network inspection
- screenshots
- basic Lighthouse checks

Keep this pointed at local or staging environments, not production admin flows.

### Framework Tools

Examples:

- route inspection
- logs
- read-only database queries
- framework-specific CLI metadata

Prefer read-only operations unless the task explicitly requires writes.

## Avoid by Default

Do not give agents automatic access to:

- production databases
- billing dashboards
- cloud admin credentials
- SSH keys to live servers
- broad personal GitHub tokens
- password managers

## Tool Naming

MCP tool names vary by environment. The `browser-tester` agent includes example Chrome DevTools MCP tool names. Adjust them to match your local MCP server names.
