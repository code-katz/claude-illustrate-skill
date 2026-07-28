# Hugging Face

**Use it for:** raster illustration on a free tier. This is the zero-cost default for the backend track.

**What it gives you:** access to Hugging Face Spaces, including FLUX image generation models, through one hosted MCP server.

## Setup

Anonymous access works for light use:

```bash
claude mcp add hugging-face --transport http https://huggingface.co/mcp
```

With a token, for higher limits:

```bash
claude mcp add hugging-face --transport http https://huggingface.co/mcp \
  -H "Authorization: Bearer $HF_TOKEN"
```

Config files support `${VAR}` interpolation in headers, so the token does not need to be written in plaintext.

## Gotchas

- **Weakest text rendering of the four.** If the asset contains words, expect misspellings and broken kerning. Inspect embedded text character by character, and prefer composing text in markup over generating it.
- **Volume needs a PRO account.** Free-tier generation is rate limited; a large set will hit the ceiling partway through and leave you with an inconsistent half-set.
- **Spaces come and go.** A Space that worked last month may be paused or removed. If a generation call fails, check whether the Space still exists before debugging the prompt.
- Raster only. Do not use it for a logo that needs to be vector.

## Prompting notes

Use the five-part structure from `SKILL.md`. FLUX responds well to concrete photographic and illustrative direction and poorly to stacked quality adjectives. State exclusions explicitly.

## After generating

Read the output file. Check embedded text spelling first, since that is the most common failure. Then run the normal inspect-and-revise loop and record provenance, noting the specific Space and model, not just "Hugging Face".
