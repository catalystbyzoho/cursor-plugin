# Catalyst by Zoho — Cursor Plugin

Official [Cursor plugin](https://cursor.com/docs/plugins) for [Catalyst by Zoho](https://catalyst.zoho.com) — Zoho's full-stack serverless cloud platform.

This plugin gives Cursor deep knowledge of Catalyst services, SDK patterns, CLI workflows, and architecture guidance. With Zoho MCP connected, the agent can also manage Catalyst infrastructure (tables, cache, buckets, ZCQL) directly from chat.

## What's included

| Component | Path | Description |
|-----------|------|-------------|
| **Rules** | `CATALYST.md` | Always-on behavioral rules and MCP routing for Catalyst projects |
| **Skills** | `skills/` | Specialized agent skills (functions, AppSail, Data Store, Stratus, Slate, Signals, SmartBrowz, SDKs, pricing, and more) |
| **Hooks** | `hooks/` | `sessionStart` context bootstrap from `.catalystrc` + local toolchain checks |
| **Commands** | `commands/` | Agent commands (e.g. `switch-dc` guides enabling your DC's MCP server) |
| **MCP** | `.mcp.json` | One Zoho MCP server per data center (enable yours) |
| **Manifest** | `.cursor-plugin/plugin.json` | Plugin metadata, including the DC TLD `variables` enum |

### Skills

| Skill | Description |
|-------|-------------|
| `catalyst-by-zoho` | Skill router — start here; loads the most specific skill for the task |
| `catalyst-basics` | Architecture, project setup, CLI, environments |
| `catalyst-functions` | Serverless functions, handlers, API Gateway |
| `catalyst-appsail` | Backend PaaS, Docker, managed runtimes |
| `catalyst-slate` | Frontend hosting, frameworks, Git deploy |
| `catalyst-datastore` | Relational Data Store, ZCQL, permissions |
| `catalyst-stratus` | S3-compatible object storage |
| `catalyst-nosql` | Document storage, collections |
| `catalyst-cache` | In-memory key-value cache |
| `catalyst-authentication` | User auth, ZAID, Connections/OAuth |
| `catalyst-signals` | Event-driven triggers, pub/sub (replaces Event Listeners) |
| `catalyst-smartbrowz` | Headless browser, PDF/screenshot, Browser Logic, Dataverse |
| `catalyst-pricing` | Free tier, pay-as-you-go, cost estimation |
| `catalyst-sdk` | Node.js, Web, Python, Java, mobile SDKs |
| `catalyst-zia` | Zia Services, QuickML, OCR/ML |
| `catalyst-zoho-mcp` | MCP setup and `CatalystbyZoho_*` via `ZohoMCP_executeTool` |

> **Note:** For edge-case lookups not covered by skill reference files, agents are instructed to search `docs.catalyst.zoho.com` and fetch individual pages — keeping the plugin lean while ensuring up-to-date answers.

## Installation

### Option 1: Cursor Marketplace (recommended)

When published, install from **Cursor Settings → Plugins** or the [Cursor Marketplace](https://cursor.com/marketplace).

### Option 2: Local testing

1. Copy this repository into Cursor's local plugins directory:

   ```bash
   mkdir -p ~/.cursor/plugins/local
   cp -R /path/to/cursor-plugin ~/.cursor/plugins/local/catalyst-by-zoho
   ```

   > Use `cp -R` rather than symlinks — symlink resolution for local plugins is unreliable in some Cursor versions.

2. Restart Cursor or run **Developer: Reload Window**.

3. Confirm the plugin loaded under **Cursor Settings → Plugins**. Skills, rules, and MCP are bundled automatically — authorize MCP once (see below).

### Option 3: Install from a Git URL

If your Cursor version supports installing plugins from a repository URL, point it at:

```
https://github.com/catalystbyzoho/cursor-plugin
```

## Catalyst MCP server (pre-configured)

The plugin ships one Catalyst MCP server per data center (`catalyst-by-zoho-us`, `-eu`, `-in`, `-au`, `-ca`, `-sa`, `-jp`, `-uae`). **IN is the default** (listed first); enable the one for your account and disable the others; run **`switch-dc`** for guidance.

The server names and URLs are listed in `skills/catalyst-zoho-mcp/references/dc-switching.md`.

To change regions later, run the **`switch-dc`** command or ask the agent (see `skills/catalyst-zoho-mcp/references/dc-switching.md`).

### Authorize once

1. Open **Cursor Settings → MCP**.
2. Find **catalyst-by-zoho** (may show "Needs authentication").
3. Click **Connect** and complete Zoho OAuth in your browser.
4. Confirm `ZohoMCP_*` meta-tools appear in the tool list.

The first time you invoke a Catalyst MCP tool, you may be prompted to authorize again if the session has expired — click **Connect** to re-authenticate.

> **Custom URL:** for a non-US DC run `switch-dc`; for a personal server, create a project `.cursor/mcp.json` with the URL for your DC, or copy a personal URL from [mcp.zoho.com](https://mcp.zoho.com).

For detailed MCP tool reference and verification steps, see `skills/catalyst-zoho-mcp/references/zoho-mcp.md`.

## Pre-flight checklist

Before asking Cursor to write or deploy Catalyst code:

- [ ] `catalyst login --dc <dc> -ni` has been run in your terminal
- [ ] `catalyst init --org <orgId> -p <projectId> -ni` has been run in your project directory
- [ ] `.catalystrc` and `catalyst.json` exist at the project root
- [ ] **Catalyst MCP data center** matches your account region (run `switch-dc` if not US)
- [ ] **catalyst-by-zoho** shows connected in **Cursor Settings → MCP** (click Connect if needed)

## Plugin structure

```text
cursor-plugin/
├── .cursor-plugin/
│   └── plugin.json       # Manifest
├── CATALYST.md           # Behavioral rules (wired via manifest)
├── .mcp.json             # One MCP server per data center
├── hooks/
│   ├── hooks.json        # sessionStart hook registration
│   └── catalyst-context.js
├── commands/
│   └── switch-dc.md      # Enable the MCP server for a DC
├── skills/               # Agent skills (auto-discovered)
│   ├── catalyst-by-zoho/
│   ├── catalyst-basics/
│   └── ...
├── assets/
│   └── catalyst_logo.svg
└── README.md
```

## Session start hook

When an Agent session starts in a Catalyst workspace (`.catalystrc` present), the plugin's `sessionStart` hook injects org/project/environment context and reports local CLI/runtime gaps. It is fire-and-forget and does not block session creation. The readiness gate in `skills/catalyst-basics/references/preflight.md` still verifies MCP parity before mutations.

## About CATALYST.md

`CATALYST.md` is a **lightweight rules file** — MCP routing and core behavioral guardrails for Catalyst work. It is registered in `plugin.json` as the plugin's rules source.

**It is not a replacement for the full skill library.** For application development, agents should route through `skills/catalyst-by-zoho/SKILL.md` and load the matching service skill and its `references/` files. `CATALYST.md` alone is insufficient for building a complete Catalyst app.

## What this plugin enables

- **Architecture recommendations** — Catalyst-specific service picks for your use case (including Signals and SmartBrowz)
- **Production-ready code generation** — correct handler signatures, SDK patterns, `catalyst-config.json`, and project structure compatible with `catalyst deploy`
- **Local-first workflow** — `sessionStart` context from `.catalystrc`, CLI/runtime prerequisite checks, and serve-before-deploy guidance
- **Platform migration guidance** — maps AWS, GCP, Azure, Vercel, Firebase, Supabase, and Heroku services to Catalyst equivalents
- **Infrastructure management via Zoho MCP** — create tables, insert data, run ZCQL, manage cache/buckets from chat (`ZohoMCP_*` meta-tools → `CatalystbyZoho_*` operations)
- **DC switching** — `switch-dc` command; one native OAuth MCP server per DC (no republish, no extra dependencies)
- **Pricing estimation** — cost breakdowns with unit prices and free tier offsets

## Other AI tools

This repository is the **Cursor-specific** distribution (skills, rules, hooks, commands, MCP). For Claude Code, see [claude-plugin](https://github.com/catalystbyzoho/claude-plugin). For a multi-tool skills pack, see [agent-skills](https://github.com/catalystbyzoho/agent-skills).

## License

Apache 2.0 — see [LICENSE](LICENSE).

## Resources

- [Catalyst Documentation](https://docs.catalyst.zoho.com/en/)
- [Catalyst Pricing](https://catalyst.zoho.com/pricing.html)
- [Catalyst GitHub](https://github.com/catalystbyzoho)
- [Zoho MCP](https://mcp.zoho.com/)
- [Cursor Plugins Docs](https://cursor.com/docs/plugins)
