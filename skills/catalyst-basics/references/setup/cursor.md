# Using Catalyst Skills with Cursor

## Installation

Install the official **Catalyst by Zoho** Cursor plugin:

- **Cursor Marketplace:** **Cursor Settings → Plugins**
- **Local testing:** Copy the repo to `~/.cursor/plugins/local/catalyst-by-zoho` and reload Cursor

See the plugin [README](https://github.com/catalystbyzoho/cursor-plugin) for full install steps.

The plugin bundles skills, rules (`CATALYST.md`), and MCP automatically — no manual skill symlinks or `.cursor/mcp.json` setup required.

## Skill Activation

Skills load from the plugin's `skills/` directory. To verify:

1. Open Cursor in your Catalyst project directory
2. Open the Composer (Cmd+I / Ctrl+I)
3. Ask: "What Catalyst skills are available?"

## MCP Setup (Recommended)

The plugin ships the Catalyst MCP server **pre-configured** in `.mcp.json` — no URL copying or mcp.zoho.com server creation needed.

**To connect:**

1. Open **Cursor Settings → MCP**.
2. Find **catalyst-by-zoho** (may show "Needs authentication").
3. Click **Connect** and complete Zoho OAuth in your browser.
4. Confirm `CatalystbyZoho_*` tools appear in the tool list.

> **Region note:** The default URL points to the **US** data center (`https://catalystbyzoho.zohomcp.com/mcp/message`). If your Catalyst account is on EU, IN, AU, JP, SA, or CA, update the `url` in **Cursor Settings → MCP** to the matching endpoint — or ask the agent to switch it. See `skills/catalyst-zoho-mcp/references/zoho-mcp.md` for the full DC table.

> **Fallback:** If your DC endpoint doesn't work, go to [mcp.zoho.com](https://mcp.zoho.com) → **Connect** → copy the exact URL for your account and update it in MCP settings.

## Pre-flight Checklist

Before asking Cursor to write Catalyst code, ensure:

- [ ] `catalyst login` has been run in your terminal
- [ ] `catalyst init` has been run in the project directory
- [ ] `.catalystrc` and `catalyst.json` exist at the project root
- [ ] **catalyst-by-zoho** shows connected in **Cursor Settings → MCP** (click Connect if needed)

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| Cursor writes files without a project | `.catalystrc` or `catalyst.json` missing | Run `catalyst login` then `catalyst init` |
| `CatalystbyZoho_*` tools not showing | MCP not authorized | **Cursor Settings → MCP** → click **Connect** on `catalyst-by-zoho` |
| MCP shows "Needs authentication" | OAuth not completed | Click **Connect** and complete Zoho login in the browser |
| MCP shows red/error status | OAuth session expired or wrong DC URL | Click **Connect** to re-authorize; verify the URL matches your Catalyst data center |
| Tools return wrong org/data | DC endpoint mismatch | Update the `url` to your region's endpoint (see `zoho-mcp.md` DC table) |
