# MCP Setup Notes

MCP servers give agents access to external systems. Use them deliberately.

## Configuration

Claude Code reads MCP servers from `.mcp.json` in the project root or from `~/.claude/settings.json` for user-level servers. Project-level `.mcp.json` is committed to the repo and applies to everyone on the team. Add it to `.gitignore` if it contains personal tokens.

### Chrome DevTools MCP (browser-tester)

The `browser-tester` agent uses Chrome DevTools MCP for UI smoke testing. Install via npm and add to `.mcp.json`:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-chrome-devtools"]
    }
  }
}
```

The tool names in `browser-tester.md` use the prefix `mcp__chrome_devtools__`. If your local MCP server registers under a different name, update the `tools:` list in `.claude/agents/browser-tester.md` to match.

To verify the tool names available in your session, run `/mcp` in Claude Code.

### GitHub MCP

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<your-token-here>"
      }
    }
  }
}
```

Use a fine-grained personal access token scoped to one repository with only the permissions the task needs. Do not commit real tokens to the repo — use environment variable references or add `.mcp.json` to `.gitignore`.

### Laravel-specific MCP (optional)

For Laravel projects, consider adding an MCP server that exposes routes, logs, and Artisan metadata. Check the [awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) list for current options and review each server's permission model before enabling it.

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

MCP tool names vary by environment. The `browser-tester` agent includes example Chrome DevTools MCP tool names. Adjust them to match your local MCP server names. Run `/mcp` in Claude Code to list available tools in the current session.
