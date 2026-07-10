# Publishing & directory checklist (internal)

Operational playbook for listing this server everywhere. Verified July 2026. Work top to bottom.

## 0. Push this repo

Create the public repo `Retro-Diffusion/retro-diffusion-mcp` on GitHub and push. Everything below links to it.

## 1. Official MCP Registry (do first — others ingest it)

One-time domain verification for the `ai.retrodiffusion/*` namespace:

```bash
# Generate a signing key (KEEP PRIVATE — do not commit key.pem)
openssl genpkey -algorithm Ed25519 -out mcp-registry-key.pem

# Public key for the DNS record:
openssl pkey -in mcp-registry-key.pem -pubout -outform DER | tail -c 32 | base64
```

Add a **TXT record on the apex** `retrodiffusion.ai` (Route 53):

```
v=MCPv1; k=ed25519; p=<base64 public key from above>
```

Then publish (CLI: `brew install mcp-publisher`, or the Windows binary from
https://github.com/modelcontextprotocol/registry/releases/latest):

```bash
mcp-publisher login dns --domain retrodiffusion.ai \
  --private-key "$(openssl pkey -in mcp-registry-key.pem -noout -text | grep -A3 'priv:' | tail -n +2 | tr -d ' :\n')"
mcp-publisher publish   # run in this repo directory; uses server.json
```

Verify: `curl "https://registry.modelcontextprotocol.io/v0.1/servers?search=ai.retrodiffusion"`

Notes: validation is automated (no human queue); published versions are immutable — updates require bumping `version` in server.json and republishing; registry is still "preview", so keep the key safe in case a data reset requires republishing. **Publishing here auto-propagates to the GitHub MCP Registry (github.com/mcp), which is what VS Code / Copilot surface.**

## 2. Directory sweep (each ~10–30 min, all free)

| Directory | How | Status |
|---|---|---|
| PulseMCP | https://www.pulsemcp.com/submit (also auto-ingests the registry within ~a week) | ☐ |
| Glama (hosted) | https://glama.ai/mcp/connectors → "Add MCP Server → Connector" (paste endpoint; instant after health check) | ☐ |
| Glama (repo) | https://glama.ai/mcp/servers → "Add Server" with this repo; `glama.json` claims it | ☐ |
| Smithery | https://smithery.ai/new → paste endpoint URL | ☐ |
| mcpservers.org | https://mcpservers.org/submit (free; $39 optional fast-track) | ☐ |
| mcpmarket.com | https://mcpmarket.com/submit → paste this repo's GitHub URL (listing generated from README + LAUNCHGUIDE.md) | ☐ |
| mcp.so | Submit button on https://mcp.so from a browser (form blocks bots) | ☐ |
| cursor.directory | https://cursor.directory/plugins/new | ☐ |
| LobeHub | `npx @lobehub/market-cli plugin submit https://github.com/Retro-Diffusion/retro-diffusion-mcp` then `... plugin claim` (fallback: GitHub issue on lobehub/lobehub, ~3 weeks) | ☐ |

## 3. Client-owned surfaces

| Surface | How | Status |
|---|---|---|
| Claude Code marketplace (self-serve, live immediately on push) | Users run `/plugin marketplace add Retro-Diffusion/retro-diffusion-mcp` — announce in Discord/docs | ☐ |
| Claude plugin directory (official) | Run `claude plugin validate .` here first, then submit at https://platform.claude.com/plugins/submit (needs only an API-console account, NOT Team/Enterprise) | ☐ |
| Cursor Marketplace | https://cursor.com/marketplace/publish → submit this repo (manifest `.cursor-plugin/plugin.json`, README, logo required) | ☐ |
| "Add to Cursor" button | Already in the README; also add to retrodiffusion.ai docs/API pages | ☐ |

## 4. Gated / later

- **Claude Connectors Directory** (claude.ai): requires a Team/Enterprise org to submit AND effectively OAuth 2.0 (Bearer keys only via beta `static_headers` / `custom_connection`, coordinated through mcp-review@anthropic.com). Revisit after adding an OAuth layer to the MCP server — the highest-traffic surface, worth the work eventually.
- **GitHub MCP Registry featured/partner tier**: nominate via partnerships@github.com (the community tier is already covered by step 1).
- **Windsurf Plugin Store**: no public submission — partnership outreach to Cognition.

## Copy-paste submission kit

Same strings for every form. Fill and go.

- **Name:** `Retro Diffusion Pixel Art`
- **Tagline:** `Real pixel art in your AI assistant — sprites, animations, and tilesets.`
- **Description:** `Hosted MCP server for Retro Diffusion. Generate authentic, grid-aligned pixel art — sprites, characters, animations, and tilesets — from Claude, Cursor, VS Code, Windsurf, or any MCP client. 90+ styles, free cost estimation, pay-per-generation with no subscription and credits that never expire.`
- **Endpoint:** `https://mcp.retrodiffusion.ai/mcp` (transport: Streamable HTTP)
- **Auth:** header `Authorization: Bearer <key>` — keys created free at `https://retrodiffusion.ai/app/devtools`
- **Repo:** `https://github.com/Retro-Diffusion/retro-diffusion-mcp`
- **Website:** `https://retrodiffusion.ai` · **Docs:** `https://astropulse.gitbook.io/retro-diffusion`
- **Logo (raw URL):** `https://raw.githubusercontent.com/Retro-Diffusion/retro-diffusion-mcp/master/assets/logo.png`
- **Categories:** Image Generation · Game Development · Creative Tools
- **Tool count:** 17
- **Support:** `https://discord.gg/retrodiffusion` (use your preferred support email where one is required)

## Updating listings

- Registry: bump `version` in server.json → `mcp-publisher publish`
- Claude plugin directory: auto-mirrors this GitHub repo after approval
- LobeHub: `npx @lobehub/market-cli plugin publish` (or comment on the listing issue)
- Others: listings scrape the repo README — keeping it current updates them
