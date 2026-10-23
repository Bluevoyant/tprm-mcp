---
name: handle-cross-client-requests
description: Whenever a request spans multiple customers/clients/tenants/accounts at once (non-exhaustive list of phrasings — "across all clients", "which of our customers have the most X", "compare Y across the book of business", "totals across every tenant") against an MCP server whose data tools are scoped to answer for only ONE customer per call, draft the single-customer request once, enumerate the actual customers in scope, run it per customer, and aggregate the results yourself — do not answer from one customer's data, and do not query the whole org when the user named a subset.
---

# Handle requests that span multiple clients

## Why this exists

Some MCP servers scope their data tools to one customer/tenant per call —
there's no server-side "all customers at once" query. Left to its own
defaults, a host LLM either answers from whatever single customer is
already in context (wrong: the request was org-wide), or fires the same
query at every customer the org has when the user actually named a
specific subset (wrong: wastes calls, pulls in unwanted data). Both
mistakes happen upstream of any tool call, in how the request gets framed.

## What to do

1. **Recognize the shape.** Does this need one customer's data, or a set?
   Signals: "clients", "customers", "across", "all of", "compare … and …",
   "our portfolio". A request naming one customer isn't this case.
2. **Scope the set.** Named customers → resolve each by name/ID. "All of
   them" → list the full set the caller's token can access. Don't broaden
   to the whole org just because you can.
3. **Draft the request once**, using this server's normal query-drafting/
   validation flow — same fields/filters/aggregation for every customer,
   only the scoping identifier changes.
4. **Run it per customer.** Follow the server's own batching/rate-limit
   convention if it has one; otherwise use ordinary judgment (some
   concurrency for a handful, bounded batches or backoff for a large set).
   Don't build a queueing system for this.
5. **Aggregate it yourself** — no server-side tool reduces across
   customers. Rank, sum, or compare in your own reasoning/code, keeping
   each result tagged with which customer it came from.
6. **State the scope in the answer** — which customers it covers, and flag
   any that failed or returned nothing rather than silently dropping them.

## Worked example: `aiviz-mcp-server` (`tprm-prod`)

Its `execute_graphql`/`execute_graphql_resilient` tools run a GraphQL query
scoped to one SCD customer (`clientUuid`) per call — no multi-customer
query shape exists.

For "which of our clients have the most open findings":

1. Draft + validate the query once, this server's own way:
   `understand_question` → `get_rag_examples` → `get_type_fields`/
   `resolve_terms`/`find_filterable_fields`/`prolog_synthesize` →
   `get_query_generation_prompt` → `validate_query`.
2. Scope: `list_customers` for "all clients", or `resolve_customer` per
   named client.
3. Run the validated query once per `clientUuid` via `execute_graphql(_resilient)`.
4. Reduce the per-customer results into the requested shape yourself.
5. If the result becomes a chart/report, that's separate — follow this
   server's own chart/report tooling from there (see
   `check-mcp-report-ownership`).
