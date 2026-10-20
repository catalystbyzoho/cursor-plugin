# Catalyst Skills — Common Setup Steps

Shared Cursor setup for this plugin. Skill activation and MCP authorization details are in `cursor.md`.

## Installation

Install the **Catalyst by Zoho** Cursor plugin:

1. **Marketplace (recommended):** Cursor Settings → Plugins → search for Catalyst by Zoho.
2. **Local testing:** copy this repo to `~/.cursor/plugins/local/catalyst-by-zoho`, then reload Cursor.
3. **Git URL (if supported):** install from `https://github.com/catalystbyzoho/cursor-plugin`.

Skills, rules, hooks, and the bundled MCP server load with the plugin — no separate skills clone is required.

## MCP Setup — Pick DC, then authorize

This plugin ships one MCP server per data center (`catalyst-by-zoho-us`, `-eu`, `-in`, …).

1. **Choose your data center:** run **`switch-dc`** (or in **Customize → Catalyst By Zoho → MCPs**, enable the server for your DC and disable the others).
2. Open **Cursor Settings → MCP**.
3. Find **catalyst-by-zoho** (may show "Needs authentication").
4. Click **Connect** and complete Zoho OAuth in your browser.
5. Confirm the `ZohoMCP_*` meta-tools appear in the tool list.

To change regions later, run `switch-dc` or see `../../catalyst-zoho-mcp/references/dc-switching.md`.

**Personal / custom MCP server (optional):** create a server at [mcp.zoho.com](https://mcp.zoho.com), add **Catalyst by Zoho** under Tools, set Connections to **On Demand**, copy the URL, and put it in project `.cursor/mcp.json` or Cursor Settings → MCP.

## Pre-flight Checklist

Before asking Cursor to write Catalyst code, ensure:

- [ ] `catalyst login --dc <dc> -ni` has been run in your terminal
- [ ] `catalyst init --org <orgId> -p <projectId> -ni` has been run in the project directory
- [ ] `.catalystrc` and `catalyst.json` exist at the project root
- [ ] **Catalyst MCP data center** matches your account region (run `switch-dc` if not US)
- [ ] **catalyst-by-zoho** shows connected in **Cursor Settings → MCP** (the `CatalystbyZoho_*` operations are invoked via `ZohoMCP_executeTool`, not shown as tools)

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| AI writes files without a project | `.catalystrc` or `catalyst.json` missing | Run `catalyst login --dc <dc> -ni` then `catalyst init --org <orgId> -p <projectId> -ni` |
| Wrong project context | Multiple projects in `.catalystrc` | Run `catalyst project:use <project-name> -ni` first |
| MCP needs authentication | OAuth not completed / expired | Cursor Settings → MCP → Connect on **catalyst-by-zoho** |
| Region / auth errors on wrong DC | MCP URL does not match account region | Run `switch-dc` for the correct data center |
