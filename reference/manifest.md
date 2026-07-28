# Asset provenance manifest

Every generated asset carries a record of where it came from. Without one, nobody can answer "is this safe to use commercially?" six months later, and that question always arrives eventually, usually from legal, usually about the logo.

## Where it lives

`assets/MANIFEST.md` at the project root is a sensible default. If the project already has an assets directory under another name, use that instead. Say where you put it.

**Check that the location is not gitignored.** A provenance log that is not committed defeats its own purpose, and `assets/` is a commonly ignored path. If it is ignored, either put the manifest somewhere tracked or tell the user the ignore rule needs an exception.

## Format

One row per asset. Markdown table, so it stays readable in a diff and in a PR review.

```markdown
# Asset Manifest

| Asset | Backend | Model | Date | License | Commercial use | Prompt or source |
|---|---|---|---|---|---|---|
| `logo-mark.svg` | none | hand-authored SVG | 2026-07-28 | project | yes | Authored directly, no model |
| `hero-launch.png` | hugging-face | FLUX.1-schnell | 2026-07-28 | Apache-2.0 | yes | See `prompts/hero-launch.txt` |
| `mascot-a.png` | recraft | recraft-v3 | 2026-07-28 | vendor terms | verify | See `prompts/mascot-a.txt` |
```

## Column notes

- **Backend** — `none` for hand-authored work. That is a first-class value, not a gap.
- **Model** — the exact model or Space, not the vendor. "Hugging Face" is not an answer; "FLUX.1-schnell" is. If you cannot determine the exact model, record that you could not, rather than guessing.
- **License** — the model or service's terms as they apply to output. Link the terms where a link exists.
- **Commercial use** — `yes`, `no`, or `verify`. Use `verify` honestly. An unverified `yes` is worse than an admitted unknown.
- **Prompt or source** — long prompts belong in a sibling file rather than crushed into a table cell. Keep them, because reproducing an asset later requires the exact prompt.

## Rules

- Write the row when the asset is produced, not in a cleanup pass later. The details are gone by then.
- An asset whose provenance cannot be established is **flagged as unsafe to ship**, not quietly delivered. Say so out loud: *"This came from a model whose license I could not verify for commercial use. Treat it as a comp until that is confirmed."*
- Hand-authored SVG is the easy case and worth preferring partly for this reason: there is no third-party license question to resolve at all.
