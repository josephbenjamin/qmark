# q-mark — Quiet Media Logo Builder

Interactive browser tool for designing the Quiet Media logo: a power symbol (broken circle + vertical bar).

## Run

```bash
python main.py
```

Opens a server at `http://localhost:3000`. Open that URL in VSCode via **Cmd+Shift+P → Simple Browser: Show**.

No dependencies beyond Python 3.12 stdlib.

## Controls

| Control | What it does |
|---|---|
| **Rotation** | Rotates entire symbol −180°→180° |
| **Gap / breathing room** | Angular width of circle break (degrees) |
| **Parallel to bar** toggle | Switches gap mode: slider now controls pixel padding beyond bar edge; gap auto-tracks bar weight |
| **Circle weight** | Stroke width of the arc |
| **Bar weight** | Stroke width of the bar line |
| **Linecap** | Round / Flat / Square ends on all strokes |
| **Start (inner reach)** | How far bar extends from center away from gap (% of radius; 0 = starts at center) |
| **End (outer reach)** | How far bar extends from center toward gap (% of radius; 100 = circle edge) |
| **Stroke / Background** | Color pickers |
| **Transparent background** | Omits background rect from exported SVG |
| **⇄ Swap colors** | Swaps stroke and background colors |

## Export

- **Download SVG** — saves `quiet-media-logo.svg`
- **Copy SVG code** — copies SVG markup to clipboard
