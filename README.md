# VTX Insights

VTX Insights connects Codex, Claude Code, OpenCode, OpenClaw, Hermes Agent, Cursor, supported GitHub Copilot surfaces, and Google Antigravity to VTX analysis, bot optimization, fleet profile management, write-only provider/exchange connections, Trader and Assistant controls, and complete trading operations on profiles the user owns.

Long analyses use durable `analysis.start` and `analysis.status` tools, then return a complete immutable artifact. Complete retrieval acknowledges exact chunk hashes through `artifact.resume`, so an interrupted host can continue from its durable checkpoint. A host response timeout never requires silently sampling or narrowing the requested VTX population.

Artifact chunks are lossless: read `text` as UTF-8 bytes when `encoding=utf-8`, or decode `base64_data` when `encoding=base64`, then verify the raw `byte_count` and `content_hash` before acknowledging the chunk.

The packaged Codex, Claude Code, and Cursor plugins install two skills with the
MCP server. OpenCode, OpenClaw, and Hermes install the same public skills
separately with the steps below. `vtx-insights-analysis` covers the complete VTX analysis and
action surface.
`vtx-bot-trade-chain-analysis` is the flagship deep-analysis workflow: it
reconstructs settings generations, decisions, executions, fills, position
changes, exits, fees, funding, PnL, and market context before recommending a
focused bot improvement.

Remote server: `https://api.vtxmacro.com/insights/mcp`

Analyze the signed-in user's bots, named public VTX profiles or wallets, or the whole public platform. Settings, profile, connection, automation, and trading actions work only for profiles the user owns and require separately approved permissions. Bot status and Start cover exact Server/Client cohorts; a Client start saves desired Trader intent without taking over an owner and remains waiting until eligible live ownership is confirmed. Connection values are write-only and cannot be read back through Insights. Trading includes market, limit, trigger, and scale orders; order and TWAP cancellation; leverage changes; one-position or confirmed all-position closes; and resumable heterogeneous fleet batches.

OAuth identifies the signed-in account but does not classify a supplied profile
handle or wallet. For named-profile work, call `profiles.discover` once, confirm
each `owned_by_caller` label, and use the matching exact owned or public
selection. VTX rejects an unresolved exact selector before starting a durable
analysis instead of silently analyzing a smaller population.

## Codex

```bash
codex plugin marketplace add DataDoesYou/VTX-Insights
codex plugin add vtx-insights@vtx-insights
codex mcp login vtx-insights --scopes insights:read
```

Authentication begins in the MCP client, not on the VTX Insights website. In
ChatGPT desktop, open **Settings > MCP servers**, select VTX Insights, and choose
**Authenticate**. In Codex CLI or the IDE, use the command above. Codex 0.146.0
has a known OAuth issuer-validation regression; upgrade it if login reports
`Authorization server response missing required issuer`.

Start a new Codex session after installation so initialization and tool
discovery run. In ChatGPT desktop, type `/mcp` and confirm VTX Insights is
active. Confirm expected read tools such as `analysis.start` and
`positions.episodes` are available; an OAuth grant alone is not operational
verification. To update an existing cached installation:

```bash
codex plugin marketplace upgrade vtx-insights
codex plugin remove vtx-insights@vtx-insights
codex plugin add vtx-insights@vtx-insights
```

Start another new session and reauthenticate if prompted. Codex desktop users
can disable the plugin from Settings > Plugins. The manual MCP fallback is in
`manual/codex.config.toml`.

After updating, confirm the installed manifest reports only `2026.9.2` before
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
`claude plugin install vtx-insights@vtx-insights`. Confirm the installed manifest reports only `2026.9.2`, run `/reload-plugins`, and start a fresh session. Use `claude plugin disable`, `enable`, or `uninstall` with
`vtx-insights@vtx-insights` for later lifecycle changes. The manual fallback is
in `manual/claude.mcp.json`.

## OpenCode

Save `manual/opencode.json` as `opencode.json` in the project where you use
OpenCode, or merge its `vtx-insights` entry into your global OpenCode config.
Then authenticate and confirm the connection:

```bash
opencode mcp auth vtx-insights
opencode mcp list
```

OpenCode completes VTX OAuth in the browser. Approve only the permissions this
OpenCode session needs. If the connection fails, run `opencode mcp debug
vtx-insights`; remove its stored grant with `opencode mcp logout vtx-insights`.

OpenCode discovers native Agent Skills under
`~/.config/opencode/skills/<skill-name>/SKILL.md`. Clone this repository and copy
both directories under `plugins/vtx-insights/skills/` into that global skills
directory, preserving each skill directory name.

## OpenClaw

OpenClaw's published CLI can connect to the remote VTX MCP server with OAuth:

```bash
openclaw mcp add vtx-insights --url https://api.vtxmacro.com/insights/mcp --transport streamable-http --auth oauth --oauth-scope 'insights:read' --timeout 120
openclaw mcp login vtx-insights
openclaw mcp login vtx-insights --code YOUR_AUTHORIZATION_CODE
openclaw mcp doctor vtx-insights --probe
```

Open the URL printed by the first login command, approve the requested VTX
permissions, then replace `YOUR_AUTHORIZATION_CODE` with the returned code.

The equivalent checked configuration is in `manual/openclaw.config.json`. To
install the VTX workflows, clone this repository and run `openclaw skills
install <skill-directory> --global` for both directories under
`plugins/vtx-insights/skills/`.

## Hermes Agent

Hermes Agent 0.20.4 or newer can connect to VTX through its native remote MCP
and OAuth support when installed through Hermes' supported official installer,
Desktop app, or Docker image. The separately published PyPI package is an
unsupported distribution and does not provide this safety contract.

```bash
hermes mcp add vtx-insights --url https://api.vtxmacro.com/insights/mcp --auth oauth --connect-timeout 315
hermes config set mcp_servers.vtx-insights.trust untrusted
hermes mcp test vtx-insights
```

The equivalent `mcp_servers` configuration is in `manual/hermes.config.json`.
The `untrusted` tier keeps Hermes' local approval gate in front of VTX tools
that can change settings, control bots, manage profiles or connections, or
trade.
Hermes can install either public
`SKILL.md` directly with `hermes skills install <raw-skill-url>`.

OpenCode, OpenClaw, and Hermes support the Insights analysis/action surface
independently of VTX trading inference. Their current machine result contracts
do not meet VTX's durable inference receipt and interruption requirements; use
the foreground `agent-connect` path if one of these harnesses supplies Provider
decisions or runs a Main Agent assignment. The harness loop must remain open.

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
