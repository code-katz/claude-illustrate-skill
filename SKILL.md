---
name: illustrate
description: Produce brand and visual assets — logos, wordmarks, favicons, icon sets, illustration, and marketing graphics — with a render-and-inspect loop so nothing ships unseen. Use this skill whenever the user asks to design, generate, make, or iterate on a logo, icon, app icon, favicon, wordmark, brand mark, illustration, hero image, social card, sticker, badge, or any image asset. Also use it when the user asks which AI image tool to use, how to connect an image generation backend, whether an AI-generated asset is safe to use commercially or trademark, or how to keep a set of assets visually consistent. Routes by asset class: vector marks are authored as SVG and need no image backend at all, raster illustration needs a connected one. Works across any project.
---

# Illustrate: brand and visual assets

This skill produces visual assets and, critically, **looks at what it produced**. Most AI asset work fails not because the generation is bad but because nobody checks the result at the size it will actually be used. A logo that reads beautifully at 512px and turns to mud at 16px is not a logo, it is a picture of one.

Two rules govern everything below.

1. **Route by asset class before choosing a tool.** The right instrument differs completely across the three classes.
2. **Never ship an asset you have not opened and inspected.** Claude can see images. Use that.

## Route first

| Asset class | Examples | Track |
|---|---|---|
| **Vector mark** | Logo, wordmark, favicon, app icon, UI icon set | **SVG track.** No image backend required. This is the default, not a fallback. |
| **Raster illustration** | Hero images, character art, textures, photographic composites | **Backend track.** Requires a connected generation backend. |
| **Composed layout** | Social cards, README headers, badges, one-pagers | **Markup.** HTML/CSS or SVG written directly. Not a generation problem. |

State the class out loud before proposing a tool. Most requests that sound like "generate an image" are vector marks or composed layouts, and generating those with a diffusion model is the wrong instrument.

## Take the brief first

No asset work starts without these. Ask for whatever is missing; do not invent values.

| Field | Why it matters |
|---|---|
| **Asset class** | Determines the track. See the table above. |
| **Smallest use size** | The binding constraint. 16px favicon and 512px app icon are different designs. |
| **Single-color requirement** | Whether it must survive being one flat color (stamps, embroidery, dark mode) |
| **Palette** | Exact hex values, not names |
| **Typography** | Families and weights, plus their licenses if the mark contains text |
| **Style reference** | Specific and named. Never "modern" or "clean". |
| **Set membership** | What existing assets this must match, or none |
| **Constraints** | What to explicitly avoid |

If the project has a brand file, compose the brief from it rather than inventing values.

---

## The SVG track (default for vector marks)

This works today with no account, no API key, and no backend. It is the primary path for logos and icons.

### 1. Author distinct concepts

Write **3 to 5 SVG files**, one per concept. They must be *structurally distinct ideas*, not recolors or minor variants of one idea. If all your concepts share a silhouette, you have one concept and four decorations.

Constraints that matter for a real mark:

- Build on a square viewBox (`0 0 512 512` is a sensible default) so scaling is predictable.
- Keep the node count low. Fewer, cleaner curves survive downscaling; a 400-node blob becomes noise at 16px.
- Avoid strokes thinner than roughly `2` units per 100 of viewBox, because they vanish when scaled down.
- No text below the size where it stays legible. If the wordmark must work at 16px, it is an icon, not a wordmark.
- No gradients, filters, or drop shadows in a v1 mark unless the brief asks for them. They complicate every downstream export.

### 2. Find a renderer

Chromium is present in most Claude Code environments but **often not on `PATH`**. Check the Playwright location before concluding it is missing:

```bash
CHROME=$(command -v chromium || command -v chromium-browser || command -v google-chrome \
  || ls /opt/pw-browsers/chromium-*/chrome-linux/chrome 2>/dev/null | head -1)
```

If nothing is found, try `rsvg-convert -w $size -h $size`. If there is no renderer at all, say so plainly and inspect the SVG source instead, rather than claiming you rendered a set you did not.

### 3. Build the contact sheet, and inspect that

**Do not render small PNGs and try to look at them one by one.** A 16px or 32px image viewed at actual size is unreadable, to you and to the user. That is not a rendering failure, it is the nature of the format. The contact sheet is what makes small-size evaluation possible, so build it first and inspect *it*.

Three techniques matter here, each of which silently produces a plausible-looking wrong result if you skip it:

- **Never screenshot a bare `.svg` file with `--window-size`.** If the SVG carries `width`/`height` attributes, the window **crops instead of scaling**, so your "16px render" is really the top-left 16×16 corner of a 512px image. It looks like a broken mark; it is a broken command. Always wrap in HTML and size the `svg` element with CSS.
- **Inline the SVG source into the sheet at each size.** Do not reference files with `<img src="...">`; in a `file://` context those race the screenshot or fail silently, and you get a blank sheet that reads as a design failure.
- **Pass `--virtual-time-budget`.** Without it the screenshot fires before layout settles and the sheet comes out blank or partial.

```bash
{
  printf '<!doctype html><meta charset="utf-8"><style>'
  printf 'body{margin:0;background:#fff;font:12px system-ui;padding:24px}'
  printf '.row{display:flex;gap:28px;align-items:flex-end;margin-bottom:28px}'
  printf 'figure{margin:0;text-align:center}figcaption{margin-top:8px;color:#444}'
  printf '</style><div class="row">'
  for s in 16 24 32 48 128 512; do
    printf '<figure><div style="width:%spx;height:%spx">' "$s" "$s"
    sed "s|<svg |<svg style=\"width:${s}px;height:${s}px\" |" concept-a.svg
    printf '</div><figcaption>%spx</figcaption></figure>' "$s"
  done
  printf '</div>'
} > sheet.html

"$CHROME" --headless --disable-gpu --no-sandbox --hide-scrollbars \
  --virtual-time-budget=3000 \
  --screenshot=sheet.png --window-size=1400,760 "file://$PWD/sheet.html"
```

Put every concept in one sheet, on both light and dark backgrounds. This single view is where set inconsistency and small-size failure become obvious, and it is the artifact to show the user.

### 4. Inspect, and be specific

**Read `sheet.png`.** The actual pixels, not the SVG source. Then score each concept:

- Does it read at the smallest use size, or is it a smudge?
- Does it survive being one flat color?
- Do the hex values on screen match the brief?
- If it belongs to a set, does it look like the same hand drew it?
- Is any text correctly spelled and kerned?

Name **the single worst failure**, not a list. A list is a way of avoiding a decision.

### 5. Refine, then re-render

Fix one thing. Re-render. Re-inspect. **Three passes maximum.** A pass that improves nothing means the concept is wrong, not underworked, and saying so beats burning another pass.

### 6. Export and record

Ship the SVG plus PNGs at the sizes the project needs, and a monochrome variant if the brief calls for one. Then write the provenance row (see `reference/manifest.md`). For hand-authored SVG the row is simple and clean: authored directly, no model, no third-party license question.

---

## The backend track (raster illustration)

### Declare the backend before promising anything

Check what is actually connected in this session. Then say which backend will produce the asset, by name.

**If no backend is connected, say so directly.** Do not describe an image you cannot produce. Offer what you can actually deliver: the SVG track if the asset is really a vector mark, a markup composition if it is a layout, or a production-ready prompt brief the user can take elsewhere.

If several are connected, pick by asset class and say why rather than defaulting to whichever is first.

### Choosing among them

| Need | Backend | Why |
|---|---|---|
| Vector logo or icon from a generator | Recraft | The only one emitting true SVG paths rather than a raster approximation |
| General raster illustration | Hugging Face (FLUX) | Free tier, no cost to try |
| Editable layout a non-engineer will keep working on | Canva | Hands off into a real editor |
| Turning an approved mark into a design system | Figma | It is a vector editor, not a generator |

Setup, auth, endpoints, and gotchas for each live in `backends/`. Read the relevant one when you need it rather than loading all of them.

### Prompting

Structure every prompt explicitly rather than piling up adjectives. Five parts:

1. **Type** — photograph, flat vector illustration, 3D render, line art
2. **Subject** — the specific thing, with composition
3. **Environment** — setting, background, spatial relationships
4. **Technical** — lighting, lens, palette as hex where the backend honors it
5. **Constraints** — what to exclude, stated positively where possible ("plain background" beats "no clutter")

Concrete specs beat vague quality words. "85mm, soft window light from the left, muted palette" produces far more than "high quality, beautiful, 4k".

**Sanitize before sending.** Prompts go to third-party services. Never include proprietary business logic, internal codenames, customer data, or unreleased product details.

### Then run the same loop

Read the generated file. Score it. Name the worst failure. Revise one thing. Three passes. **The inspection step is not optional just because a backend produced the image** — it matters more, because you did not control the output.

### Set consistency

A set is the hard part. One good icon is easy; twelve that look related is the job.

- Generate set members in one session with one prompt skeleton, varying only the subject clause.
- Where the backend supports style references or style IDs, pin one and reuse it for every member.
- Always inspect the set **together** on a contact sheet, never one file at a time.
- If a member drifts, regenerate it against the others rather than accepting it and moving on.

---

## Before anything ships

- Provenance recorded for every generated asset. Format in `reference/manifest.md`.
- Commercial-use and trademark posture understood if the asset is a brand mark. See `reference/ip-and-licensing.md`.
- Fonts used in any mark are licensed for the actual use.
- Every delivered asset has been opened and looked at, at its smallest size.

An asset that cannot carry a provenance line is flagged as unsafe to ship, not quietly delivered.

---

## Prior art

This skill was written from scratch, informed by four projects worth crediting. No text was copied from any of them; all three third-party repos are MIT licensed.

- **[neonwatty/logo-designer-skill](https://github.com/neonwatty/logo-designer-skill)** (MIT) — demonstrated that a full logo workflow can run on directly authored SVG with no image backend at all, and that exporting across a standard size ladder is what surfaces failure. That insight is the basis of the SVG track being the default here rather than a fallback.
- **[designrique/ai-graphic-design-skill](https://github.com/designrique/ai-graphic-design-skill)** (MIT) — for framing backend choice as a per-task decision rather than a single winner, and for treating IP indemnification as a first-class selection criterion.
- **[jezweb/claude-skills](https://github.com/jezweb/claude-skills)** (MIT) — for the structured multi-part prompt approach, and for the practical lesson that image APIs should be called from Python rather than shell, because shell escaping breaks on apostrophes.
- **Anthropic's `theme-factory` skill** — for the structure used here: a thin orchestrating `SKILL.md` with one data file per option, so that vendor detail stays isolated in a single file instead of scattered through the pipeline.
