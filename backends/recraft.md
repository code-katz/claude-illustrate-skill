# Recraft

**Use it for:** vector logos and icon sets when you want a generator rather than hand-authored SVG, and for vector illustration.

**Why it is different:** Recraft emits genuine SVG paths, not a rasterized image with an `.svg` extension. The output opens in Figma or Illustrator and is editable node by node. Every other generation backend in this skill returns raster.

## Setup

```bash
claude mcp add recraft --transport http https://mcp.recraft.ai/mcp
```

Then run `/mcp` in an interactive session and complete the OAuth consent in the browser. This step cannot be scripted or pre-seeded, and it is per machine.

## Gotchas

- **The npm package is deprecated.** `@recraft-ai/mcp-recraft-server` is marked deprecated by its own README, which directs users to the remote server above. Do not set up the stdio package; it is a dead path.
- **Style types matter more than prompt wording.** Asking for a "vector illustration" or "icon" style produces far cleaner geometry than a photographic or painterly style that is then expected to look flat.
- **Node count still needs checking.** Vector output is not automatically *good* vector output. Open the SVG and look at the path data. A mark with hundreds of nodes will be unpleasant to edit and may still degrade at small sizes.
- Paid API. Costs accrue per generation.

## Prompting notes

Lead with the style type and the structural idea, not with adjectives. Name the palette in hex where possible. State the smallest use size in the prompt as a constraint on detail: a mark intended for 16px should be described as simple and bold, because the model will happily add detail that dies on downscale.

## After generating

Run the standard loop from `SKILL.md`: render the SVG to PNG at 16, 32, and 512, read the PNGs, and check that the vector actually survives downscaling. Then record provenance per `reference/manifest.md`, including the exact model.
