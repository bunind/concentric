---
name: concentric
description: Generate UI components with concentric scaling — all geometry (padding, radius, gaps, child sizes, type) derives from one input, the component height H. Plus audits existing components and reports what drifts from concentric design principle. Use when the user types /concentric, asks to create a button/chip/input/segmented control/toggle at a size (2XSmall–2XLarge / 2xs–2xl, or any H), asks to make a component concentric, or asks to analyze/audit a component and suggest improvements.
---

# Concentric scaling

One input: component height **H**. Everything else is derived from it.
Never copy pixel values from another instance. Evaluate the formulas.

```
          R = H/2                      band = 6u
        ╭──────────────────────────────────────╮   ┬
        │ ←3u→   ◯ icon   ·label·   (1)   ←3u→ │   H
        ╰──────────────────────────────────────╯   ┴
        badge radius r = R − p  →  concentric
```

## Formulas

Base unit **u = H/12**. All geometry is a whole multiple of u; type is the
only exception.

| Value          | Formula                              |
|----------------|--------------------------------------|
| base unit      | u = H/12                             |
| padding p      | 3u spacious · 1u dense (see classes) |
| content band   | 6u (icons, badges, nested items)     |
| gap            | 1u between inline items              |
| text inset     | 1u, on the text item's own wrapper   |
| outer radius   | R = min(H/2, R_max)                  |
| child radius   | r = R − p                            |
| font size      | round(5.6u)                          |
| line height    | 6u                                   |
| letter spacing | −1.3% of font size                   |

Round every output to whole px, fonts included. Emit a fractional font size
only when the user explicitly asks for one.

## Size ramp

The **full name** is canonical — use it for layer names, component names,
Figma variable modes, and token keys, where it reads on its own. The **alias**
is input only: accept it when the user types, never write it onto an artifact.
Any raw H works too.

| Name     | Alias | H  | p  | band | gap | R  | font |
|----------|-------|----|----|------|-----|----|------|
| 2XSmall  | 2xs   | 16 |  4 |   8  |  1  |  8 |  7 ⚠ |
| XSmall   | xs    | 20 |  5 |  10  |  2  | 10 |  9   |
| Small    | sm    | 24 |  6 |  12  |  2  | 12 | 11   |
| Medium   | md    | 32 |  8 |  16  |  3  | 16 | 15   |
| Large    | lg    | 40 | 10 |  20  |  3  | 20 | 19   |
| XLarge   | xl    | 48 | 12 |  24  |  4  | 24 | 22   |
| 2XLarge  | 2xl   | 52 | 13 |  26  |  4  | 26 | 24   |

⚠ A derived font under 9px is a legibility risk. Flag it and prefer an
icon-only variant at that size.

## Rules

- **Height is emergent.** Build hug-content with `min-height: H` (add `min-width: H` when icon-only). Never fix the height; H = band + 2p by construction.
- **Text carries its own breathing room.** Wrap text in its own element with `padding-inline: 1u`. Solid shapes sit 3u from the edge, text 4u. Never move this inset onto the container. The band is tight to cap-height (line-height 6u), so descenders (g, y, p) overflow it by design and rely on the pill's padding for room — the text wrapper and any auto-layout hugging text must **not** clip content (Figma: `clipsContent = false`; CSS: no `overflow: hidden`). Only the outer pill clips, and only for its corner radius.
- **Child radius is R − p, never child/2.** The two agree only for pills. For squared corners compute R − p, clamped at 0.
- **Two container classes.** Spacious (padding 3u): children are band content or independent components — button, chip, input, toolbar. Dense (padding 1u): children are the control's own states or internals — segments, tab items, toggle thumb, loader rail. Dense children stand H − 2u tall with radius R − 1u, and each is a full concentric component inside (own 3u padding, own text inset).
- **Recursion runs down.** A slot inside the band is a concentric container with its own H = parent band. Re-apply every rule.
- **Recursion runs up.** A container holding components (toolbar, bar, row) is concentric too: its band = tallest child's H, so container H = child × 2, padding 3u, sibling gap 1u of the container. Smaller children center in the band. Never introduce a separate spacing scale between siblings.
- **Type does not recurse.** Font size derives from the top-level H; nested children inherit it. Primary labels: Medium (500). Text inside dense items (badges, segments): Regular (400).
- **Touch targets.** Below H=24, note that an invisible hit area of at least 24px should wrap the component.
- **Large radii.** Pills suit interactive elements at any H. For cards and panels above H=48, ask the user for R_max instead of silently emitting an egg.

## Anatomy

Build these structures; the formulas supply every measurement.

| Element         | Structure                                                 |
|-----------------|-----------------------------------------------------------|
| button          | pill( icon · text( label ) · badge )                      |
| icon button     | pill( icon ) with min-width H → square                    |
| chip            | pill( icon · text( label ) · remove circle at r = R − p ) |
| input           | field( icon · text( placeholder ) · icon button, opt. )   |
| segmented, tabs | dense track( segment × n ), each a full component         |
| toggle          | dense track( thumb circle ), width ≈ 2 × thumb + 2u       |
| badge           | dense pill at band size( digit, line height = height )    |
| toolbar, bar    | spacious container( components, centered in band )        |

## Audit mode

When asked to analyze or audit a component, or to suggest improvements:

1. Measure the container's actual height H; compute u = H/12.
2. Classify from the tallest child: child/H near 0.5 → spacious; above 0.7 → dense. A ratio between 0.55 and 0.7 fits neither class — report that as the finding.
3. Compare every actual value against the class formulas: padding, gap, radius, child sizes, child radius (R − p), text inset, font, line height.
4. Report a drift table — `property | expected | actual | drift` — and call out the two highest-impact drifts, one line each.
5. Propose the nearest concentric spec: snap to the closest ramp size (or keep the exact H if intentional) and print its full derivation table, ready to apply.

Never auto-fix during an audit. Report, propose, and fix only when asked.

## Output by context

- **Figma** → build via use_figma (load the figma-use skill first). Bind to the host file's variables when names match; otherwise use raw derived values.
- **CSS/HTML** → keep derivations live: `padding: calc(var(--size) / 4)`, `gap: calc(var(--size) / 12)`. Always `border: none`; UA borders break emergent height. Live calc() may stay fractional; whole-px rounding applies to static outputs.
- **Design tokens (DTCG)** → derived values rarely exist on a global scale. Add them to global/primitive tokens first, then alias from component tokens. Never put raw values in component tokens.

Always show the evaluated derivation table for the user's H in your response,
so every number is auditable.

## The concentric identity

Concentric nesting requires r = R − p. For a pill, R = H/2, and a child
filling the band stands H − 2p tall, so its natural pill radius is
(H − 2p)/2 = H/2 − p = R − p. The child's own pill radius equals the
concentric radius at any padding: nesting is correct by construction.
The identity silently breaks once corners square off (R < H/2), which is
why child radius must always be computed as R − p, never child/2.

## Do not

- Edit existing components during audits unless explicitly asked.
- Touch color, elevation, or motion. Concentric governs geometry only.
- Invent per-size overrides. If a value looks wrong at some H, flag it and propose retuning the named ratio globally (font k = 5.6/12, text inset, R_max).