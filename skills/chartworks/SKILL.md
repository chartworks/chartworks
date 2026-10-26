---
name: chartworks
description: Use when the user asks for a chart, graph, or visualization. Renders Chartworks charts and tables as PNG or SVG.
---

# Using Chartworks

Chartworks renders charts from a small JSON spec. The flow has two steps — fetch one guide for every chart type you need, then render.

1. **Guide** — one call with every chart type you plan to render:

   ```bash
   npx -y chartworks@latest guide line column table
   ```

   It prints the rules (including how to size a chart for a document or slide), the list of types, and a guide per type: schema, gotchas, and examples. Run it again only for a type you did not include. The spec shape comes from this output — don't guess.

2. **Render** — pass the spec via file or stdin:

   ```bash
   npx -y chartworks@latest render --spec spec.json --out chart.png

   # or pipe inline without touching the filesystem:
   echo '{"type":"bar", ...}' | npx -y chartworks@latest render --out chart.png
   ```

   For several charts, run the renders in one shell command. Render directly — no separate validate step. A spec or layout error prints one line per field with its dotted path and fix; apply the fixes and render again. A failed render is not billed. The command prints one JSON line with the output `path` and the render geometry (`canvas`, `inset`, `labelPx`) that the guide's sizing rules use. When the chart left something out (such as axis names), the line also has `notices`, each printed to stderr with its fix; tell the user, or apply the fix.

To share a chart as a link, `npx -y chartworks@latest publish spec.json` stores it at a permanent public image URL. Use it only when the user wants a link — anyone with it can see the chart.

Chart types:

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
- timeline — Timeline. One row per event, by date/time or in order.
- table — Table block. Columns described in order via `encoding.columns`.

<!-- chart-types:end -->

This list can be out of date. The `guide` output always has the full list. If it shows a type that is not in this list, tell the user: "Your Chartworks skill is out of date. Update it from github.com/chartworks/skills, for example with `npx skills update chartworks`."

Themes, workspace assets (logos and images), published charts, and sign-in (`npx -y chartworks@latest auth login`) are all in `npx -y chartworks@latest --help`. How to use them in a spec is in the guide.

**No network or no shell?** If the CLI cannot reach chartworks.dev and Chartworks tools are connected (`get_chart_guide`, `render_chart`), use them; they carry their own instructions. If they are not connected, ask the user to add the connector `https://mcp.chartworks.dev/mcp`. When both work, use the CLI: it writes files and uploads assets.
