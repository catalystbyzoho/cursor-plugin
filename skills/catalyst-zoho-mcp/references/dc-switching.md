## DC Switching — Catalyst Zoho MCP (Cursor)

Use this when the user wants to change the Catalyst MCP data center in Cursor.

Prefer the **`switch-dc`** command when available — it walks through the same steps.

---

## DC TLD Map

These are the allowed values for the plugin variable `CATALYST_MCP_TLD`:

| DC | Region | `CATALYST_MCP_TLD` | Resolved MCP URL |
|----|--------|--------------------|------------------|
| US | United States | `com` | `https://catalystbyzoho.zohomcp.com/mcp/message` |
| EU | Europe | `eu` | `https://catalystbyzoho.zohomcp.eu/mcp/message` |
| IN | India | `in` | `https://catalystbyzoho.zohomcp.in/mcp/message` |
| AU | Australia | `com.au` | `https://catalystbyzoho.zohomcp.com.au/mcp/message` |
| CA | Canada | `ca` | `https://catalystbyzoho.zohomcp.ca/mcp/message` |
| SA | Saudi Arabia | `sa` | `https://catalystbyzoho.zohomcp.sa/mcp/message` |
| JP | Japan | `jp` | `https://catalystbyzoho.zohomcp.jp/mcp/message` |
| UAE | United Arab Emirates | `ae` | `https://catalystbyzoho.zohomcp.ae/mcp/message` |

---

## Primary method — plugin Configure (recommended)

The plugin ships `.mcp.json` with `"url": "https://catalystbyzoho.zohomcp.${CATALYST_MCP_TLD}/mcp/message"`. Cursor substitutes the TLD the user sets under Plugins → Configure.

1. Ask which DC (or map `$ARGUMENTS` / console hostname: `catalyst.zoho.eu` → EU / `eu`, `.in` → IN / `in`, etc.).
2. Instruct the user:
   - **Cursor Settings → Plugins → Catalyst by Zoho → Configure**
   - Set **Catalyst MCP data center** to the matching TLD from the table
   - Save, then **Developer: Reload Window** if needed
3. **Cursor Settings → MCP → catalyst-by-zoho → Connect** and complete browser OAuth for the new DC.
4. Confirm the `ZohoMCP_*` meta-tools are present again.

Do **not** edit marketplace/plugin cache files under `~/.cursor/plugins/cache/`.

---

## Fallback — project override

Only if Plugins → Configure is unavailable. Create or update `.cursor/mcp.json` in the workspace:

```json
{
  "mcpServers": {
    "catalyst-by-zoho": {
      "type": "streamable-http",
      "url": "https://catalystbyzoho.zohomcp.eu/mcp/message"
    }
  }
}
```

Replace the TLD in the `url` with the chosen DC. Reload Cursor, then Connect under Settings → MCP.

---

## What to Tell the User After Switching

- Which DC / TLD they should have selected (or that you wrote into `.cursor/mcp.json`).
- Reload Cursor if the endpoint did not update.
- Re-authorize via **Settings → MCP → Connect** — expected for the new DC.
- The old DC session is not affected.
- Until auth completes, MCP may only expose auth helpers.

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| Still hitting US / wrong region | Variable not changed or window not reloaded | Plugins → Configure → set `CATALYST_MCP_TLD`, then Reload Window |
| Only auth tools visible after switch | Not yet authorized on the new DC | Settings → MCP → Connect |
| Region / auth errors | Account DC ≠ configured TLD | Pick the matching row from the table above |
| Configure UI missing | Older / local-only install | Use the project `.cursor/mcp.json` fallback |
