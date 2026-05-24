# Changelog

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
