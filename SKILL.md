---
name: artist-storybook-character
description: Activated when the user asks to generate a single original character or image from an arbitrary object, animal, person, profession, mood, action, or combined phrase in the established artist-led storybook style; produces one isolated character on a solid pure-white background as a PNG.
---

# Artist Storybook Character

Generate exactly one isolated author-led storybook character from the user's complete input.

## Core invariant

Every generation must use:

1. The same master prompt below.
2. Two changing fields: the user's exact `{{USER_INPUT}}` and a deterministic `{{PALETTE_DIRECTIVE}}` calculated from that input.
3. A fine but clearly visible near-black contour standard across the complete silhouette and major internal forms.

Never add subject-specific design instructions, including proposed silhouette, pose, anatomy, negative-space solution, facial layout, prop placement, manually chosen color palette, or exaggeration method. The purpose of this Skill is to let the same artistic method discover a different structural answer for any input while the deterministic palette mechanism prevents repeated default colorways.

## Deterministic palette mechanism

Before every generation, calculate a palette directive from the exact UTF-8 bytes of `{{USER_INPUT}}`:

1. Compute SHA-256 of the exact input.
2. Let `A`, `B`, `C`, `D`, and `E` be the decimal values of the first five hash bytes.
3. Calculate:
   - `H1 = A × 360 // 256`
   - `H2 = (H1 + 47 + B % 127) % 360`
   - `H3 = (H2 + 47 + C % 127) % 360`
   - `S1 = 58 + D % 31`, `S2 = 52 + E % 35`, `S3 = 66 + B % 25`
   - `L1 = 36 + C % 29`, `L2 = 48 + D % 27`, `L3 = 42 + E % 31`
4. Fill `{{PALETTE_DIRECTIVE}}` with exactly:

```text
Use this computed one-run palette: dominant HSL(H1 S1% L1%), secondary HSL(H2 S2% L2%), accent HSL(H3 S3% L3%). Keep the dominant color visibly predominant and use the other two sparingly. These numeric colors are binding. Do not substitute a familiar default palette.
```

5. Replace the symbols with the calculated integers before generation.
6. If the user explicitly specifies one or more colors, those explicit colors remain binding; use the computed colors only for unspecified roles.

## Context isolation

Every invocation is context-independent.

- Use only the user's current complete input and this Skill file.
- Do not use prior conversation, earlier generations, prior assistant interpretations, remembered palettes, recently used shapes, or previous outputs as generation context.
- Do not inspect, reference, compare with, imitate, or continue any image generated in an earlier invocation.
- Do not add inferred details from earlier turns, even when the current input resembles a previous subject.
- Do not use any image reference unless the user explicitly supplies one in the current request and explicitly asks for reference-based generation.

## Input interpretation

- Treat the user's entire phrase as one unified character concept.
- Never split comma-separated keywords into multiple characters unless the user explicitly requests multiple separate outputs.
- If the user explicitly lists several independent subjects and requests one image for each, run the identical master prompt separately for every subject. Change only `{{USER_INPUT}}`.
- Preserve explicit attributes in the input, such as color, species, profession, action, mood, or object type.

## Generation settings

Use the host environment’s image-generation capability (`generate_image` or an equivalent interface) with:

- `aspect`: `1:1`
- `size`: `4K`
- no reference image by default; use one only when explicitly supplied and requested in the current input
- one final PNG per subject in the current environment’s permitted output directory

The generated image must be a complete isolated character on an opaque, uniform pure-white (#FFFFFF) canvas. Never request a transparent background, background removal, or a simulated transparency checkerboard. Do not add grids, gray squares, off-white paper, or background texture.

Do not mention SVG, vector, editable paths, icons, logos, or vectorization in the image-generation prompt. These words distort the visual language toward rigid vector mascots. Output PNG only by default. If true SVG is explicitly requested, treat it as a separate vector-production task and verify actual vector paths; never rename a PNG to .svg or embed a PNG in an SVG wrapper and describe it as vector artwork.

The host must provide an image-generation capability; this repository contains the Skill instructions, not a standalone image model or rendering service. Map the settings to the available interface and respect the host’s permitted file locations.

## Master prompt

Replace only `{{USER_INPUT}}` and `{{PALETTE_DIRECTIVE}}`. Do not modify, expand, paraphrase, or append subject-specific visual solutions.

```text
Create one isolated original artist-led storybook character based only on this complete user input: “{{USER_INPUT}}”. Use this self-contained artistic language: delicate hand-drawn contour, inventive character construction, quiet expression, clean vivid matte color, and authored storybook sensibility. Do not derive anything from prior conversation, earlier images, remembered palettes, recently used shapes, or previous outputs.

Interpret the complete input as one unified character concept. Preserve its essential identity, function, mood, profession, and explicitly implied attributes, but do not solve it by placing accessories onto a conventional mascot body. Recognition should come from a few decisive cues. First invent the primary silhouette, then allow proportion, posture, structural integration, asymmetrical balance, and meaningful negative space to carry the imagination. Let professional, functional, emotional, animal, plant, or object qualities reshape the body itself when useful. Find a fresh structural answer appropriate only to this subject; do not use a standard body template, standard species anatomy, or a predetermined exaggeration formula.

Keep the character quiet, observant, slightly unusual, and naturally humorous. Facial features, if present, should be tiny and understated. Use a clearly visible near-black outer contour around the complete silhouette and major internal forms. Keep the contour fine and naturally hand-drawn, but consistently dark, continuous, and clearly contrasted against every adjacent color fill. Never let the outline fade, disappear, break unnecessarily, or merge into the fill. The hand-drawn feeling must come from line and shape, never from dirty texture. Choose a clear, vivid, high-purity matte palette freely according to the subject. Use decisive, complete color shapes with clean boundaries and evenly filled surfaces. Visual imagination must come from silhouette and structure, not surface effects.

{{PALETTE_DIRECTIVE}}

Show exactly one complete, uncropped character centered with comfortable empty padding on an opaque, flat, uniform pure-white (#FFFFFF) background extending to every canvas edge. The background must be actual solid white, not transparency or a transparency preview. No checkerboard, grid, gray squares, off-white or ivory backdrop, paper texture, background gradient, scenery, ground plane, frame, badge, circle, cast shadow, shadow panel, caption, label, logo, or typography.

Avoid standardized mascot anatomy, identical round heads, default arms and legs, oversized glossy eyes, generic cute-cartoon styling, conventional species silhouettes when a more inventive but recognizable construction is possible, pasted-on surreal accessories, polished geometric symmetry, thick vector outlines, gradients, 3D gloss, realistic rendering, stains, speckles, mottling, grain, dry-brush patches, faded edges, distressed wear, dirty overlays, or cloudy color variation. The final image should feel immediately readable yet structurally surprising, like a singular character invented by an observant storybook artist.
```

## Quality check

Before delivery, verify:

- Exactly one character per output.
- Complete silhouette is visible and uncropped.
- Canvas is opaque and uniformly pure white (#FFFFFF), with no checkerboard, grid, gray squares, transparency, off-white tint, texture, gradient, or shadow.
- The requested subject remains immediately recognizable.
- Structural imagination comes from silhouette, proportion, posture, integration, or negative space.
- A fine near-black contour is clearly visible, continuous, and consistently contrasted around the silhouette and major internal forms.
- Color areas are clean, vivid, even, and texture-free.
- The prompt contains the correctly calculated HSL directive for the exact current input.
- The delivered image visibly follows the computed dominant hue rather than a familiar default palette.
- The result is not a generic cute mascot or rigid vector icon.

If a result fails, rerun with the exact same master prompt and input. Do not repair it by adding subject-specific design instructions.

## Delivery

Deliver each PNG separately and describe it accurately as a pure-white-background PNG. Do not claim transparency, background removal, SVG output, or vector editability. Never present a renamed PNG or an SVG containing an embedded bitmap as a true vector SVG.
