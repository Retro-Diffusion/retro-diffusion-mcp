<p align="center">
  <img src="assets/logo.png" alt="Retro Diffusion" width="96" height="96">
</p>

<h1 align="center">Retro Diffusion MCP Server</h1>

<p align="center">
  Generate <b>real pixel art</b> — sprites, characters, animations, and tilesets — from any MCP-capable AI assistant.<br>
  Grid-aligned pixels, controlled palettes, transparent backgrounds. Not "pixel-art style" images. The real thing.
</p>

<p align="center">
  <a href="https://retrodiffusion.ai">Website</a> ·
  <a href="https://www.retrodiffusion.ai/app/guide/api">API Docs</a> ·
  <a href="https://github.com/Retro-Diffusion/api-examples">API Examples</a> ·
  <a href="https://discord.gg/retrodiffusion">Discord</a>
</p>

<p align="center">
  <a href="cursor://anysphere.cursor-deeplink/mcp/install?name=retro-diffusion&config=eyJ1cmwiOiJodHRwczovL21jcC5yZXRyb2RpZmZ1c2lvbi5haS9tY3AiLCJoZWFkZXJzIjp7IkF1dGhvcml6YXRpb24iOiJCZWFyZXIgWU9VUl9BUElfS0VZIn19"><img src="https://cursor.com/deeplink/mcp-install-dark.svg" alt="Add to Cursor"></a>
  <img src="https://img.shields.io/badge/license-MIT-green" alt="MIT License">
  <img src="https://img.shields.io/badge/transport-Streamable%20HTTP-blue" alt="Streamable HTTP">
</p>

---

This is a **hosted, remote MCP server** — nothing to install or run locally. Point your client at the endpoint, add your API key, and your assistant can browse 90+ pixel art styles, estimate costs for free, generate game-ready art, and repair enlarged or softened pixel art.

```
Endpoint:  https://mcp.retrodiffusion.ai/mcp     (Streamable HTTP)
Auth:      Authorization: Bearer <your API key>   (keys start with rdpk-)
```

The hosted MCP endpoint remains backward compatible and defaults to Retro
Diffusion API v2. Its server-side version policy is described in
[API compatibility](API_COMPATIBILITY.md); MCP clients do not change their
endpoint or authentication settings.

## Get an API key (2 minutes)

1. Create a free account at [retrodiffusion.ai](https://retrodiffusion.ai) — new accounts include free starter credits.
2. Open [Developer Tools](https://retrodiffusion.ai/app/devtools) and click **Create API Key**.

Pricing is pay-per-generation (from ~$0.01 per image), prepaid, **no subscription, and credits never expire**. Cost estimation is always free, so your agent can check the price of anything before spending.

## Setup by client

### Claude Code

```bash
claude mcp add --transport http retro-diffusion https://mcp.retrodiffusion.ai/mcp \
  --header "Authorization: Bearer rdpk-YOUR-KEY"
```

Or install the plugin (bundles the server plus a pixel-art workflow skill):

```
/plugin marketplace add Retro-Diffusion/retro-diffusion-mcp
/plugin install pixel-art@retro-diffusion
```

### Cursor

Click the **Add to Cursor** badge above, then replace `YOUR_API_KEY` — or add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "retro-diffusion": {
      "url": "https://mcp.retrodiffusion.ai/mcp",
      "headers": { "Authorization": "Bearer rdpk-YOUR-KEY" }
    }
  }
}
```

### VS Code (GitHub Copilot)

Add to `mcp.json` (Command Palette → "MCP: Add Server" → HTTP):

```json
{
  "servers": {
    "retro-diffusion": {
      "type": "http",
      "url": "https://mcp.retrodiffusion.ai/mcp",
      "headers": { "Authorization": "Bearer rdpk-YOUR-KEY" }
    }
  }
}
```

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "retro-diffusion": {
      "serverUrl": "https://mcp.retrodiffusion.ai/mcp",
      "headers": { "Authorization": "Bearer rdpk-YOUR-KEY" }
    }
  }
}
```

### Claude Desktop

Use [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) in `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "retro-diffusion": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.retrodiffusion.ai/mcp",
        "--header",
        "Authorization: Bearer rdpk-YOUR-KEY"
      ]
    }
  }
}
```

### Anything else

Any MCP client that speaks Streamable HTTP works: URL `https://mcp.retrodiffusion.ai/mcp`, header `Authorization: Bearer rdpk-YOUR-KEY`.

## Tools

**Generate**

| Tool | What it does |
|---|---|
| `create_inference` | Generate images, animations (GIF/sprite sheet), or tilesets. Returns hosted URLs and a recovery `request_id`; prefer RD Pro styles for top quality |
| `get_inference_result` | Recover or refresh signed output URLs for a successful synchronous generation without generating or charging again |
| `start_inference_job` | Start generation as an async job — recommended for animations and batches |
| `get_inference_job` | Poll an async job for status and results |
| `list_inference_jobs` | List your recent async jobs — recover a `task_id` after a lost submission response instead of re-submitting and double-charging |
| `estimate_inference_cost` | **Free** price check for any generation before running it |

Generation is non-idempotent and may charge before a client timeout or delivery problem is visible.
If `create_inference` succeeds but its `output_urls` are missing or expired, call
`get_inference_result(request_id)`. Never repeat the generation automatically. The MCP omits raw
generation base64 to keep Cursor and other tool contexts compact, and uses short-lived hosted URLs
for delivery instead.

**Edit** — post-process any image

| Tool | What it does |
|---|---|
| `list_edit_tools` | Enabled edit tools with their fields, costs, and limits |
| `run_edit_tool` | Run an edit tool: background remover, palette converter, color reducer, pixel correction, rotation, K-centroid downscale (free–$0.01) · image edit, inpainting, outpainting, seam tiling (premium) |
| `estimate_edit_tool_cost` | **Free** cost/duration estimate for any edit |
| `fix_pixel_art` | **Free** recovery of the native pixel grid from enlarged, softened, AI-rendered, or compressed pixel art |

**Styles**

| Tool | What it does |
|---|---|
| `list_available_styles` | Live style catalog with per-style size limits and capabilities |
| `list_available_models` | The model families: RD Fast, RD Plus, RD Pro, RD Mini |
| `get_style_usage` | Usage guidance and constraints for a specific style |
| `create_user_style` | Create a custom style from a reference image (RD Pro template) |
| `update_user_style` | Modify one of your custom styles |
| `delete_user_style` | Remove one of your custom styles |

**Account**

| Tool | What it does |
|---|---|
| `authenticate` | Validate an API key and attach it to the session |
| `get_balance` | Check remaining prepaid balance and credits |
| `get_service_status` | Subsystem health check (no auth required) |
| `logout` | Clear the stored session key |

`fix_pixel_art` accepts PNG or JPEG as raw base64 or a data URL. Images must be at least 16×16 and
no more than 4 megapixels. Standard and neural share a 10 requests/minute limit per API key;
request JSON is capped at 900,000 bytes and successful response JSON at 850,000 bytes. Use the
standard engine for native Rust grid detection or the neural engine with optional target width and
height values.

## What you can make

- **Sprites & characters** — 90+ styles: portraits, game assets, isometric, top-down, platformer, 1-bit, Minecraft items/textures, UI elements, item sheets, character turnarounds
- **Animations** — walk/idle/jump/crouch/attack cycles from a start frame, four-angle walk cycles, 8-direction rotations, VFX, battle sprites; GIF or sprite-sheet output
- **Tilesets** — Wang/blob tilesets, tile variations, single tiles, scene objects
- **Consistent characters** — generate once with RD Pro, then pass the output as a reference image in follow-up generations (up to 9 references)
- **Edits** — img2img, seamless tiling, transparent backgrounds, palette-constrained output
- **Pixel repair** — reconstruct enlarged or softened pixel art at its detected native resolution

Example prompts to try once connected:

> "List the available pixel art styles for animations, then estimate what a 64×64 walking animation would cost."

> "Generate a 128×128 pixel art knight resting by a campfire, RD Pro default style, transparent background."

> "Create a 16×16 Wang tileset: grey stone path tiles blending into lush grass."

> "Fix this enlarged sprite back to its native pixel grid using the standard Pixel Fixer."

## Prompting tips (important)

- **Describe the subject only** — never write "pixel art" in the prompt; the style handles the rendering.
- Sizes run 16×16 to 384×384 depending on style; `list_available_styles` gives exact per-style limits.
- Costs range from ~$0.015 (RD Fast) to $0.18/image (RD Pro); animations $0.07–$0.25; tilesets $0.10. `estimate_inference_cost` is always free — use it first.
- Reuse a seed to iterate on the same composition while changing only the prompt.

## Who makes this

Retro Diffusion is built by [Astropulse](https://x.com/RealAstropulse) — a pixel artist with seven years of freelance experience before building the tool — and is used in production by studios and platforms including Replicate, Poe (Quora), and Scenario. It's designed by artists and held to artist-grade quality standards.

## Support

- [Discord community](https://discord.gg/retrodiffusion) — fastest answers, the founder is active daily
- [Full API reference](https://www.retrodiffusion.ai/app/guide/api) and [runnable examples](https://github.com/Retro-Diffusion/api-examples)
- [API v1/v2 compatibility](API_COMPATIBILITY.md) — v2 default, v1 support, error contract, request IDs, and retry rules
- [Service status](https://api.retrodiffusion.ai/v1/status) (no auth required)

## License

The contents of this repository (documentation and manifests) are [MIT licensed](LICENSE). The Retro Diffusion service itself is governed by its [Terms of Service](https://retrodiffusion.ai/terms).
