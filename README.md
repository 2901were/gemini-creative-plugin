# Gemini Creative Plugin

A Claude Code plugin wrapping a Model Context Protocol (MCP) server for AI image generation using Google's Gemini 3 image models. Designed for LLM agents — built around sessions, organized file output, and 9 auto-activating skills covering game art, UI mockups, character sprites, and prompt engineering.

**Default model:** Gemini 3.1 Flash (Nano Banana 2) · **Also supports:** Gemini 3 Pro · **License:** MIT

---

## Install

Two slash commands inside Claude Code:

```
/plugin marketplace add https://github.com/2901were/gemini-creative-plugin
/plugin install gemini-creative-plugin@gemini-creative-marketplace
```

Then set your Gemini API key (get one at [aistudio.google.com/apikey](https://aistudio.google.com/apikey)):

```bash
export GEMINI_API_KEY="your-key-here"
```

That's it. The plugin registers the MCP server and the 9 auto-activating skills. Restart Claude Code after setting the env var (MCPs inherit env at launch).

---

## What it does

### 10 MCP tools

**Image generation:**
- `generate_image` — text-to-image with aspect ratio, resolution, and thinking-level controls
- `edit_image` — modify an existing image with text + up to 14 reference images
- `continue_editing` — iterate on the last image without re-specifying its path

**Sessions:**
- `start_creative_session` / `send_creative_message` / `end_creative_session` — organize multi-image workflows with shared defaults (model, aspect ratio, output directory)
- `get_session_info` — list active sessions or inspect one

**Setup + utilities:**
- `configure_gemini_token` / `get_configuration_status` — API-key setup
- `get_last_image_info` — file path, size, and timestamp of the last generated image
- `get_prompt_template` — pre-built prompt structures for common asset types

### 9 auto-activating skills

Claude Code triggers these automatically when the conversation matches their description:

| Skill | Triggers on |
|---|---|
| `/gemini-quick` | Single image, fast one-off |
| `/gemini-characters` | Game characters — style bible, poses, cross-session consistency |
| `/gemini-sprite-series` | Consistent character sprites across poses or animation frames |
| `/gemini-game-assets` | Pixel art, tilesets, palettes, isometric, tier evolutions |
| `/gemini-ui-mockups` | Multi-screen UI mockups with style selection |
| `/gemini-session` | Session setup, model selection, troubleshooting |
| `/gemini-workflows` | Workflow decision tree across the four main patterns |
| `/gemini-prompts` | Domain-specific prompt structures (sprites, UI, icons, web) |
| `/gemini-configure` | API-key setup and authentication troubleshooting |

---

## Best practices

**For visual consistency across multiple images,** always pass the previous image via the `images: [path]` parameter in `send_creative_message`. Without it, Gemini reconstructs from text alone and details drift between calls — proportions shift, colors change, fine features vary.

**Prompt structure:**

```
[Style] [Subject] in [pose/state], [composition], [distinctive details], [background], [technical specs]
```

**Pixel-art bible prefix:**

```
2D pixel art, 32x32 sprite, PICO-8 16-color palette, 1px black outline, 2-tone cel shading, transparent background —
```

---

## Troubleshooting

**"Gemini API token not configured"** — Run `get_configuration_status` to verify setup. Set `GEMINI_API_KEY` env var and restart Claude Code (MCPs inherit env at launch).

**Images generating inconsistently across multiple calls** — Pass the previous image via the `images: [path]` parameter in `send_creative_message`. The bible-pinning approach + image references are what locks consistency.

**"Maximum sessions (5) reached"** — Run `get_session_info` and end an older session.

**Array parameters not working** — Ensure your MCP client sends arrays as JSON arrays, not strings. The server handles both formats defensively, but the JSON form is canonical.

---

## Links

- [Get a Gemini API key](https://aistudio.google.com/apikey) — free tier available
- [Gemini API documentation](https://ai.google.dev/gemini-api/docs)
- [Image generation guide](https://ai.google.dev/gemini-api/docs/image-generation)
- [Model Context Protocol](https://modelcontextprotocol.io/)

---

Built by [Vladimir Bulanenko](https://github.com/2901were). MIT licensed.
