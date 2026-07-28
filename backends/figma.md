# Figma

**Use it for:** turning an approved mark into a design system, reading existing design context, and producing screen-level layouts. Figma is a vector editor, so it is also a legitimate home for refining an SVG mark by hand.

**Not for:** generating illustration. Figma is not a diffusion backend.

## Setup

The recommended route is Figma's own plugin:

```bash
claude plugin install figma@claude-plugins-official
```

Alternatively, connect the MCP server directly.

**Remote server**, works on all seats and plans:

```bash
claude mcp add figma --transport http https://mcp.figma.com/mcp
```

**Desktop server**, requires a Dev or Full seat on a paid plan, plus the Figma desktop app running with a Design file open and the MCP server enabled from Dev Mode in the right sidebar:

```bash
claude mcp add figma --transport http http://127.0.0.1:3845/mcp
```

Both use OAuth or the local app session. The desktop server only responds while the app is open, so a failing connection is usually a closed app rather than a config problem.

## Gotchas

- **Seat requirements differ between the two servers.** The remote one works on any plan; the desktop one needs Dev or Full on a paid plan. Check which the user has before recommending the local route.
- **Design context is an external call.** Pulling from shared libraries sends file context to a third party. Do not include unreleased product detail, internal codenames, or proprietary logic.
- **It reads a design system you already have.** If the project has no design tokens, Figma will not invent good ones for you.

## Where it fits in the pipeline

Figma is usually the step *after* this skill rather than inside it: a mark is authored as SVG here, then imported to Figma to become a component, get variants, and feed a token set. Say that explicitly when handing off, and include the exported sizes and the monochrome variant.

## After generating or exporting

Screenshot what you produced and look at it. Record provenance per `reference/manifest.md`; for a mark refined in Figma, note the original authoring source so the trail stays unbroken.
