# BlueVoyant Agent plugin marketplace

Currently we host exactly one plugin, providing access to BlueVoyant's Third Party Risk Management service via MCP, and accompanying skills to improve the effectiveness of agents accessing the service.

You will require a log in to the BlueVoyant TPRM service to use this. If your company does not yet work with BlueVoyant, please contact our sales team for more information.


## Getting started

The marketplace lives at [github.com/Bluevoyant/tprm-mcp](https://github.com/Bluevoyant/tprm-mcp). Install `bluevoyant-tprm` with whichever agent you use; on first use the bundled MCP server `tprm-prod` (`https://mcp.bluevoyant.com/mcp`) takes you through OAuth sign-in with your BlueVoyant TPRM credentials.

Client behavior below is per vendor docs as of 2026-10-23

### Claude Code (terminal, VS Code, JetBrains, desktop Code tab)

```bash
claude plugin marketplace add Bluevoyant/tprm-mcp
claude plugin install bluevoyant-tprm@bluevoyant-tprm   # --scope user | project | local
```

Or inside a session: `/plugin install bluevoyant-tprm@bluevoyant-tprm`, then pick a scope. Claude Code v2.1.292+ can do both in one command: `claude plugin install bluevoyant-tprm --marketplace Bluevoyant/tprm-mcp`. All Claude Code surfaces (terminal, VS Code, JetBrains, the desktop app's Code tab) share the same settings, so one install covers them. Verify with `claude plugin list`; the skills appear as `/bluevoyant-tprm:<skill>`.

### Codex (Codex CLI and Codex in the ChatGPT desktop app)

```bash
codex plugin marketplace add Bluevoyant/tprm-mcp
```

Then run `codex`, enter `/plugins`, open **bluevoyant-tprm** in the marketplace tab, install it, and start a new session. The repo-marketplace layout Codex reads (`.agents/plugins/marketplace.json`) is already in place; `--ref` and `--sparse` are supported on the `marketplace add` command if you need to pin a ref.

### GitHub Copilot CLI

Copilot CLI reads the same `.claude-plugin/marketplace.json` layout this repo already ships:

```bash
copilot plugin marketplace add Bluevoyant/tprm-mcp
copilot plugin install bluevoyant-tprm@bluevoyant-tprm
```

### VS Code (Copilot agent)

Register the marketplace in `settings.json`:

```json
{ "chat.plugins.marketplaces": ["Bluevoyant/tprm-mcp"] }
```

Then open the Extensions view, search `@agentPlugins`, and install **bluevoyant-tprm** (answer the first-install trust prompt), or run `/plugin install bluevoyant-tprm` in a Copilot chat session. VS Code also picks up plugins installed by GitHub Copilot CLI from `~/.copilot/installed-plugins/`.

### Cursor

The package is spec-conformant (root `plugin.json`), so it loads from Cursor's local plugin directory. From a checkout:

```bash
git clone https://github.com/Bluevoyant/tprm-mcp
mkdir -p ~/.cursor/plugins/local
cp -R tprm-mcp/plugins/bluevoyant-tprm ~/.cursor/plugins/local/
```

Restart Cursor (or **Developer: Reload Window**), then enable **bluevoyant-tprm** in **Customize**. Cursor does not expand the standard's `${PLUGIN_ROOT}`/`${PLUGIN_DATA}` variables in `mcp.json` — this plugin's remote-HTTP entry needs neither.

For teams, this repo also imports as a Cursor team marketplace: Dashboard → **Plugins & MCPs** → **Add Marketplace** → **Import from Repo** with `https://github.com/Bluevoyant/tprm-mcp`. Cursor reads the root `.cursor-plugin/marketplace.json` (bare plugin names resolved under `metadata.pluginRoot: plugins`) plus `plugins/bluevoyant-tprm/.cursor-plugin/plugin.json` beside the portable manifests; **Marketplace Settings → Refresh** pulls updates. Listing in Cursor's *public* marketplace is a separate submission process, not a repo change — see the additions list below.

### Kiro (powers)

Powers panel → **Add Custom Power** → **Import power from GitHub** → `https://github.com/Bluevoyant/tprm-mcp` (power directory: `plugins/bluevoyant-tprm`), or **Import power from a folder** pointing at that directory from a local clone. Kiro manages the bundled MCP server internally; it activates and deactivates with the power. Uninstall via the panel or `/powers uninstall bluevoyant-tprm`.

### Gemini CLI

```bash
git clone https://github.com/Bluevoyant/tprm-mcp
gemini extensions install ./tprm-mcp/plugins/bluevoyant-tprm
```

Answer the folder-trust prompt (`--consent --skip-settings` for scripted installs), then confirm with `gemini extensions list` — the extension shows as `bluevoyant-tprm (3.4.0)` with the `tprm-prod` MCP server and both skills.

Or install straight from the URL — the repo root carries its own `gemini-extension.json`, named `tprm-mcp` after the repo (Gemini matches the manifest name to the clone's root directory):

```bash
gemini extensions install https://github.com/Bluevoyant/tprm-mcp --consent --skip-settings
```

That mode delivers the connector only — Gemini's manifest has no skills field and the repo root has no `skills/` — so use the local-path install above when you want the full plugin.

### OpenHands

```bash
openhands --plugin github:Bluevoyant/tprm-mcp/plugins/bluevoyant-tprm
```

or list it in `~/.openhands/config.toml` under `[plugins] sources = [...]`, or add the same `github:owner/repo/path` source in the GUI's Plugins tab. OpenHands loads the Agent Plugins 1.0 layout (root `plugin.json` takes precedence over `.claude-plugin/`-style layouts) and supports `streamable-http`, the transport this plugin's `mcp.json` declares.

### OpenClaw

```bash
git clone https://github.com/Bluevoyant/tprm-mcp
openclaw plugins install ./tprm-mcp/plugins/bluevoyant-tprm
```

or from the marketplace: `openclaw plugins install bluevoyant-tprm --marketplace Bluevoyant/tprm-mcp`. Confirm detection with `openclaw plugins inspect <id>` — it should report `Format: bundle`, `Bundle format: agent (Agent Plugins)`.

### omp (oh-my-pi)

omp reads the same `.claude-plugin/marketplace.json` catalog (its preferred `.omp-plugin/` path is unnecessary — the Claude-compatible location is a supported fallback) and loads Agent Plugins 1.0 packages through its portable discovery provider:

```bash
omp plugin marketplace add Bluevoyant/tprm-mcp
omp plugin install bluevoyant-tprm@bluevoyant-tprm   # --scope user (default) | project
```

Or in a session: `/marketplace add Bluevoyant/tprm-mcp`, then `/marketplace install bluevoyant-tprm@bluevoyant-tprm`. Verify with `omp plugin list`; run `/reload-plugins` in a live session to pick up the skills and MCP server.

