---
description: Catalyst by Zoho behavioral rules and MCP routing — apply when building, deploying, or managing Catalyst serverless projects.
alwaysApply: true
---

# Catalyst by Zoho — Agent Rules

> This file provides (1) MCP tool routing when Zoho MCP is connected, and (2) behavioral rules
> for Catalyst projects. For full service coverage, SDK patterns, and architecture guidance,
> start at `skills/catalyst-by-zoho/SKILL.md` and load the routed skill from there.

---

## MCP Tool Usage

- The plugin ships a pre-configured MCP endpoint (`https://catalystbyzoho.zohomcp.${CATALYST_MCP_TLD}/mcp/message`); the user picks the data center TLD under **Plugins → Catalyst by Zoho → Configure**, then authorizes via **Cursor Settings → MCP → Connect**.
- Leverage Catalyst services for serverless compute, relational data storage, object storage, AI/ML, and workflow orchestration.
- When the Zoho MCP `ZohoMCP_*` meta-tools (`ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`) are available in your tool list, MCP is connected — use it for infrastructure operations (create tables, query data, manage buckets/cache) instead of asking the user to do it manually in the console. Each operation is a `CatalystbyZoho_*` name passed as the `tool_name` argument to `ZohoMCP_executeTool` (fetch its schema first with `ZohoMCP_getSchema`); the `CatalystbyZoho_*` names are never shown as tools themselves.
- **MCP pre-flight — follow the single canonical sequence:** `skills/catalyst-basics/references/preflight.md`. Run it **once per session** (not before every call — trust it once it passes). In short: establish the active org (`.catalystrc` → `projects[].env[].id`) and project (`projects[].id`), then confirm the MCP account and CLI agree via `CatalystbyZoho_Get_Project_By_Id` before you start operating. If `.catalystrc` is absent, resolve via `List_All_Organizations` → `List_All_Projects`. Do not restate the steps here — that file is authoritative.
- Always default to `"Development"` environment unless the user explicitly requests production.
- If `List_All_Tables` returns `PERMISSION_NEEDED`, ask the user for the correct project ID from their Catalyst console URL (format: `.../project/<project_id>/...`).
- If the user asks to create tables, query data, manage cache, or set up infrastructure — use MCP tools directly instead of asking them to go to the console.

---

## Skill Discovery

Before starting any Catalyst task, read `skills/catalyst-by-zoho/SKILL.md` and load the most specific matching skill:

| Task type | Load this skill |
|-----------|-----------------|
| Architecture / "what should I use for X", DC restrictions | `skills/catalyst-basics/` → `references/architecture.md` |
| Project setup, CLI, `.catalystrc`, `catalyst.json`, IDs, environments | `skills/catalyst-basics/` |
| Functions, handlers, API Gateway, `catalyst-config.json` | `skills/catalyst-functions/` |
| AppSail, Docker, managed runtimes, PORT variable | `skills/catalyst-appsail/` |
| Slate frontend hosting, `slate-config.toml` | `skills/catalyst-slate/` |
| Data Store, CRUD, ZCQL, permissions | `skills/catalyst-datastore/` |
| Stratus object storage, signed URLs, multipart | `skills/catalyst-stratus/` |
| NoSQL tables, document storage | `skills/catalyst-nosql/` |
| Authentication, ZAID, Connections/OAuth | `skills/catalyst-authentication/` |
| Cache segments, TTL, key-value ops | `skills/catalyst-cache/` |
| Signals — event-driven triggers, pub/sub | `skills/catalyst-signals/` |
| SmartBrowz — headless browser, PDF, Browser Logic, Dataverse | `skills/catalyst-smartbrowz/` |
| Pricing, free tier, cost estimation | `skills/catalyst-pricing/` |
| SDKs (Node.js, Web, Python, Java, mobile) | `skills/catalyst-sdk/` |
| Zia Services, QuickML, OCR/ML | `skills/catalyst-zia/` |
| MCP setup, `CatalystbyZoho_*` via `ZohoMCP_executeTool` | `skills/catalyst-zoho-mcp/` → `references/zoho-mcp.md` |
| Skill gave wrong or outdated guidance | `skills/catalyst-by-zoho/references/skill-feedback.md` |

If the loaded skill's reference files do not contain the answer, use a site-scoped web search (`site:docs.catalyst.zoho.com <term>`) and fetch the specific page URL returned. Do NOT fabricate docs URLs — all Catalyst documentation lives under `https://docs.catalyst.zoho.com/en/`.

---

## CLI Usage — always non-interactive

- The `catalyst` CLI is interactive by default (arrow-key menus, prompts). Agents drive TUIs poorly, so **always run it in non-interactive (NI) mode.**
- Enable NI mode any of three equivalent ways (requires CLI v1.27.0+): the `-ni` flag, the `--non-interactive` flag, or the env var `ZCATALYST_NON_INTERACTIVE=1`.
- **Pass every input as a flag** so the CLI never has to prompt — e.g. `catalyst init --org <orgId> -p <projectId> -ni`, `catalyst functions:add --name <name> --type <type> --stack <stack> -ni`. The required flags per command are in `skills/catalyst-basics/references/cli.md`.
- Interactive prompts exist for humans; **agents must never rely on them.** The one unavoidable exception is `catalyst login`, which opens a browser for OAuth — use `catalyst login --dc <dc> -ni` to skip the DC-selection prompt, but the browser sign-in itself needs the user.

---

## Project Initialization

- Before creating any files, check for `.catalystrc` and `catalyst.json` in the working directory.
- If **both exist**: project is already initialized — do NOT re-scaffold or overwrite them.
- If either file is missing: follow `skills/catalyst-basics/references/preflight.md`. Resolve and confirm org/project with the user (or from `.catalystrc` when present), then link with `catalyst login --dc <dc> -ni` and `catalyst init --org <orgId> -p <projectId> -ni`. Never create `.catalystrc` / `catalyst.json` by hand; never use interactive `catalyst init`.
- NI mode can only **link** an existing project. If no project exists yet, tell the user to create one in the Catalyst console first.

---

## Code Conventions

- Follow Catalyst's strict directory layout: functions go under `functions/`, web client under `client/`, `catalyst.json` at project root.
- Always use the correct handler signature per function type — consult `skills/catalyst-functions/references/functions-basics.md` before writing any function code.
- For AppSail, always use `process.env.X_ZOHO_CATALYST_LISTEN_PORT` with a fallback port (e.g., `|| 9000`).
- Prefer MCP (`ZohoMCP_executeTool` + `CatalystbyZoho_*` operations) for resource IDs (Table ID, ZAID, Segment ID, bucket names). Never leave unexplained placeholders — if MCP cannot resolve an ID, say so and tell the user where to find it. Org/project selection still follows the pre-flight Golden Rule (confirm; never assume).
- For new projects, never recommend the deprecated **standalone services** — File Store, Event Listeners, and the standalone Cron scheduler. Use **Stratus** (storage), **Signals** (data-change triggers), and **Job Scheduling** (scheduled execution) instead. Note this is distinct from the function *types*: the **Event function type** is still current (trigger it with Signals, not Event Listeners), and scheduled work should use the **Job function type** with Job Scheduling rather than the legacy Cron function type.

---

## Deployment Preferences

- Prefer `catalyst deploy -ni` from the CLI over manual console uploads (always non-interactive — see the CLI Usage rule).
- Always verify project structure matches Catalyst's requirements before suggesting deploy.
- After deployment, recommend checking **DevOps → Logs** for execution errors on first invocation.
- For scheduled (Job Scheduling) and event-driven (Signals) functions, proactively suggest configuring Application Alerts.

---

## Verification

- After writing code, verify it matches Catalyst's expected project structure and handler signatures.
- After infrastructure creation via MCP tools, verify access with a read operation (e.g., `List_All_Tables`) before performing writes.
- Never leave placeholder IDs unexplained — resolve via MCP when connected; otherwise tell the user where to find the real value.
