---
name: switch-dc
description: Switch the Catalyst by Zoho MCP server to a different data center
argument-hint: <US|EU|IN|AU|CA|SA|JP|UAE>
---
Help the user use the Catalyst by Zoho MCP server for the data center in: $ARGUMENTS

The plugin ships one MCP server per data center. Switching means enabling the matching server and disabling the others. You cannot toggle servers yourself, and you must not edit files under `~/.cursor/plugins/`.

1. Resolve the DC (valid: US, EU, IN, AU, CA, SA, JP, UAE; case-insensitive). If `$ARGUMENTS` is empty or unrecognized, list the table below and ask. Reject unsupported values.

| DC | Region | MCP server name | URL |
|----|--------|-----------------|-----|
| US | United States | `catalyst-by-zoho-us` | `https://catalystbyzoho.zohomcp.com/mcp/message` |
| EU | Europe | `catalyst-by-zoho-eu` | `https://catalystbyzoho.zohomcp.eu/mcp/message` |
| IN | India | `catalyst-by-zoho-in` | `https://catalystbyzoho.zohomcp.in/mcp/message` |
| AU | Australia | `catalyst-by-zoho-au` | `https://catalystbyzoho.zohomcp.com.au/mcp/message` |
| CA | Canada | `catalyst-by-zoho-ca` | `https://catalystbyzoho.zohomcp.ca/mcp/message` |
| SA | Saudi Arabia | `catalyst-by-zoho-sa` | `https://catalystbyzoho.zohomcp.sa/mcp/message` |
| JP | Japan | `catalyst-by-zoho-jp` | `https://catalystbyzoho.zohomcp.jp/mcp/message` |
| UAE | United Arab Emirates | `catalyst-by-zoho-uae` | `https://catalystbyzoho.zohomcp.ae/mcp/message` |

2. Tell the user:
   - Open **Customize → Catalyst By Zoho → MCPs** (or **Cursor Settings → MCP**).
   - Turn **on** the server for their DC and turn **off** the other `catalyst-by-zoho-*` servers.
   - Click **Connect** on that server and finish the Zoho login in the browser.
3. Report the DC, server name and URL. Do not call `ZohoMCP_*` tools until they are available again.

Full reference: `skills/catalyst-zoho-mcp/references/dc-switching.md`
