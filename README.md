# /concentric

A skill that generates UI components at a consistent concentric scale. Spacing, radius, gaps, icon sizes, and font size all come from the container's height, so every component size shares the same proportions on a single scale. The skill audits designs too: give it an existing component and it checks the geometry against the concentric principle.

## Install

```bash
git clone https://github.com/bunind/concentric ~/.claude/skills/concentric
```

## Usage

```
/concentric button lg
/concentric segmented control with 3 options, size md
/concentric chip xs in Figma
analyze this component using /concentric and suggest improvements
```

It outputs to three targets: Figma (via the Figma MCP), CSS/HTML (with live `calc()` derivations), and DTCG design tokens.

## How it works

Name a component and a size, and the skill builds it with every value derived from the height. Ask for `button md` and you get a 32px button with its padding, radius, gaps, and text all scaled to fit. Ask for several sizes and you get the whole set, every button in the same proportions. A size is a name or a raw pixel height:

| Name    | Alias | H  |
|---------|-------|----|
| 2XSmall | 2xs   | 16 |
| XSmall  | xs    | 20 |
| Small   | sm    | 24 |
| Medium  | md    | 32 |
| Large   | lg    | 40 |
| XLarge  | xl    | 48 |
| 2XLarge | 2xl   | 52 |

Under the hood, every value comes from the height H through a base unit `u = H/12`:

| Value        | Formula     | At H=24 |
|--------------|-------------|---------|
| padding      | 3u          | 6       |
| content band | 6u          | 12      |
| gap          | 1u          | 2       |
| radius       | H/2         | 12      |
| child radius | R − p       | 6       |
| font         | round(5.6u) | 11      |

## Audit

Ask for an analysis and you get a drift table (`property | expected | actual | drift`) showing where each value drifts from the system, plus the nearest concentric spec to apply. It only reports and suggests; it never edits anything itself.

## License

MIT © Dmitrii Bunin · [buninux.com](https://buninux.com)
