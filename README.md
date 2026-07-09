# /concentric

`/concentric` generates UI components on a concentric scale, and audits existing ones against its principles.

> Concentric scaling is a technique to build components from a single value, the container's height (H). Every nested element shares the container's center, so spacing, elements, and proportions stay aligned at all sizes.

## Install

```bash
git clone https://github.com/bunind/concentric ~/.claude/skills/concentric
```

## Usage

- `/concentric button lg`
- `/concentric segmented control with 3 options, size md`
- `/concentric chip xs in Figma`
- analyze this component using `/concentric` and suggest improvements

Outputs to Figma and Paper (via MCP), CSS/HTML with live `calc()`, or DTCG tokens.

## How it works

Each component comes from a single request: a name and a size. `button sm` builds a 24px button with concentric padding, radius, gaps, and text. The same request across the ramp produces a matching set, every size in the same proportions. Change H and every size follows, so you never have to hand-tweak spacing and radius per size.

_A size can be a name or a raw pixel height_:

| Name    | Alias | H  |
|---------|-------|----|
| 2XSmall | 2xs   | 16 |
| XSmall  | xs    | 20 |
| Small   | sm    | 24 |
| Medium  | md    | 32 |
| Large   | lg    | 40 |
| XLarge  | xl    | 48 |
| 2XLarge | 2xl   | 52 |

Every value is a multiple of the base unit `u = H/12`. At H=24, u is 2:

| Value          | Formula     | At H=24 |
|----------------|-------------|---------|
| padding        | 3u          | 6       |
| content height | 6u          | 12      |
| gap            | 1u          | 2       |
| radius         | H/2         | 12      |
| nested radius  | R − p       | 6       |
| font           | round(5.6u) | 11      |

## Audit

Point the skill at an existing component and it returns a drift table (`property | expected | actual | drift`) with the nearest concentric spec to apply. It reports and suggests, but never edits on its own.

## License

[MIT LICENSE](LICENSE)

© 2026 Dmitrii Bunin — [BuninUX](https://buninux.com)
