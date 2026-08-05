# VTX Insights

VTX Insights connects Codex, Claude Code, Cursor, supported GitHub Copilot surfaces, and Google Antigravity to VTX analysis, bot optimization, fleet profile management, write-only provider/exchange connections, Trader and Assistant controls, and complete trading operations on profiles the user owns.

Long analyses use durable `analysis.start` and `analysis.status` tools, then return a complete immutable artifact. Complete retrieval acknowledges exact chunk hashes through `artifact.resume`, so an interrupted host can continue from its durable checkpoint. A host response timeout never requires silently sampling or narrowing the requested VTX population.

Artifact chunks are lossless: read `text` as UTF-8 bytes when `encoding=utf-8`, or decode `base64_data` when `encoding=base64`, then verify the raw `byte_count` and `content_hash` before acknowledging the chunk.

The connector installs two skills with the MCP server. `vtx-insights-analysis`
covers the complete VTX analysis and action surface.
`vtx-bot-trade-chain-analysis` is the flagship deep-analysis workflow: it
reconstructs settings generations, decisions, executions, fills, position
changes, exits, fees, funding, PnL, and market context before recommending a
focused bot improvement.

Remote server: `https://api.vtxmacro.com/insights/mcp`

Analyze the signed-in user's bots, named public VTX profiles or wallets, or the whole public platform. Settings, profile, connection, automation, and trading actions work only for profiles the user owns and require separately approved permissions. Bot status and Start cover exact Server/Client cohorts; a Client start saves desired Trader intent without taking over an owner and remains waiting until eligible live ownership is confirmed. Connection values are write-only and cannot be read back through Insights. Trading includes market, limit, trigger, and scale orders; order and TWAP cancellation; leverage changes; one-position or confirmed all-position closes; and resumable heterogeneous fleet batches.

## Codex

```bash
codex plugin marketplace add DataDoesYou/VTX-Insights
codex plugin add vtx-insights@vtx-insights
codex mcp login vtx-insights --scopes insights:read,insights:settings,insights:control,insights:trade,insights:profiles,insights:credentials
```

Start a new Codex session after installation so the bundled skills and tools are
discovered. To update an existing cached installation:

```bash
codex plugin marketplace upgrade vtx-insights
codex plugin remove vtx-insights@vtx-insights
codex plugin add vtx-insights@vtx-insights
```

Start another new session and reauthenticate if prompted. Codex desktop users
can disable the plugin from Settings > Plugins. The manual MCP fallback is in
`manual/codex.config.toml`.

After updating, confirm the installed manifest reports only `2026.8.10` before
starting the new session.

## Claude Code

```bash
claude plugin marketplace add DataDoesYou/VTX-Insights
claude plugin install vtx-insights@vtx-insights
claude mcp login plugin:vtx-insights:vtx-insights
```

The one-time version-format migration sorts below the retired packed-date
version, so an ordinary Claude update can leave the old package installed. Run
`claude plugin marketplace update vtx-insights`, then
`claude plugin uninstall vtx-insights@vtx-insights`, then
`claude plugin install vtx-insights@vtx-insights`. Confirm the installed manifest reports only `2026.8.10`, run `/reload-plugins`, and start a fresh session. Use `claude plugin disable`, `enable`, or `uninstall` with
`vtx-insights@vtx-insights` for later lifecycle changes. The manual fallback is
in `manual/claude.mcp.json`.

## Cursor

The public Marketplace submission is pending. Until it is accepted, add the remote server with `manual/cursor.mcp.json`; the package is already shaped for native `/add-plugin vtx-insights` installation after listing.

## GitHub Copilot

MCP Registry publication is pending because the Registry requires the production remote endpoint to be publicly reachable. The current VS Code/Copilot OAuth fallback is `manual/copilot.mcp.json`: place it at `.vscode/mcp.json`, start the server, and select **Auth** above the server entry.

## Google Antigravity

Antigravity has a searchable MCP Store, but VTX Insights is not currently listed there. Open **Settings > Customizations > Installed MCP Servers > Add MCP**, then use **Manage MCP Servers > View raw config** with `manual/antigravity.mcp.json`. The same file works globally at `~/.gemini/config/mcp_config.json` or in one workspace at `.agents/mcp_config.json`. Do not add a bearer token or client secret.

After saving the server, select **Authenticate**, complete VTX sign-in in the browser, copy the authorization code back into Antigravity, and select **Submit**. Dynamic client registration creates the client automatically, but this user confirmation flow is still required.

For a versioned plugin fallback, copy `plugins/vtx-insights` to `.agents/plugins/vtx-insights` or `_agents/plugins/vtx-insights` for one workspace, or to `~/.gemini/config/plugins/vtx-insights` globally. Its root `plugin.json` and `mcp_config.json` expose the same server and reuse the shared VTX Insights skill.

## Use

Start with: “Use the VTX bot trade-chain analysis skill to explain why my bots are winning or losing, recommend the smallest evidence-backed improvements, and ask before applying them.”

The connected model chooses the tools, reasoning, and answer. VTX does not grade, rewrite, or verify model responses or conclusions. The available VTX permissions are `insights:read`, `insights:settings`, `insights:control`, `insights:trade`, `insights:profiles`, and `insights:credentials`; approve only what this agent should be able to do.

Setup, grant management, compatibility status, privacy details, and troubleshooting live at https://vtxmacro.com/insights.
