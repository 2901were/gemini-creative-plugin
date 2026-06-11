---
name: gemini-quick
description: This skill should be used when the user asks to "generate one image", "make a single image", "create a quick image", "generate an image quickly", "just need one image", or otherwise needs a single-shot image generation without session setup. Covers the minimal generate_image flow and when to skip the session machinery.
version: 0.4.0
---

# Gemini Quick Image Generator

Generate a single image fast, without session overhead. Best for one-off images, prototypes, or exploration.

## When to Use This

- Creating a single image with no plans to iterate
- Exploring different concepts (generate several variations)
- When you don't need organized file management
- Quick reference images or placeholders

## When NOT to Use This

- If you'll want consistent variations → use the `gemini-sprite-series` skill instead
- If you're building a multi-screen project → use the `gemini-ui-mockups` skill instead
- If you want to iterate and refine → use `continue_editing` after the first generation

## Steps

1. **Ask the user** what they want to generate (if not already described).

2. **Check API status** with `get_configuration_status`. If not configured, guide through setup:
   - Set `GEMINI_API_KEY` env variable (recommended), or
   - Run `configure_gemini_token` with their key from [AI Studio](https://aistudio.google.com/apikey)

3. **Choose parameters** based on the request:
   - `aspectRatio`: 1:1 (square/social), 16:9 (wide/presentation), 9:16 (mobile/story), 3:2 (photo), 21:9 (cinematic)
   - `model`: `gemini-3.1-flash-image-preview` (default, best quality/speed balance)
   - `resolution`: `1K` default, `2K` for sharper detail, `4K` for maximum quality
   - `responseMode`: `image_only` (default/fast) or `text_and_image` if user wants description

4. **Generate** with `generate_image`. Write a descriptive prompt:
   - Include: subject, style, lighting, mood, composition, color palette
   - Avoid vague keywords — "a cozy coffee shop with warm amber lighting, steam rising from cups, watercolor style" not "coffee shop"

   For a one-off anchored to an EXISTING image (same character in a new scene, style-matched companion piece), pass `referenceImages: ["/path/to/ref.png"]` — no session needed. All ref paths must be valid; the call fails rather than generating unanchored. For a multi-image consistent series, switch to sessions (see gemini-workflows).

5. **Visually verify** with Read tool. If the result needs tweaking, use `continue_editing` with a correction prompt.

6. **Report back** with the image path and any notable details about what was generated.

## Prompt Quality Guide

| Vague ❌ | Descriptive ✅ |
|----------|--------------|
| "a cat" | "A ginger tabby cat sitting in a sunlit window, watercolor illustration, warm tones" |
| "a logo" | "Minimalist tech startup logo, letter 'G', geometric, dark blue on white, vector style" |
| "space scene" | "Deep space nebula, purple and gold clouds, distant stars, photorealistic, cinematic lighting" |

## Universal Prompt Formula

```
[Subject] + [Composition] + [Action/State] + [Location] + [Style] + [Technical specs]
```

Example: `2D pixel art wizard character [subject], centered, front-facing [composition], casting a fireball [action], plain white background [location], clean outlines, limited 16-color palette [style], 32x32 pixel grid [technical]`

## Iteration Strategy

- **Wrong detail** → `continue_editing("change X to Y")` — targeted correction
- **Wrong composition** → start fresh with `generate_image` — editing rarely fixes fundamental layout
- **Style drifted** → pass the original image as a reference in `continue_editing`
- **Text errors** (e.g. "SWIM" → "SVIM") → always verify text with Read tool, then correct with `continue_editing`

See the `gemini-prompts` skill for domain-specific prompt structures (sprites, UI, icons, characters).
