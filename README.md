# VTX Insights

VTX Insights connects Codex, Claude Code, Cursor, and supported GitHub Copilot surfaces to VTX analysis, bot optimization, automation controls, and trading on profiles the user owns.

Remote server: `https://api.vtxmacro.com/insights/mcp`

Analyze the signed-in user's bots, named public VTX profiles or wallets, or the whole public platform. Settings, automation, and trading actions work only for profiles the user owns and require separately approved permissions.

## Codex

```bash
codex plugin marketplace add DataDoesYou/VTX-Insights
codex plugin add vtx-insights@vtx-insights
codex mcp login vtx-insights --scopes insights:read,insights:settings,insights:control,insights:trade
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

Start with: “Compare my bots this month, recommend the highest-impact settings improvements, and ask before applying them.”

The connected model chooses the tools, reasoning, and answer. VTX does not grade, rewrite, or verify model responses or conclusions. The available VTX permissions are `insights:read`, `insights:settings`, `insights:control`, and `insights:trade`; approve only what this agent should be able to do.

Setup, grant management, compatibility status, privacy details, and troubleshooting live at https://vtxmacro.com/insights.
