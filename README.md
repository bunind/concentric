# /concentric

A skill that generates UI components at a consistent concentric scale.

/concentric uses the default container's height to generate spacing, radius, gaps, icons, and type, so every size stays in proportion without hand-tuning. Nested pieces stay aligned too: a badge in a button in a toolbar.

It also audits: point it at a component and it checks the geometry against the system.

## Install

```bash
git clone https://github.com/bunind/concentric ~/.claude/skills/concentric
```

## Usage

> /concentric button lg
>
> /concentric segmented control with 3 options, size md
>
> /concentric chip xs in Figma
>
> analyze this component using /concentric and suggest improvements

Outputs to Figma (via the Figma MCP), CSS/HTML with live `calc()`, or DTCG tokens.

## How it works

Ask the skill for `button md` and you get a 32px button with concentric spacing, radius, gaps, and text. Ask for several sizes to get the whole set in the same proportions. A size is a name or a raw pixel height:

| Name    | Alias | H  |
|---------|-------|----|
| 2XSmall | 2xs   | 16 |
| XSmall  | xs    | 20 |
| Small   | sm    | 24 |
| Medium  | md    | 32 |
| Large   | lg    | 40 |
| XLarge  | xl    | 48 |
| 2XLarge | 2xl   | 52 |

The concentric principle uses the container's height to do the math. Every value is a multiple of the base unit `u = H/12`:

| Value          | Formula     | At H=24 |
|----------------|-------------|---------|
| padding        | 3u          | 6       |
| content height | 6u          | 12      |
| gap            | 1u          | 2       |
| radius         | H/2         | 12      |
| nested radius  | R − p       | 6       |
| font           | round(5.6u) | 11      |

## Audit

Ask for an analysis and you get a drift table (`property | expected | actual | drift`) plus the nearest concentric spec to apply. The skill reports and suggests—never edits on its own.

## License

[MIT LICENSE](LICENSE)

© 2026 Dmitrii Bunin — [BuninUX](https://buninux.com)