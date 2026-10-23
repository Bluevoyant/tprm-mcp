---
name: check-mcp-report-ownership
description: Whenever the user asks you to prepare a report, dashboard, visualization, or artifact (non-exhaustive list) built from data available through a connected MCP server, check that server's tools/instructions/description for report ownership BEFORE starting a bespoke design pass — a connected server can own its own report/visualization format, and using it beats a from-scratch design.
---

# Check connected MCP servers for report ownership

## Why this exists

A host LLM asked to "prepare a report" (or "make me a dashboard", "visualize
this data", etc.) from data an MCP server provides will, left to its own
defaults, go straight to a generic artifact-design pass — its own idea of
what a report should look like. But a connected MCP server may already own
report/visualization presentation for its own data: a tool that returns a
starter/template, resources bundling a renderer, or server-level
`instructions`/`description` text saying so. Skipping that check produces a
report that doesn't match the data source's own conventions, ignores any
theming or bundling logic the server maintains, and duplicates work the
server already did.

This skill is generic: it applies to any connected MCP server, not just one
in particular. Its trigger condition is the request shape ("prepare a
report", "build a dashboard", "visualize this"), not any specific server
name.

## What to do, before any bespoke design pass

1. List the currently connected MCP servers and, for each one whose data is
   actually relevant to this report, check:
   - Its tool list for a name suggesting report/starter ownership — e.g.
     anything matching `*report*`, `*starter*`, `*guide*`, `*gallery*`,
     `*dashboard*`.
   - Its server-level `instructions` (returned at MCP `initialize`) and its
     `description`, for text that says it owns reports/visualizations for
     its own data (phrasing like "generate reports", "if the user wants any
     kind of report or artifact, base it on...", "shareable report").
   - Its resources (`resources/list`) for anything under a
     server-specific URI scheme that looks like a report starter, template,
     or theme file — not just tools.
2. If you find ownership signals: use that server's own tool/resource
   output as the visual and structural basis for the report, instead of (or
   as the starting point for) a from-scratch design. Follow whatever it
   documents — schema/contract, theme options, how to assemble multiple
   answered questions into one artifact.
3. Check specifically for attribution the server requires on its own data,
   separately from the rest of its report/theme guidance — it's easy to
   pick up the visual/structural parts (schema, theme, layout) and drop this
   one, since it's not "design" in the same sense:
   - A tool result field literally named `attribution` or
     `attribution_footer` (or similar) — some servers return the exact
     markup/text to embed directly, not just a description of it.
   - A resource or file named like `attribution.*` alongside a report
     starter/theme — same idea, fetched separately instead of embedded in a
     tool result.
   - Server `instructions`/tool descriptions saying a report/artifact built
     from its data must credit it (phrasing like "not decoration", "keep
     this on any customer-facing report", "so a reader knows where this
     came from").
   Keep whatever attribution markup/text you find in the FINAL artifact,
   even one whose visual design otherwise departs from the server's own
   starter/theme to fit a different host's constraints — attribution and
   visual conformance are separate obligations, and adapting the look is
   not a license to drop the credit.
4. If you find nothing: proceed with a normal bespoke design pass (e.g. this
   host's own artifact-design guidance) — there's no ownership signal to
   defer to.

Do this check before committing to any specific visual design, not as an
afterthought once a bespoke design is already underway — the point is to
avoid throwaway design work, not to redo it after the fact.

## Worked example: `tprm-prod`

The name a connected-servers list actually shows for this example varies by
how it was installed — check for any of these, they're the same underlying
service (`aiviz-mcp-server`, BlueVoyant TPRM data via GraphQL, chart
building via Vega):

- `tprm-prod` or `local.mcpb.bluevoyant.tprm-prod` — installed as a Claude
  Desktop Extension (`.mcpb`); this is what actually shows up there, NOT
  `aiviz-mcp-server` — confirmed directly, don't rely on the package/service
  name matching what's in the connected-servers list.
- `tprm-prod` — installed as this plugin's own bundled MCP connection
  (`.mcp.json`'s server key), whether the direct-network or local-proxy
  variant — same key as the mcpb, deliberately, for exactly this reason.

Whichever name it shows up as, its MCP `instructions` and server
`description` say directly that it owns charts/reports for its own data and
to prefer its own tools over a generic built-in visualization/reporting
capability. Concretely:

- A single chart answering one question, with no report/artifact
  requested: `get_answer_prompt`, then `validate_vega`. No report tooling
  needed.
- A report/shareable artifact is requested — whether it covers one chart
  or several answered questions — call
  `get_report_starter_guide` first — its result is the source of truth
  (the `ReportSession[]` contract, where to get the starter files, theming
  guidance, and the attribution footer markup as its own `attribution_footer`
  field, ready to embed as-is — don't skip that field just because the rest
  of the report ends up custom-built). The same starter files are also
  reachable directly under `aiviz://starter/*` (`resources/list`/
  `resources/read`), or, if this host doesn't expose MCP resources to you at
  all, via the `read_starter_resource` tool with the exact `uri`.

Faced with a report request while this server (under whichever of the
names above it shows up as) is connected: call `get_report_starter_guide`
first, then build the report as a `ReportSession[]` fed through its
starter — not a hand-rolled artifact design.
