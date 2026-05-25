# Changelog

## 0.1.4 (2026-05-25)

Tool count: **27 → 31**. Closes 4 of the GovTribe-comparison gaps using real
data (no fake claims). Server-side only — npm clients pick up new tools
automatically on next `tools/list`.

- **`search_federal_grants`** — query grants.gov + 40K historical grants in
  BidSparq's archive. Filters: keyword, agency, state, due-date range,
  active vs historical.
- **`list_agencies`** — federal agency directory with per-agency award count,
  total $, top NAICS, last-award-date. Optional NAICS filter to show only
  agencies buying in a given space.
- **`naics_lookup`** — Census 2022 NAICS taxonomy (2,125 codes, levels 2–6).
  Lookup by exact code or fuzzy title keyword. Returns parent_code for
  hierarchy navigation.
- **`psc_lookup`** — acquisition.gov Product Service Codes April 2025
  (3,837 codes, 2,540 currently active). Lookup by code, keyword, or
  category prefix (e.g. 'D' for IT services, '70-79' for IT equipment).

## 0.1.3 (2026-05-25)

Tool count: **18 → 27**. Server-side only — the npm package is a thin proxy,
so `npx -y bidsparq-mcp-server@latest` picks up new tools without a re-install.
This release bumps the version reported via the MCP `initialize` handshake +
the Registry listing (com.bidsparq/mcp → 0.3.0).

**Sprint 1 — Personal relevance** (uses BidSparq's 75K-row contractor×RFP
AI-scoring engine; signed-in OAuth user required):

- **`get_my_top_matches`** — the user's highest-AI-scored ACTIVE RFPs with
  ai_score + llm_pwin (Shipley) + decision verdict + reason. The "what should
  I bid on right now?" tool.
- **`explain_my_fit`** — given an rfp_id, returns the user's personalized
  score breakdown (factor_scores, compliance_gaps, wired_risk). Answers
  "why did this RFP score X?".
- **`find_similar_active_rfps`** — given an rfp_id, query-by-example via
  OpenAI embeddings + pgvector HNSW. Finds RFPs whose scope reads like the
  source even when titles+NAICS differ.

**Sprint 2 — Competitive analytics** (built on 164K federal awards):

- **`competitive_landscape`** — top winners, win concentration (top-1/3/10
  share %), avg bidders, set-aside mix, competition extent. The "who am I
  really up against?" tool.
- **`set_aside_analysis`** — per-set-aside breakdown: award count, avg
  bidders, top vendor, easier-than-baseline flag. Use to evaluate
  certification ROI.
- **`upcoming_deadlines`** — active RFPs closing in the next N days, filter
  by NAICS/agency/state/set_aside_only.

**Historical intelligence** (built on the 1.6M closed-RFP corpus):

- **`vendor_win_history`** — per-vendor federal award history. By UEI exact or
  vendor_name fuzzy. Returns per-award detail + top agencies + cadence.
- **`budget_intelligence`** — p25/median/p75 PRE-RFP posted budget ranges.
  Distinct from `analyze_pricing_intel` (POST-award $).
- **`contract_duration_intel`** — typical contract length from 1.6M closed
  RFPs. Parses '1 year' / '36 months' / '5 years' patterns.

System prompts on all 3 BidSparq chat surfaces (main /ai, public landing,
per-RFP) updated to surface the new tools so the LLM actually reaches for
them.

## 0.1.2 (2026-05-25)

- Add `mcpName: com.bidsparq/mcp` field to package.json so the Official MCP
  Registry can cross-link the npm package to our DNS-verified namespace.
- Bump server version reported via MCP `initialize` to match.
- Source now public at https://github.com/n0edlg/bidsparq-mcp-server.

## 0.1.1 (2026-05-24)

Server-side bug fixes (no client API changes — re-installs are optional).

Tool behavior improvements:

- **`get_agency_profile`** — abbreviation routing so `"VA"` resolves to Veterans
  Affairs instead of City of Vancouver. NAICS codes no longer wrapped in
  `{...}` braces. Filters $1T+ ceiling-amount sentinels and "Unknown Vendor"
  rows from top stats. Ranks top-level agencies above sub-offices. Adds
  `query_resolution` block with confidence level.
- **`find_recompetes`** — `matching_rfps` now requires NAICS match (not OR
  with agency), so IT recompetes no longer pair with unrelated lab-equipment
  RFPs.
- **`find_subaward_primes`** — dedupes primes by SAM UEI across name variants
  (e.g., Perspecta + Peraton merger). Surfaces `name_variants` array.
- **`filter_rfps`** — supports NAICS prefix matching (`5415`) consistent with
  every other NAICS-accepting tool. Always hydrates `wired_signals` and adds
  `has_wired_signals` boolean.
- **`compare_rfps`** — surfaces `unresolved_ids` array + warning when input
  IDs don't exist (previously silently dropped).
- **`semantic_search`** — filters rfpmart page boilerplate out of chunk
  scoring (country-list noise was depressing relevance ranking).
- **`get_rfp_details`** — adds `enrichmentStatus: complete | pending | no_document`
  so callers know when AI fields are null because of pending enrichment vs
  missing data.
- **`search_rfp_documents`** — surfaces `document_indexed: false` when the
  document is extracted but not yet chunked into the search index.
- **`search_historical_bids`** — `2099-12-31` "no close date known" sentinel
  is now returned as `null`.
- **`get_trending`** — adds `metric_note` clarifying that the weekly counts
  reflect BidSparq ingestion timing (which is batch-driven), not original
  agency posting date.
- **`count_rfps`** — filters scraper platform names (`bids`, `Bonfire`,
  `Onvia`, `FindRFP`, etc.) from agency groupby.

Schema fix:

- `rfps.estimated_value_cents` widened from `integer` (capped at $21.5M) to
  `bigint`. 1,509 rows previously clamped at INT32_MAX were null-ed so the
  next scrape cycle can backfill the true values from upstream sources.

## 0.1.0 (2026-05-24)

Initial release. 18 tools across RFP discovery, market intel, and account.
