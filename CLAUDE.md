# CLAUDE.md — q-mark

Logo builder for Quiet Media. Single-page app served by Python stdlib HTTP server.

## Files

- `index.html` — entire frontend (HTML + CSS + JS, no build step)
- `main.py` — `http.server` wrapper, port 3000, silences access logs
- `pyproject.toml` — Python 3.12+, zero dependencies

## Architecture

All rendering is SVG, mutated live by JS on every input event. No framework, no state object — sliders read directly in `render()`.

### SVG coordinate convention

`CX=200, CY=200, R=130` (viewBox 400×400). Angle convention: **0° = up, clockwise positive** (compass bearing). Convert to SVG coords via:

```js
function pt(angle) {
  const a = toRad(angle);
  return [CX + R * Math.sin(a), CY - R * Math.cos(a)];
}
```

### Key render logic

**Bar**: two endpoints computed from center along direction vector `(sin θ, −cos θ)`. `innerReach` extends in `−dir`, `outerReach` in `+dir`, both as % of R.

**Arc**: SVG path `A` command, always sweep=1 (CW), large-arc flag = `spanDeg > 180 ? 1 : 0`. Gap is centered at `barAngle` (the outer/tip side of the bar). Arc runs from `barAngle + gapDeg` → `barAngle − gapDeg` the long way around.

### Gap modes

Two modes controlled by `#parallelGap` checkbox:

- **Angular** (default): `gapDeg` = slider value in degrees directly.
- **Parallel to bar**: `gapDeg = arcsin((barWidth/2 + padding) / R)`. Gap width is a physical pixel slot — tracks bar weight automatically. Slider controls `padding` (extra px beyond bar edge on each side).

`effectiveGapDeg()` encapsulates the mode logic. `onParallelToggle()` converts the slider value on switch so the gap doesn't jump visually.

## Extending

To add a new parameter: add a `<input type="range">` in the panel, read it in `render()`, apply to the relevant SVG attribute. The generic input listener at the bottom auto-wires it.

To change circle radius: update `R` constant. `pt()`, all arc/bar math, and parallel-gap `arcsin` all derive from `R`.
