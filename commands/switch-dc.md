---
name: switch-dc
description: Switch the Catalyst by Zoho MCP server to a different data center
argument-hint: <US|EU|IN|AU|CA|SA|JP|UAE>
---
Help the user use the Catalyst by Zoho MCP server for the data center in: $ARGUMENTS

The plugin ships one MCP server per data center. Switching means enabling the matching server and disabling the others. You cannot toggle servers yourself, and you must not edit files under `~/.cursor/plugins/`.

1. Resolve the DC (valid: US, EU, IN, AU, CA, SA, JP, UAE; case-insensitive). If `$ARGUMENTS` is empty or unrecognized, default to **IN** and ask the user to confirm (or choose another DC). Reject unsupported values.

Server names and URLs: read the DC table in `skills/catalyst-zoho-mcp/references/dc-switching.md` (single source of truth). Server name format: `catalyst-by-zoho-<dc>` (us, eu, in, au, ca, sa, jp, uae).

2. Tell the user:
   - Open **Customize → Catalyst By Zoho → MCPs** (or **Cursor Settings → MCP**).
   - Turn **on** the server for their DC and turn **off** the other `catalyst-by-zoho-*` servers.
   - Click **Connect** on that server and finish the Zoho login in the browser.
3. Report the DC, server name and URL. Do not call `ZohoMCP_*` tools until they are available again.

Full reference: `skills/catalyst-zoho-mcp/references/dc-switching.md`
