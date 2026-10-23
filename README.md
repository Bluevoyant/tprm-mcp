# BlueVoyant Agent Plugin Marketplace

As of October 2026, we have a plugin for the BlueVoyant Third Party Risk Management service through MCP, with skills to make agents that access the service more effective.

* Connects your AI agent with BlueVoyant Third-Party Risk Management (TPRM) data. 
* Adds a connector and skills to help supported AI agents query TPRM data and generate reports with BlueVoyant-recommended formatting and attribution.

> [!Note] AI-generated outputs may contain inaccuracies and should be reviewed. Because the AI agent and underlying model are selected and operated by the customer, BlueVoyant does not control the agent’s processing or use of data.

You must have valid BlueVoyant TPRM service credentials to use this plugin. If your company does not yet work with BlueVoyant, [request a demo](https://www.bluevoyant.com/request-a-demo) to get started!


## Getting started

You can sync this repository as a Third Party Marketplace ([OpenAI](https://help.openai.com/en/articles/20001504-importing-and-syncing-plugin-marketplaces-from-github), [Anthropic](https://code.claude.com/docs/en/plugins/install#add-a-marketplace)) to automatically get the latest version of our plugins.

Install `bluevoyant-tprm` with any agent you use.
On first use, the MCP server `tprm-prod` (`https://mcp.bluevoyant.com/mcp`) guides you through OAuth login with your BlueVoyant TPRM credentials.

<!-- Client behavior below is per vendor docs as of 2026-10-23 / can we delete this? -->

### Install with Claude Code (terminal, VS Code, JetBrains, desktop Code tab)
All Claude Code interfaces (terminal, VS Code, JetBrains, the desktop app Code tab) use the same settings, so one install adds the plugin to all. 
To install the plugin, you run an install command and then select a scope. 

Claude Code v2.1.292+ can do both in one command:  
`claude plugin install bluevoyant-tprm --marketplace Bluevoyant/tprm-mcp`

Other versions of Claude Code:
```bash
claude plugin marketplace add Bluevoyant/tprm-mcp
claude plugin install bluevoyant-tprm@bluevoyant-tprm   # --scope user | project | local
```

Or in a session: 
1. Run: `/plugin install bluevoyant-tprm@bluevoyant-tprm`
2. Select a scope. 

Confirm installation: `claude plugin list`

See the plugin skills: `/bluevoyant-tprm:<skill>`

### Install with Codex (Codex CLI and Codex in the ChatGPT desktop app)
1. Run:  `codex plugin marketplace add Bluevoyant/tprm-mcp`
1. Run: `codex`
1. Enter: `/plugins`
1. Open **bluevoyant-tprm** in the marketplace tab
1. Install it and start a new session.  
The repo-marketplace layout Codex shows
 `.agents/plugins/marketplace.json`

To pin a ref,  `--ref` and `--sparse` are supported on the `marketplace add` command.

### Install with GitHub Copilot CLI

Copilot CLI reads the same `.claude-plugin/marketplace.json` layout this repo ships:

```bash
copilot plugin marketplace add Bluevoyant/tprm-mcp
copilot plugin install bluevoyant-tprm@bluevoyant-tprm
```

### Install with VS Code (Copilot agent)

1. Register the marketplace in `settings.json`:

```json
{ "chat.plugins.marketplaces": ["Bluevoyant/tprm-mcp"] }
```

2. Open the Extensions view.
3. Search `@agentPlugins`
4. Install **bluevoyant-tprm** (answer the first-install trust prompt).  

Or, in a Copilot chat session, run: `/plugin install bluevoyant-tprm`  

VS Code also picks up plugins installed by GitHub Copilot CLI from `~/.copilot/installed-plugins/`.

### Install with Cursor

The package is spec-conformant (root `plugin.json`). It loads from Cursor's local plugin directory. 

1. From a checkout, run:

```bash
git clone https://github.com/Bluevoyant/tprm-mcp
mkdir -p ~/.cursor/plugins/local
cp -R tprm-mcp/plugins/bluevoyant-tprm ~/.cursor/plugins/local/
```
2. Restart Cursor (or **Developer: Reload Window**).
3. Enable **bluevoyant-tprm** in **Customize**. 

<!--Note: Cursor does not expand the standard's `${PLUGIN_ROOT}`/`${PLUGIN_DATA}` variables in `mcp.json`. This plugin remote-HTTP entry does not require them.-->

For Teams, this repo imports as a Cursor team marketplace:
Dashboard → **Plugins & MCPs** → **Add Marketplace** → **Import from Repo** with `https://github.com/Bluevoyant/tprm-mcp`.

Cursor reads the root `.cursor-plugin/marketplace.json` (bare plugin names resolved under `metadata.pluginRoot: plugins`) and `plugins/bluevoyant-tprm/.cursor-plugin/plugin.json` next to the portable manifests.

To get updates:  **Marketplace Settings → Refresh** 

Listing in Cursor's *public* marketplace is a separate submission process, not a repo change.

### Install with Kiro (powers)

Powers panel → **Add Custom Power** → **Import power from GitHub** → `https://github.com/Bluevoyant/tprm-mcp` (power directory: `plugins/bluevoyant-tprm`), 
or **Import power from a folder** that points at that directory from a local clone. 

Kiro manages the bundled MCP server internally. It activates and deactivates with the power. 

To uninstall, use the panel or `/powers uninstall bluevoyant-tprm`.

### Install with Gemini CLI
1. Run:
```bash
git clone https://github.com/Bluevoyant/tprm-mcp
gemini extensions install ./tprm-mcp/plugins/bluevoyant-tprm
```
2. Answer the folder-trust prompt (`--consent --skip-settings` for scripted installs).
3. Confirm with `gemini extensions list` — the extension shows as `bluevoyant-tprm (3.4.0)` with the `tprm-prod` MCP server and both skills.

Or install from the URL. 
Note: This installation method delivers the connector only. Gemini's manifest has no skills field, and the repo root has no skills directory. Use the local-path installation if you want the full plugin.

The repo root carries its own `gemini-extension.json`, named `tprm-mcp` after the repo. (Gemini matches the manifest name to the clone's root directory):

```bash
gemini extensions install https://github.com/Bluevoyant/tprm-mcp --consent --skip-settings
```


### Install with OpenHands

```bash
openhands --plugin github:Bluevoyant/tprm-mcp/plugins/bluevoyant-tprm
```

Or list it in `~/.openhands/config.toml` under `[plugins] sources = [...]`

Or add the same `github:owner/repo/path` source in the GUI's Plugins tab.

OpenHands loads the Agent Plugins 1.0 layout (root `plugin.json` takes precedence over `.claude-plugin/`-style layouts) and supports `streamable-http`, the transport that this plugin's `mcp.json` declares.

### Install with OpenClaw

```bash
git clone https://github.com/Bluevoyant/tprm-mcp
openclaw plugins install ./tprm-mcp/plugins/bluevoyant-tprm
```

Or from the marketplace:  
`openclaw plugins install bluevoyant-tprm --marketplace Bluevoyant/tprm-mcp`

Confirm detection with `openclaw plugins inspect <id>` 

It should show: `Format: bundle`, `Bundle format: agent (Agent Plugins)`

### Install with omp (oh-my-pi)

omp reads the same `.claude-plugin/marketplace.json` catalog (the preferred `.omp-plugin/` path is unnecessary because the Claude-compatible location is a supported fallback) and loads Agent Plugins 1.0 packages through its portable discovery provider. 

Run:
```bash
omp plugin marketplace add Bluevoyant/tprm-mcp
omp plugin install bluevoyant-tprm@bluevoyant-tprm   # --scope user (default) | project
```

Or in a session:
1. Run: `/marketplace add Bluevoyant/tprm-mcp`
2. Run: `/marketplace install bluevoyant-tprm@bluevoyant-tprm`
3. Verify: `omp plugin list`
4. To get the skills and MCP server, in a live session run: `/reload-plugins`

