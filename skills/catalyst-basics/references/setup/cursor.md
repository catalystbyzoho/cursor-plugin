# Using Catalyst Skills with Cursor

> Shared steps — Installation, MCP authorization, and the pre-flight checklist — are in `setup-common.md`. This file covers Cursor-specific verification and troubleshooting.

## Skill Activation

This Cursor plugin registers skills automatically. To verify:

1. Open Cursor in your Catalyst project directory
2. Open Agent / Composer
3. Ask: "What Catalyst skills are available?"

You should see routing from `catalyst-by-zoho` and the specialized service skills.

## MCP Setup — Confirm the plugin server

First complete the MCP steps in `setup-common.md` (Configure `CATALYST_MCP_TLD`, then Connect). The plugin ships `.mcp.json` with `…zohomcp.${CATALYST_MCP_TLD}/…` — you normally do **not** need a project-level MCP file.

Optional project override — create `.cursor/mcp.json` only if Plugins → Configure is unavailable or you need a personal Zoho MCP URL. See `../../catalyst-zoho-mcp/references/dc-switching.md`.

Confirm MCP is active under **Cursor Settings → MCP** (green / connected next to `catalyst-by-zoho`).

## Session start hook

The plugin registers a `sessionStart` hook (`hooks/catalyst-context.js`) that:

- Reads `.catalystrc` and injects org / project / environment into agent context
- Surfaces CLI / Node / runtime prerequisite gaps
- Warns when the active environment looks like Production

If the hook does not appear to fire, check **Cursor Settings → Hooks** and the Hooks output channel.

## Common Errors (Cursor)

See `setup-common.md` for errors common to setup. Cursor-specific:

| Error | Cause | Fix |
|-------|-------|-----|
| MCP not connecting | Plugin not installed or OAuth incomplete | Install the plugin, then Cursor Settings → MCP → Connect |
| MCP shows needs authentication | Token expired or DC mismatch | Re-Connect; if region errors persist, Plugins → Configure → correct `CATALYST_MCP_TLD` (see `../../catalyst-zoho-mcp/references/dc-switching.md`) |
| Skills not listed | Plugin not loaded | Confirm under Cursor Settings → Plugins; reload window |
| Hook context missing | sessionStart race / hook disabled | Prefer rules + preflight gate; check Hooks output channel |
