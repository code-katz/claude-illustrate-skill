# IP, licensing, and trademark posture

Generated brand assets carry legal exposure that is cheap to handle up front and expensive to discover late. Raise these before a mark is designed, not after someone asks where it came from.

## The three questions

1. **Can this be used commercially?** Depends on the model's terms, not on the fact that AI made it.
2. **Can this be registered as a trademark?** A separate question from copyright, with a different answer.
3. **Who indemnifies us if someone claims infringement?** Usually nobody, unless you chose a backend specifically for it.

## Copyright

**Purely AI-generated output generally does not attract copyright protection**, because authorship requires human creative contribution. Jurisdictions differ and the law is moving, but the practical consequence is stable and worth stating plainly to users:

> A logo you generated entirely from a prompt may not be something you can stop a competitor from copying.

Meaningful human creative work on the mark, selecting, arranging, editing, and refining rather than accepting a first output, strengthens the position considerably. This is a real argument for the SVG track: a mark authored deliberately, node by node, has human authorship that a prompt-and-accept workflow does not.

## Trademark

Trademark protects a mark used to identify a business's goods or services, and it is largely independent of how the mark was created. Registries do not generally ask whether a mark was AI-assisted, and AI-assisted marks have been registered.

What matters for registration is that the mark is distinctive and not confusingly similar to an existing one. So:

- **Search before committing.** A generated mark can resemble an existing one, because the model learned from existing ones.
- **Keep the provenance trail.** If the mark's origin is ever questioned, the manifest is the answer.
- **Prefer marks with human authorship in the chain**, both for copyright and because it is easier to defend.

## Indemnification by backend

This is where backend choice becomes a legal decision rather than a quality one.

| Posture | What it means |
|---|---|
| **Indemnified** | The vendor contractually defends enterprise customers against IP claims arising from output. Adobe Firefly is the notable example, on enterprise tiers, having trained on licensed stock. |
| **Partial** | Some coverage, typically enterprise-only and scoped. |
| **None** | Most models. Training data is undisclosed or contested, and several are subject to ongoing litigation. |

Terms change and vary by plan. **Verify against the vendor's current terms rather than this table**, and record what you found in the manifest.

## When to escalate

Tell the user to get actual legal review, rather than proceeding, when:

- The mark will be registered as a trademark.
- The asset is for a client who has demanded IP guarantees.
- The work is for a regulated industry or a company with a formal IP policy.
- A generated mark looks noticeably like an existing brand's.

This skill can keep a clean provenance trail and flag risk. It cannot give legal advice, and should say so rather than implying otherwise.

## Fonts and third-party assets

Easy to forget and a common source of real problems.

- Web fonts, stock imagery, and icon libraries all carry license terms.
- A font licensed for web display is not automatically licensed for embedding in a logo.
- Verify licensing for the *actual* use (commercial, open source, internal) before an asset ships, not after.
- Record font choices and their licenses alongside the asset.
