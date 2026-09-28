---
name: chartworks
description: Use when the user asks for a chart, graph, or visualization. Renders Chartworks charts, tables, callouts, and compositions as PNG or SVG via the Chartworks CLI.
---

# Using Chartworks

Chartworks renders charts from a JSON spec. The agent flow has two steps — fetch one guide for every chart type you need, then render.

1. **Guide** — one call with every chart or block type you plan to render:

   ```bash
   npx chartworks guide line column table
   ```

   It prints the general rules, the list of types, the schema parts those types share (once), and a guide per type: schema, gotchas, and examples. Run it again only for a type you did not include. With no types, it prints the rules and the type list.

2. **Render** — write the chart to disk. Pass the spec via file or stdin:

   ```bash
   npx chartworks render --spec spec.json --out chart.png

   # or pipe inline without touching the filesystem:
   echo '{"type":"bar", ...}' | npx chartworks render --out chart.png
   ```

   For several charts, run the renders in one shell command. Render directly — no separate validate step. A spec error prints one line per field with its dotted path; fix those fields and render again. A failed render is not billed.

Chart and block types:

<!-- chart-types:start -->

- bar — Horizontal bars. Categories on y-axis, values on x-axis.
- column — Vertical bars. Categories on x-axis, values on y-axis.
- combo — Column + line chart with optional right-axis line series.
- line — Wide-format line chart. Each series is its own quantitative column.
- area — Stacked or single-series area chart. Same wide-format tidy data as line.
- mekko — Marimekko. Column widths reflect column totals; segments stack to 100%.
- circle-pack — Hierarchical bubbles — leaves sized by value, optionally clustered by group.
- scatter — Scatter plot. One row per point on two quantitative axes; add `size` for a bubble chart, `quadrants` for a 2x2.
- pie — Pie chart. One row per slice.
- donut — Donut chart (pie with a fixed inner radius). One row per slice.
- timeline — Timeline. One row per event with an ISO date.
- table — Table block. Columns described in order via `encoding.columns`.

<!-- chart-types:end -->

This list can be out of date. The `guide` output always has the full list. If it shows a type that is not in this list, tell the user: "Your Chartworks skill is out of date. Update it from github.com/chartworks/skill, for example with `gh skill install chartworks/skill`."

The spec describes one chart or a composition of blocks. The shape comes from step 1 — don't guess. For small multiples, use the chart's `facet` channel when its guide/schema supports it. Usually omit `facet.columns`; Chartworks picks an aspect-aware grid by default.

For exact output dimensions (fixed slots), set `dimensions: { "width": 960, "height": 540 }` in the spec — authoritative for SVG and the PNG logical canvas, tables included. `aspect` plus one of `width`/`height` derives the other; never send all three. PNG pixels = canvas × `--scale` (default 2, range 1-4): 960x540 at `--scale 1` is exactly 960x540, at `--scale 2` exactly 1920x1080. Scale changes resolution only, never layout; SVG ignores it. Oversized requests fail with the failing limit named (canvas ≤ 4096px/side; PNG output ≤ 8192px/side and ≤ 8,000,000px² area).

**Sizing a chart that gets placed in a document or slide.** The render command prints one JSON line with the output `path` and a `render` object containing the logical `canvas`, outer `inset`, and resolved `labelPx`. Text inside the chart is sized from the canvas. Compute its placed size rather than guessing:

```
displayed label size (pt) = (label_px ÷ canvas_width_px) × 72 × placed_width_inches
```

If the result does not match the host text, set `dimensions.typeScale` from 0.6 through 2 and render again. The multiplier applies after the canvas-derived size clamp. It scales chart text and the outer inset; canvas size, output pixels, and placement stay unchanged. Titles, subtitles, block titles, footnotes, and attribution stop scaling above 1.25; chart content continues to 2. Read the new `labelPx` and `inset` from the result. For PNG pixel cropping, multiply the logical inset by `--scale`.

Axis labels are about 0.025 × canvas width at 400x225, falling to about 0.014 at 960x540 and above — the base type size clamps, so a bigger canvas makes type _smaller_ relative to the figure. Two anchors:

- **Report figure, ~6in text column, 11pt body text** — use `{ "width": 400, "height": 225 }` with `--scale 4`. Labels land near 10.8pt, output is 1600x900px at ~267 DPI. The same figure from a 1600x900 canvas gives roughly 6pt labels and 4pt footnotes, which is unreadable next to body text.
- **Slide, 10-13in content box** — use `{ "width": 600, "height": 400 }` or larger. Labels land 12-16pt against typical 18-24pt slide text.

Raise `--scale` for print density; it never changes layout or type size. Use one canvas size across every figure in a set so type stays consistent between them.

To browse themes: `npx chartworks themes`.

To list workspace assets (logos and images you've uploaded): `npx chartworks assets list`. Use `--prefix acme/` to list one virtual folder recursively. Reference one in a spec by its complete handle, such as `asset:acme/logos/primary` — list first rather than guessing.

To upload one: `npx chartworks assets put ./acme-logo.png --name acme/logos/primary` (PNG, JPEG, or WebP; without `--name`, the handle defaults to the filename). Uploading over an existing complete handle needs `--force`. This needs a key with `asset:write`, which `npx chartworks auth login` requests by default. Delete with `npx chartworks assets rm <handle>`.

For SVG output add `--format svg`. For all options: `npx chartworks --help`.
