# Catalyst by Zoho — Cursor Plugin

Official [Cursor plugin](https://cursor.com/docs/plugins) for [Catalyst by Zoho](https://catalyst.zoho.com) — Zoho's full-stack serverless cloud platform.

This plugin gives Cursor deep knowledge of Catalyst services, SDK patterns, CLI workflows, and architecture guidance. With Zoho MCP connected, the agent can also manage Catalyst infrastructure (tables, cache, buckets, ZCQL) directly from chat.

## What's included

| Component | Path | Description |
|-----------|------|-------------|
| **Rules** | `CATALYST.md` | Always-on behavioral rules and MCP routing for Catalyst projects |
| **Skills** | `skills/` | 14 specialized agent skills (functions, AppSail, Data Store, Stratus, Slate, SDKs, pricing, and more) |
| **MCP** | `.mcp.json` | Zoho MCP server config for `CatalystbyZoho_*` infrastructure tools |
| **Manifest** | `.cursor-plugin/plugin.json` | Plugin metadata for Cursor Marketplace and local installs |

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
| `catalyst-pricing` | Free tier, pay-as-you-go, cost estimation |
| `catalyst-sdk` | Node.js, Web, Python, Java, mobile SDKs |
| `catalyst-zia` | Zia Services, QuickML, OCR/ML |
| `catalyst-zoho-mcp` | MCP setup and `CatalystbyZoho_*` tool usage |

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

The plugin ships with the Catalyst MCP server **pre-wired** in `.mcp.json`, so Cursor can manage Catalyst infrastructure (create tables, query data, manage cache/buckets) directly from chat — no manual MCP server creation or URL copying required.

### Authorize once

1. Open **Cursor Settings → MCP**.
2. Find **catalyst-by-zoho** (may show "Needs authentication").
3. Click **Connect** and complete Zoho OAuth in your browser.
4. Confirm `CatalystbyZoho_*` tools appear in the tool list.

The first time you invoke a Catalyst MCP tool, you may be prompted to authorize again if the session has expired — click **Connect** to re-authenticate.

> **Region note:** `.mcp.json` defaults to the **US** data center endpoint (`https://catalystbyzoho.zohomcp.com/mcp/message`). If your Catalyst account is on a different DC, update the `url` to match your region — or ask the agent to switch it for you.

| Data Center | MCP Server URL |
|-------------|----------------|
| **US** (default) | `https://catalystbyzoho.zohomcp.com/mcp/message` |
| **EU** | `https://catalystbyzoho.zohomcp.eu/mcp/message` |
| **IN** | `https://catalystbyzoho.zohomcp.in/mcp/message` |
| **AU** | `https://catalystbyzoho.zohomcp.com.au/mcp/message` |
| **CA** | `https://catalystbyzoho.zohomcp.ca/mcp/message` |
| **SA** | `https://catalystbyzoho.zohomcp.sa/mcp/message` |
| **JP** | `https://catalystbyzoho.zohomcp.jp/mcp/message` |

> **Fallback:** If the URL for your DC doesn't work, go to [mcp.zoho.com](https://mcp.zoho.com) → **Connect** → copy the exact URL shown for your account and update it in **Cursor Settings → MCP**.

For detailed MCP tool reference and verification steps, see `skills/catalyst-zoho-mcp/references/zoho-mcp.md`.

## Pre-flight checklist

Before asking Cursor to write or deploy Catalyst code:

- [ ] `catalyst login` has been run in your terminal
- [ ] `catalyst init` has been run in your project directory
- [ ] `.catalystrc` and `catalyst.json` exist at the project root
- [ ] **catalyst-by-zoho** shows connected in **Cursor Settings → MCP** (click Connect if needed)

## Plugin structure

```text
cursor-plugin/
├── .cursor-plugin/
│   └── plugin.json       # Plugin manifest
├── CATALYST.md           # Behavioral rules (wired via manifest)
├── .mcp.json             # Zoho MCP server config
├── skills/               # Agent skills (auto-discovered)
│   ├── catalyst-by-zoho/
│   ├── catalyst-basics/
│   └── ...
├── assets/
│   └── catalyst_logo.svg
└── README.md
```

## About CATALYST.md

`CATALYST.md` is a **lightweight rules file** — MCP routing and core behavioral guardrails for Catalyst work. It is registered in `plugin.json` as the plugin's rules source.

**It is not a replacement for the full skill library.** For application development, agents should route through `skills/catalyst-by-zoho/SKILL.md` and load the matching service skill and its `references/` files. `CATALYST.md` alone is insufficient for building a complete Catalyst app.

## What this plugin enables

- **Architecture recommendations** — Catalyst-specific service picks for your use case
- **Production-ready code generation** — correct handler signatures, SDK patterns, `catalyst-config.json`, and project structure compatible with `catalyst deploy`
- **Platform migration guidance** — maps AWS, GCP, Azure, Vercel, Firebase, Supabase, and Heroku services to Catalyst equivalents
- **Infrastructure management via Zoho MCP** — create tables, insert data, run ZCQL, manage cache/buckets from chat
- **Pricing estimation** — cost breakdowns with unit prices and free tier offsets

## Other AI tools

This repository is the **Cursor-specific** distribution. For Claude Code, Gemini CLI, Codex, Windsurf, and other agents, see the multi-tool [agent-skills](https://github.com/catalystbyzoho/agent-skills) repository.

## License

Apache 2.0 — see [LICENSE](LICENSE).

## Resources

- [Catalyst Documentation](https://docs.catalyst.zoho.com/en/)
- [Catalyst Pricing](https://catalyst.zoho.com/pricing.html)
- [Catalyst GitHub](https://github.com/catalystbyzoho)
- [Zoho MCP](https://mcp.zoho.com/)
- [Cursor Plugins Docs](https://cursor.com/docs/plugins)
