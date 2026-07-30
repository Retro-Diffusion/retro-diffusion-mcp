# Retro Diffusion MCP — Launch Guide

## About

Retro Diffusion is the pixel-art specialist: models and tools purpose-built to produce real, grid-aligned, palette-controlled pixel art rather than "pixel-art style" images. This hosted MCP server puts that capability inside any MCP-capable assistant — Claude, Cursor, VS Code, Windsurf, and more — with no local installation. It is built by a working pixel artist and used in production by studios and platforms including Replicate, Poe (Quora), and Scenario.

## Key features

- **Real pixel art**: pixel-perfect grids, controlled palettes, transparent backgrounds, and per-style size constraints (16×16 up to 384×384)
- **90+ styles**: characters, portraits, isometric, top-down, platformer, 1-bit, Minecraft items and textures, UI elements, item sheets, character turnarounds
- **Animation**: walk/idle/jump/crouch/attack cycles from a start frame, four-angle walk cycles, 8-direction rotation, VFX — GIF or sprite-sheet output
- **Tilesets**: Wang/blob tilesets, tile variations, and scene objects
- **Character consistency**: up to 9 reference images with RD Pro styles
- **Free cost estimation**: agents can check the exact price of any generation before spending
- **Fair pricing**: pay per generation from ~$0.01, prepaid, no subscription, credits never expire; commercial use allowed

## Use cases

- Generate game-ready sprites, tiles, and animations directly inside an AI coding session ("vibe-coded" game development)
- Batch-produce asset variations for game jams and prototypes
- Keep a recurring character visually consistent across an entire asset set
- Build custom styles from reference art and reuse them across a project
- Estimate and control art budgets programmatically before generating

## Getting started

1. Create a free account at https://retrodiffusion.ai (free starter credits included)
2. Create an API key at https://retrodiffusion.ai/app/devtools
3. Connect: endpoint `https://mcp.retrodiffusion.ai/mcp` (Streamable HTTP) with header `Authorization: Bearer <key>`

Full per-client setup instructions: see the README.

## API compatibility

The public MCP URL and `Authorization` header are stable across the Retro
Diffusion API v1-to-v2 migration. The hosted MCP implementation defaults to v1
until its operator opts it into v2; clients do not select an API version.

The v2 migration standardizes upstream errors, preserves request IDs for
support, and never retries a failed paid request against another API version.
See [API_COMPATIBILITY.md](API_COMPATIBILITY.md) for the contract and rollout
boundary.
