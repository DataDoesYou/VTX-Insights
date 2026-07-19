# VTX Insights

VTX Insights connects Codex, Claude Code, Cursor, and supported GitHub Copilot surfaces to the same dedicated read-only VTX MCP server. It contains no VTX backend, database, model runtime, or credential.

Remote server: `https://api.vtxmacro.com/insights/mcp`

The server can analyze the signed-in user's bots, named public VTX profiles or wallets, and the whole public platform. It never exposes email addresses, login or authentication data, billing or contact data, credentials, tokens, or secrets.

## Codex

```bash
codex plugin marketplace add DataDoesYou/VTX-Insights
codex plugin add vtx-insights@vtx-insights
codex mcp login vtx-insights --scopes insights:read
```

Refresh or remove the plugin with `codex plugin marketplace upgrade vtx-insights` and `codex plugin remove vtx-insights@vtx-insights`. Codex desktop users can disable it from Settings > Plugins. The manual MCP fallback is in `manual/codex.config.toml`.

## Claude Code

```bash
claude plugin marketplace add DataDoesYou/VTX-Insights
claude plugin install vtx-insights@vtx-insights
claude mcp login plugin:vtx-insights:vtx-insights
```

Use `claude plugin marketplace update vtx-insights` and `claude plugin update vtx-insights@vtx-insights` to update. Use `claude plugin disable`, `enable`, or `uninstall` with `vtx-insights@vtx-insights`, then run `/reload-plugins` after a lifecycle change. The manual fallback is in `manual/claude.mcp.json`.

## Cursor

The public Marketplace submission is pending. Until it is accepted, add the remote server with `manual/cursor.mcp.json`; the package is already shaped for native `/add-plugin vtx-insights` installation after listing.

## GitHub Copilot

MCP Registry publication is pending because the Registry requires the production remote endpoint to be publicly reachable. The current VS Code/Copilot OAuth fallback is `manual/copilot.mcp.json`: place it at `.vscode/mcp.json`, start the server, and select **Auth** above the server entry.

## Use

Start with: “Compare my bots this month. Inspect coverage and provenance, calculate anything needed, and explain which model is performing best.”

Every answer still requires evidence review. Inspect coverage, provenance, as-of times, warnings, and any large-result artifact before accepting a conclusion.

Setup, grant management, compatibility status, privacy details, and troubleshooting live at https://vtxmacro.com/insights.
