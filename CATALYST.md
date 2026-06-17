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

- The plugin ships a pre-configured MCP endpoint; the user authorizes via **Cursor Settings → MCP → Connect** (no per-user URL setup required).
- Leverage Catalyst services for serverless compute, relational data storage, object storage, AI/ML, and workflow orchestration.
- When Zoho MCP tools (`CatalystbyZoho_*`) are available in your tool list, use them for infrastructure operations (create tables, query data, manage buckets/cache) instead of asking the user to do it manually in the console.
- **Mandatory MCP pre-flight — Org → Project → Verify → Operate:**
  1. `List_All_Organizations` → get org `id` (if multiple orgs, ask the user which one)
  2. `List_All_Projects` (with `Catalyst-org` header) → get project `id` (if multiple projects, ask the user)
  3. `List_All_Tables` → verify access works before any write operations
  4. Only then proceed with create/update operations
- If `.catalystrc` exists locally, read it first for the authoritative `project_id` and `env_id`, then cross-check with the MCP calls above.
- Always default to `"Development"` environment unless the user explicitly requests production.
- If `List_All_Tables` returns `PERMISSION_NEEDED`, ask the user for the correct project ID from their Catalyst console URL (format: `.../project/<project_id>/...`).
- If the user asks to create tables, query data, manage cache, or set up infrastructure — use MCP tools directly instead of asking them to go to the console.

---

## Skill Discovery

Before starting any Catalyst task, read `skills/catalyst-by-zoho/SKILL.md` and load the most specific matching skill:

| Task type | Load this skill |
|-----------|-----------------|
| Architecture / "what should I use for X" | `skills/catalyst-basics/` → `references/architecture.md` |
| Project setup, CLI, `.catalystrc`, `catalyst.json` | `skills/catalyst-basics/` |
| Functions, handlers, API Gateway, `catalyst-config.json` | `skills/catalyst-functions/` |
| AppSail, Docker, managed runtimes, PORT variable | `skills/catalyst-appsail/` |
| Slate frontend hosting, `slate-config.toml` | `skills/catalyst-slate/` |
| Data Store, CRUD, ZCQL, permissions | `skills/catalyst-datastore/` |
| Stratus object storage, signed URLs, multipart | `skills/catalyst-stratus/` |
| NoSQL collections, document storage | `skills/catalyst-nosql/` |
| Authentication, ZAID, Connections/OAuth | `skills/catalyst-authentication/` |
| Cache segments, TTL, key-value ops | `skills/catalyst-cache/` |
| Pricing, free tier, cost estimation | `skills/catalyst-pricing/` |
| SDKs (Node.js, Web, Python, Java, mobile) | `skills/catalyst-sdk/` |
| Zia Services, QuickML, OCR/ML | `skills/catalyst-zia/` |
| MCP setup, `CatalystbyZoho_*` tool calls | `skills/catalyst-zoho-mcp/` → `references/zoho-mcp.md` |
| Skill gave wrong or outdated guidance | `skills/catalyst-by-zoho/references/skill-feedback.md` |

If the loaded skill's reference files do not contain the answer, use a site-scoped web search (`site:docs.catalyst.zoho.com <term>`) and fetch the specific page URL returned. Do NOT fabricate docs URLs — all Catalyst documentation lives under `https://docs.catalyst.zoho.com/en/`.

---

## Project Initialization

- Before creating any files, check for `.catalystrc` and `catalyst.json` in the working directory.
- If **both exist**: project is already initialized — do NOT re-scaffold or overwrite them.
- If `catalyst.json` exists but `.catalystrc` is missing: project is not linked — instruct the user to run `catalyst login` then `catalyst init`.
- Only scaffold new project files if neither file exists.

---

## Code Conventions

- Follow Catalyst's strict directory layout: functions go under `functions/`, web client under `client/`, `catalyst.json` at project root.
- Always use the correct handler signature per function type — consult `skills/catalyst-functions/references/` before writing any function code.
- For AppSail, always use `process.env.X_ZOHO_CATALYST_LISTEN_PORT` with a fallback port (e.g., `|| 9000`).
- When writing code that uses any Catalyst ID (Table ID, ZAID, Segment ID, Org ID, Project ID), always add an inline comment telling the user exactly where to find it in the console. Never leave ID placeholders unexplained.
- Never recommend deprecated components (File Store, Event Listeners, Cron) for new projects. Use Stratus, Signals, and Job Scheduling instead.

---

## Deployment Preferences

- Prefer `catalyst deploy` from the CLI over manual console uploads.
- Always verify project structure matches Catalyst's requirements before suggesting deploy.
- After deployment, recommend checking **DevOps → Logs** for execution errors on first invocation.
- For cron jobs and event listeners, proactively suggest configuring Application Alerts.

---

## Verification

- After writing code, verify it matches Catalyst's expected project structure and handler signatures.
- After infrastructure creation via MCP tools, verify access with a read operation (e.g., `List_All_Tables`) before performing writes.
- Never leave placeholder IDs unexplained — always tell the user where to find the real value in the console.
