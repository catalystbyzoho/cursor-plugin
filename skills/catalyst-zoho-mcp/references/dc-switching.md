## DC Switching — Catalyst Zoho MCP (Cursor)

Use this when the user wants to change the Catalyst MCP data center in Cursor.

Prefer the **`switch-dc`** command when available — it walks through the same steps.

---

## Servers

The plugin ships one native OAuth MCP server per data center; **IN is the default** (listed first). You cannot toggle servers yourself — tell the user to.

| DC | Region | Server | URL |
|----|--------|--------|-----|
| US | United States | `catalyst-by-zoho-us` | `https://catalystbyzoho.zohomcp.com/mcp/message` |
| EU | Europe | `catalyst-by-zoho-eu` | `https://catalystbyzoho.zohomcp.eu/mcp/message` |
| IN | India | `catalyst-by-zoho-in` | `https://catalystbyzoho.zohomcp.in/mcp/message` |
| AU | Australia | `catalyst-by-zoho-au` | `https://catalystbyzoho.zohomcp.com.au/mcp/message` |
| CA | Canada | `catalyst-by-zoho-ca` | `https://catalystbyzoho.zohomcp.ca/mcp/message` |
| SA | Saudi Arabia | `catalyst-by-zoho-sa` | `https://catalystbyzoho.zohomcp.sa/mcp/message` |
| JP | Japan | `catalyst-by-zoho-jp` | `https://catalystbyzoho.zohomcp.jp/mcp/message` |
| UAE | United Arab Emirates | `catalyst-by-zoho-uae` | `https://catalystbyzoho.zohomcp.ae/mcp/message` |

## Procedure

1. Ask which DC (or map the console hostname: `catalyst.zoho.eu` → EU, `.in` → IN, etc.). If the DC is unknown or cannot be determined, **fall back to IN**.
2. Tell the user to open **Customize → Catalyst By Zoho → MCPs**, turn **on** the server for that DC, turn **off** the others, and click **Connect** to complete browser OAuth.
3. Confirm the `ZohoMCP_*` meta-tools are present again.

Do **not** edit files under `~/.cursor/plugins/`.

---

## What to Tell the User After Switching

- Which DC / URL you configured (the server to enable).
- Reload Cursor if the endpoint did not update.
- Re-authorize via **Settings → MCP → Connect** — expected for the new DC.
- The old DC session is not affected.
- Until auth completes, MCP may only expose auth helpers.

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| Hitting the wrong region | Wrong server enabled | Enable only the matching `catalyst-by-zoho-<dc>` server |
| Only auth tools visible after switch | Not yet authorized on the new DC | Settings → MCP → Connect |
| Region / auth errors | Account DC ≠ configured URL | Pick the matching row from the table above |
