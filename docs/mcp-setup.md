# MCP Setup Notes

MCP servers give agents access to external systems. Use them deliberately.

## Configuration

Claude Code reads project-scoped MCP servers from `.mcp.json` in the project root. Servers added with `claude mcp add --scope local` or `--scope user` are stored in `~/.claude.json`; `~/.claude/settings.json` does not hold MCP servers. The project `.mcp.json` is committed to the repo and applies to everyone on the team, so keep tokens out of it: Claude Code expands `${VAR}` references in `command`, `args`, `env`, `url` and `headers`.

### Chrome DevTools MCP (browser-tester)

The `browser-tester` agent uses Chrome DevTools MCP for UI smoke testing. Install via npm and add to `.mcp.json`:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest"]
    }
  }
}
```

The tool names in `browser-tester.md` use the prefix `mcp__chrome-devtools__`, which matches the `chrome-devtools` server name above. If your local MCP server registers under a different name, update the `tools:` list in `.claude/agents/browser-tester.md` to match.

To verify the tool names available in your session, run `/mcp` in Claude Code.

### GitHub MCP

Use GitHub's official remote server. The `@modelcontextprotocol/server-github` npm package is deprecated.

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer ${GITHUB_PAT}"
      }
    }
  }
}
```

Or add it for yourself only, without touching `.mcp.json`:

```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

If you prefer a local server, GitHub also publishes it as the `ghcr.io/github/github-mcp-server` Docker image.

Use a fine-grained personal access token scoped to one repository with only the permissions the task needs. Keep the token in your environment (`GITHUB_PAT` above), never in a committed file.

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

Claude Code names MCP tools `mcp__<server-name>__<tool-name>` and keeps hyphens from the server name, so a server registered as `chrome-devtools` exposes `mcp__chrome-devtools__navigate_page`. If you register the server under another name, adjust the `tools:` list in `browser-tester.md` to match. Run `/mcp` in Claude Code to list available tools in the current session.
