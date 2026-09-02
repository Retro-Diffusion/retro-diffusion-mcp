---
name: pixel-art
description: Generate and repair real pixel art (sprites, characters, animations, tilesets, game assets) with the Retro Diffusion MCP tools. Use whenever the user wants pixel art, game sprites, tilesets, sprite-sheet animations, retro/8-bit/16-bit game assets, Minecraft-style items, or needs enlarged or softened pixel art restored to its native grid — including setup help if the Retro Diffusion server or RD_API_KEY is not configured yet.
---

# Generating and repairing pixel art with Retro Diffusion

Retro Diffusion produces real, grid-aligned, palette-controlled pixel art — not "pixel-art style" images. The MCP server exposes tools to browse styles, estimate costs (free), generate, and recover the native grid of enlarged or softened art.

## Setup (only if tools are unavailable or auth fails)

1. The user needs an API key: free account at https://retrodiffusion.ai, then create a key at https://retrodiffusion.ai/app/devtools (keys start with `rdpk-`).
2. This plugin reads the key from the `RD_API_KEY` environment variable. Have the user set it (e.g. in their shell profile) and restart Claude Code, or use the `authenticate` tool with the key directly.
3. New accounts include free starter credits. Balance is prepaid; check it with `get_balance`.

## Core workflow — always in this order

1. **Pick a style**: call `list_available_styles` (filterable by model or tab). Each style declares its own size limits, batch limits, and whether it needs an input image or supports reference images. Never guess limits. Prefer RD Pro styles (`rd_pro__*`) for the highest quality.
2. **Estimate first**: call `estimate_inference_cost` — it is free and returns the exact price. Tell the user the cost before generating anything expensive (RD Pro is $0.18/image; animations up to $0.25).
3. **Generate** with `create_inference` for stills. It returns hosted `output_urls` plus a recovery `request_id`. If URLs are missing or expired after a successful call, call `get_inference_result(request_id)` — never repeat the paid generation automatically. For animations or batches use `start_inference_job` and poll `get_inference_job` every 2–5 seconds — they are long-running. If the response of `start_inference_job` is lost (timeout/disconnect) before you get a `task_id`, call `list_inference_jobs` to find the accepted job and resume polling it — never blindly re-submit, since the lost job was already accepted and charged.
4. **Repair the pixel grid when needed** with `fix_pixel_art` before other edits. Use it for enlarged, softened, AI-rendered, or compressed pixel art. Choose the standard engine for native Rust grid detection or the neural engine for neural reconstruction with optional target width and height.
5. **Post-process** with `run_edit_tool`: `background_remover` and `color_style_transfer` cost $0.01; `color_reducer`, `palette_converter`, `pixel_correction`, `k_centroid_downscale`, and `rotate` are free; `image_edit`, `inpainting`, `outpainting`, and `seam_tiling` are premium ($0.18). `estimate_edit_tool_cost` is always free.

## Prompting rules (these matter)

- **Describe the SUBJECT only.** Never write "pixel art", "8-bit", or "pixelated" in the prompt — the style handles all rendering. Write "a knight resting by a campfire at night", not "pixel art of a knight".
- Reuse the same `seed` to iterate on a composition while changing only the prompt.
- Generation and edit-tool image inputs should be raw base64 PNG strings. `fix_pixel_art` accepts raw base64 or a data URL, and accepts PNG or JPEG input.
- Generated images are delivered through short-lived hosted `output_urls`; use `get_inference_result` with the returned `request_id` to refresh them.
- Sizes range 16×16 to 384×384 depending on style; low-res styles (Minecraft items/textures, skill icons) run 16–128px.

## Recipe cheat sheet

- **Character sprite**: RD Pro default (`rd_pro__default`, 64–256px) for best quality; RD Fast default for cheap drafts.
- **Consistent character across images**: generate once with RD Pro, then pass that output in `reference_images` (up to 9) for every follow-up.
- **Animation from a still**: `rd_advanced_animation__walking` / `__idle` / `__attack` / `__custom_action` — requires `input_image` as the start frame, same width/height; returns a GIF; set `return_spritesheet: true` for a sprite sheet.
- **Walk cycle from a prompt**: `rd_animation__four_angle_walking` (48×48).
- **8-direction rotation**: `rd_animation__8_dir_rotation` (80×80, up to 5 reference images).
- **Tileset**: `rd_tile__tileset` (16–32px tile); two-texture transitions with `rd_tile__tileset_advanced` (`extra_prompt` for the second texture).
- **Transparent background**: set `remove_bg: true`.
- **Seamless texture**: set `tile_x`/`tile_y`.
- **Palette control**: pass `input_palette` (base64 image of the palette) to constrain output colors.
- **Recover a native pixel grid**: call `fix_pixel_art` with the image and `engine: "standard"`; it returns one raw base64 PNG in `base64_images`. Both engines share a 10 requests/minute limit per API key.
- **Custom style**: `create_user_style` with a reference image builds a reusable RD Pro-template style.

## When things fail

- Insufficient balance → tell the user their balance (`get_balance`) and link https://retrodiffusion.ai/app/credits. Charges for failed generations are auto-refunded.
- Size rejected → re-check the style's limits via `list_available_styles`; every style enforces its own range, and none goes above 384×384.
- Service status (no auth): https://api.retrodiffusion.ai/v2/status
- Successful generation but missing/expired URLs → call `get_inference_result(request_id)`; do not call `create_inference` again.
