> **Zoho MCP** lets Cursor manage Catalyst infrastructure by invoking `CatalystbyZoho_*`
> operations through the `ZohoMCP_executeTool` meta-tool. No console clicks or REST API calls
> needed. (The `CatalystbyZoho_*` names are never callable tools on their own — see
> "How to Call Tools Correctly" below.)

---

## Setup — Plugin MCP (recommended)

This Cursor plugin ships `.mcp.json` with `"url": "https://catalystbyzoho.zohomcp.${CATALYST_MCP_TLD}/mcp/message"`. The TLD comes from the plugin variable **Catalyst MCP data center** (`com` / US by default).

**Step 1 — Pick your Data Center:**

1. Open **Cursor Settings → Plugins → Catalyst by Zoho → Configure**
2. Set **Catalyst MCP data center** (`CATALYST_MCP_TLD`) to your account region TLD:

| DC | Region | `CATALYST_MCP_TLD` | MCP URL |
|------|------|------|------|
| US | United States | `com` | `https://catalystbyzoho.zohomcp.com/mcp/message` |
| EU | Europe | `eu` | `https://catalystbyzoho.zohomcp.eu/mcp/message` |
| IN | India | `in` | `https://catalystbyzoho.zohomcp.in/mcp/message` |
| AU | Australia | `com.au` | `https://catalystbyzoho.zohomcp.com.au/mcp/message` |
| CA | Canada | `ca` | `https://catalystbyzoho.zohomcp.ca/mcp/message` |
| SA | Saudi Arabia | `sa` | `https://catalystbyzoho.zohomcp.sa/mcp/message` |
| JP | Japan | `jp` | `https://catalystbyzoho.zohomcp.jp/mcp/message` |
| UAE | United Arab Emirates | `ae` | `https://catalystbyzoho.zohomcp.ae/mcp/message` |

To change later, use Configure again, the **`switch-dc`** command, or `dc-switching.md`.

**Step 2 — Authorize in Cursor:**

1. Open **Cursor Settings → MCP**
2. Find **catalyst-by-zoho**
3. Click **Connect** and complete Zoho OAuth in the browser

Optional project override — only if Configure is unavailable or you need a personal / custom URL — create `.cursor/mcp.json` with a literal `url` for your DC.

**Step 3 — Verify:**
Look for the `ZohoMCP_*` **meta-tools** in Cursor's tool list — `ZohoMCP_getSchema`,
`ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`. Their presence means MCP is
connected. (The `CatalystbyZoho_*` operations are *not* listed as tools — they are `tool_name`
values you pass to `ZohoMCP_executeTool`.) Done.

---

## Pre-flight Sequence

Before your first MCP tool call, complete the canonical **workspace readiness gate** once per session: `../../catalyst-basics/references/preflight.md`. It establishes org/project (from `.catalystrc` + a single `CatalystbyZoho_Get_Project_By_Id` reconciliation, or the `List_All_Organizations` → `List_All_Projects` fallback when `.catalystrc` is absent) and confirms access with `List_All_Tables` before DataStore work. Once it passes, trust it and operate freely — do not re-run it before every call.

---

## How to Call Tools Correctly

**Rule: always call `ZohoMCP_getSchema` before `ZohoMCP_executeTool` for any tool you haven't called before.** Most `CatalystbyZoho_*` tools require `path_variables` (e.g. `project_id`) that are invisible without the schema — guessing the arguments causes "Mandatory path variable not present" errors.

### Step 1 — Get the schema

`ZohoMCP_getSchema` takes `query_params`, **not** `body`:

```
ZohoMCP_getSchema({
  query_params: { tool_name: "CatalystbyZoho_List_All_Functions" }
})
```

> ⚠️ Passing `body: { tool_name: "..." }` instead of `query_params` returns "tool_name is required" — this is the wrong parameter location.

### Step 2 — Call the tool

`ZohoMCP_executeTool` always takes a `body` with this shape:

```
ZohoMCP_executeTool({
  body: {
    tool_name: "CatalystbyZoho_List_All_Functions",
    arguments: {
      path_variables: { project_id: "31594000000127002" },
      headers: {},
      body: {}
    }
  }
})
```

- `path_variables` — URL path segments the tool requires (get names from the schema)
- `headers` — extra HTTP headers (usually empty `{}`)
- `body` — request payload for POST/PUT tools (empty `{}` for GET-style tools)

Tools with no required path variables (e.g. `List_All_Organizations`, `List_All_Projects`) can be called with `arguments: {}`.

---

## Available Tools

The operations available depend on which Catalyst tools are configured in your Zoho MCP server. Each is invoked by passing its name as the `tool_name` argument to `ZohoMCP_executeTool` — the names below are those `tool_name` values, not tools that appear in your client's tool list. Confirmed operation names:

| Tool | Description |
|------|-------------|
| `CatalystbyZoho_List_All_Organizations` | List all Zoho organizations the account has access to |
| `CatalystbyZoho_List_All_Projects` | List all Catalyst projects in the organization |
| `CatalystbyZoho_List_All_Tables` | List all Data Store tables in the project |
| `CatalystbyZoho_List_All_Segments` | List all Cache segments in the project |
| `CatalystbyZoho_List_All_Jobpools` | List all Job Scheduling pools in the project |
| `CatalystbyZoho_Create_Job_Pool` | Create a new Job Scheduling pool |

For the full catalog of available operations, call `ZohoMCP_listTools` (or `ZohoMCP_getFeatures`) after connecting — these enumerate every `CatalystbyZoho_*` `tool_name` the server exposes. Do **not** expect the `CatalystbyZoho_*` names to appear in your AI client's visible tool list; only the `ZohoMCP_*` meta-tools appear there.

---

## MCP-First Workflow

### Golden Rule: "MCP First, Console Fallback"

When an AI agent needs to create or manage Catalyst infrastructure (tables, cache segments, buckets, job pools), **always try MCP tools first**. Only fall back to the Catalyst Console UI if MCP is unavailable or fails.

| Approach | Time | Repeatable | Auditable |
|----------|------|-----------|-----------|
| ✅ MCP tools | ~30 seconds | Yes | Yes (in conversation) |
| ❌ Console UI | 5+ minutes | No | No |

### Decision Tree

```
Need to create Catalyst infrastructure?
        │
        ▼
Are the ZohoMCP_* meta-tools present in tool list?
        │
   YES──┘──NO
   │          │
   ▼          ▼
Use MCP    Guide user to set up Zoho MCP first
tools      (see Setup section above)
  ✅        Then retry with MCP tools
```

**Only instruct manual Console steps when:**
- MCP config is not set up AND user cannot set it up right now
- MCP tools fail with an unresolvable error
- User explicitly requests a manual UI walkthrough

### Example: Table Creation

❌ **Manual Console (5+ minutes)**
```
1. Open https://console.catalyst.zoho.com
2. Navigate to project → Data Store
3. Click Create Table, enter name
4. Add each column manually via the UI
5. Click Create
```

✅ **MCP (two calls — table, then its columns)**

`Create_Table` takes only `table_name` + `table_scope`; columns are a **separate** `Create_Column` call (a batch array) against the new table's ID. Both go through `ZohoMCP_executeTool` with `projectId` + `Catalyst-org` + `Environment`.

```javascript
// 1) Create the table (no inline columns)
ZohoMCP_executeTool({ body: {
  tool_name: "CatalystbyZoho_Create_Table",
  arguments: {
    path_variables: { projectId: "<projectId>" },
    headers: { "Catalyst-org": <orgId>, "Environment": "Development" },
    body: { table_name: "Todos", table_scope: "GLOBAL" }
  }
}})

// 2) Add columns in one batch, using the table id returned above
ZohoMCP_executeTool({ body: {
  tool_name: "CatalystbyZoho_Create_Column",
  arguments: {
    path_variables: { projectId: "<projectId>", id: "<tableId>" },
    headers: { "Catalyst-org": <orgId>, "Environment": "Development" },
    body: [
      { column_name: "title", data_type: "text", is_mandatory: "true", audit_consent: "false" },
      { column_name: "completed", data_type: "boolean", is_mandatory: "false", search_index_enabled: "false", default_value: "false", audit_consent: "false" }
    ]
  }
}})
```

See `mcp-datastore.md` for the full column-type field rules.

---

## Common Patterns

### Create a table from schema description

> "Create a Tasks table with columns: Title (text, required), DueDate (date), Status (text), Priority (integer)"

The AI calls `CatalystbyZoho_Create_Table` (name + scope), then `CatalystbyZoho_Create_Column` with the batch of column specs against the new table ID.

### Query data

> "Show me all rows in the Tasks table where Status is 'In Progress'"

The AI calls the query tool with a ZCQL query against the correct table ID.

### Schema exploration

> "What tables do I have and what are their columns?"

The AI calls `CatalystbyZoho_List_All_Tables` then describes the schema.

### Submit an immediate job

> "Run ProcessOrderFunction now as a job"

**Required pre-flight — always do this before calling `CatalystbyZoho_Create_Immediate_Job`:**
1. Call `CatalystbyZoho_List_All_Jobpools` to get existing pools and their IDs.
2. If no pools exist, call `CatalystbyZoho_Create_Job_Pool` first (type `"Function"`, memory e.g. `"256"`).
3. Pass the `jobpool_id` from step 1 or 2 to `CatalystbyZoho_Create_Immediate_Job`.

`jobpool_id` is a required field — there is no default or fallback. Job submission fails immediately if it is omitted.

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `ZohoMCP_*` meta-tools not showing | MCP server not connected or URL wrong | Verify URL in client config; restart client after saving. (The `CatalystbyZoho_*` names never appear in the tool list — only the `ZohoMCP_*` meta-tools do.) |
| `PERMISSION_NEEDED` on table operations | Project context not set | Run `CatalystbyZoho_List_All_Organizations` → `List_All_Projects` first |
| Operations applying to wrong project | Skipped pre-flight | Always run the org → project → verify sequence before any operation |
| MCP server shows red/error *(Option B)* | Token expired or URL invalid | Regenerate the authenticated URL at mcp.zoho.com |
| Browser auth loop not completing *(Option A)* | AI client doesn't support OAuth 2.0 browser flow | Check client version supports MCP 2025-03; try a different supported client |
| MCP targets wrong environment | Zoho MCP defaults to Development | Switch environment explicitly in the Zoho MCP console if production is needed (use caution) |
| `INVALID_INPUT: job_name must contain only alphanumeric and underscore` on `CatalystbyZoho_Create_Immediate_Job` | `job_name` contains hyphens or spaces | Use underscores only — `doc_audit_run_1` not `doc-audit-run-1` |
| Job submission fails with missing field error | `jobpool_id` not provided to `CatalystbyZoho_Create_Immediate_Job` | Call `CatalystbyZoho_List_All_Jobpools` first; if none exist, call `CatalystbyZoho_Create_Job_Pool` then use the returned ID |
