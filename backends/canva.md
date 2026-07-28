# Canva

**Use it for:** composed layouts that a non-engineer needs to keep editing after you hand them over. Social cards, one-pagers, decks, resized variants of an approved design.

**Not for:** logo marks. Canva is a layout and design tool, not a vector mark generator.

## Setup

```bash
claude mcp add canva --transport http https://mcp.canva.com/mcp
```

Then run `/mcp` and complete the OAuth consent in the browser. Per machine, cannot be scripted.

## The gotcha that matters most

**Brand kits and brand template autofill are Enterprise-only on the MCP surface.**

This is the single most likely wrong inference about Canva. "Connect Canva so assets are on-brand" does not work on a lower tier. What you get by plan:

| Plan | Through MCP |
|---|---|
| Free | Create designs, search your library, export |
| Pro and above | The above, plus resizing to new dimensions |
| Enterprise | The above, plus brand kits, brand templates, and autofill |

If the goal is brand *enforcement*, Canva only delivers it at Enterprise. If the goal is a handoff a colleague can edit, any tier works. Be explicit about which of those the user actually wants before recommending a paid upgrade.

## When it is genuinely the right call

- The deliverable has to live somewhere a marketer will keep changing it.
- The org already lives in Canva, so assets landing there is a feature rather than a new silo.
- You need one approved design resized across a dozen platform formats.

## After generating

Export and look at the result rather than trusting the layout description. Check that colors match the brief exactly, since template defaults quietly override. Record provenance per `reference/manifest.md`, noting that a Canva-composed asset may embed third-party stock or template elements whose licensing follows Canva's terms, not yours.
