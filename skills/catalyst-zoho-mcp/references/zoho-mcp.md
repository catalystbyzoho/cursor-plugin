> **Zoho MCP** lets AI assistants (Claude, GitHub Copilot, Cursor, etc.) manage Catalyst infrastructure
> by calling `CatalystbyZoho_*` tools directly. No console clicks or REST API calls needed.

## Setup

> **The Cursor plugin ships with the US data center endpoint pre-configured.** No manual MCP server creation or URL copying is required. If your Catalyst account is on a different DC, update the `url` using the table below before connecting.

## DC Endpoints

| Data Center | MCP Server URL |
|-------------|----------------|
| **US** (default) | `https://catalystbyzoho.zohomcp.com/mcp/message` |
| **EU** | `https://catalystbyzoho.zohomcp.eu/mcp/message` |
| **IN** | `https://catalystbyzoho.zohomcp.in/mcp/message` |
| **AU** | `https://catalystbyzoho.zohomcp.com.au/mcp/message` |
| **CA** | `https://catalystbyzoho.zohomcp.ca/mcp/message` |
| **SA** | `https://catalystbyzoho.zohomcp.sa/mcp/message` |
| **JP** | `https://catalystbyzoho.zohomcp.jp/mcp/message` |

> If the URL for your DC doesn't work, go to [mcp.zoho.com](https://mcp.zoho.com) → **Connect** → copy the exact URL shown for your account.

### For Cursor (plugin — recommended)

The plugin bundles `.mcp.json` pre-configured for US:

```json
{
  "mcpServers": {
    "catalyst-by-zoho": {
      "type": "streamable-http",
      "url": "https://catalystbyzoho.zohomcp.com/mcp/message"
    }
  }
}
```

**To connect:**

1. Install the Catalyst by Zoho Cursor plugin.
2. Open **Cursor Settings → MCP**.
3. Find **catalyst-by-zoho** (may show "Needs authentication").
4. Click **Connect** and complete Zoho OAuth in your browser.
5. Confirm `CatalystbyZoho_*` tools appear in the tool list.

For other DCs, update the `url` in **Cursor Settings → MCP** to the matching endpoint from the table above — or ask the agent to switch it for you.

**Manual override (without the plugin):** Create or edit `.cursor/mcp.json` in your project root with the static URL for your DC. You still authorize via **Connect** in Cursor Settings → MCP — no per-user token URL is needed.

### For Claude Desktop

Edit `claude_desktop_config.json`
(macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
Windows: `%APPDATA%\Claude\claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "catalyst-by-zoho": {
      "type": "streamable-http",
      "url": "https://catalystbyzoho.zohomcp.com/mcp/message"
    }
  }
}
```

### For GitHub Copilot (VS Code)

Create `.vscode/mcp.json` in your workspace root:

```json
{
  "servers": {
    "catalyst-by-zoho": {
      "type": "http",
      "url": "https://catalystbyzoho.zohomcp.com/mcp/message"
    }
  }
}
```

### Verify connection

After installing the plugin and clicking **Connect**, look for tools prefixed with `CatalystbyZoho_` in the tool list. If they appear, the MCP server is connected.

---

## Pre-flight Sequence

Always run these calls before any other MCP operation to set project context:

1. `CatalystbyZoho_List_All_Organizations` → get your org ID
2. `CatalystbyZoho_List_All_Projects` (with org ID) → get your project ID
3. `CatalystbyZoho_List_All_Tables` → verify access and get table names

If there is more than one org or project, ask the user which one to use before proceeding.

---

## Available Tools

The tools available depend on which Catalyst tools are configured on the MCP server. Confirmed tool names:

| Tool | Description |
|------|-------------|
| `CatalystbyZoho_List_All_Organizations` | List all Zoho organizations the account has access to |
| `CatalystbyZoho_List_All_Projects` | List all Catalyst projects in the organization |
| `CatalystbyZoho_List_All_Tables` | List all Data Store tables in the project |
| `CatalystbyZoho_List_Cache_Segments` | List all Cache segments in the project |

For the full catalog of available tools, check your AI client's tool list after connecting — all tools shown with the `CatalystbyZoho_` prefix are available to use.

---

## Common Patterns

### Create a table from schema description

> "Create a Tasks table with columns: Title (text, required), DueDate (date), Status (text), Priority (integer)"

The AI will call the appropriate `CatalystbyZoho_*` create-table tool with column specifications.

### Query data

> "Show me all rows in the Tasks table where Status is 'In Progress'"

The AI calls the query tool with a ZCQL query against the correct table ID.

### Schema exploration

> "What tables do I have and what are their columns?"

The AI calls `CatalystbyZoho_List_All_Tables` then describes the schema.

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `CatalystbyZoho_*` tools not showing | MCP not authorized or plugin not installed | Open **Cursor Settings → MCP** → click **Connect** on `catalyst-by-zoho` |
| "Needs authentication" in MCP settings | OAuth not completed | Click **Connect** and complete Zoho login in the browser |
| Tools show but return no data / wrong org | URL is from a different DC than your Catalyst account | Update the `url` to the correct DC endpoint from the table above |
| `PERMISSION_NEEDED` on table operations | Project context not set | Run `CatalystbyZoho_List_All_Organizations` → `List_All_Projects` first |
| Operations applying to wrong project | Skipped pre-flight | Always run the org → project → verify sequence before any operation |
| MCP server shows red/error | OAuth session expired | Click **Connect** again in **Cursor Settings → MCP** to re-authorize |
| MCP targets wrong environment | Zoho MCP defaults to Development | Switch environment explicitly in the Zoho MCP console if production is needed (use caution) |
