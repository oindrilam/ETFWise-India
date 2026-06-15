# Phase 1: Data Design

## Goal

Decide data sources, table design, data quality rules, and seed file structure.

## Out of Scope for Phase 1

No backend, frontend, LLM, RAG, scheduler, or deployment in Phase 1.

## Priority

Data correctness is the most important part.

## Related Documents

- [Data Sources](../data/data_sources.md)
- [Table Design](../data/table_design.md)
- [Data Quality Rules](../data/data_quality_rules.md)

## Step 3 - ETF Master Table Design

Only the `etfs` master table has been designed in this step. See [Table Design](../data/table_design.md) for the full column definitions, validation rules, and seed file mapping.

The daily price/NAV table (`etf_daily_prices`) and metrics table (`etf_metrics`) are intentionally not designed yet. Those will be completed in later Phase 1 steps.

## Step 4 - ETF Daily Prices Table Design

The `etf_daily_prices` table has been designed in this step. See [Table Design](../data/table_design.md) for the full column definitions, validation rules, and data entry rules.

This table will store AMFI NAV and NSE market close price/volume data for daily ETF comparison. Real-time data and WebSockets are intentionally out of scope for MVP.

The metrics table (`etf_metrics`) is intentionally not designed yet. That will be completed in the next Phase 1 step.

## Step 5 - ETF Metrics Table Design

The `etf_metrics` table has been designed in this step. See [Table Design](../data/table_design.md) for the full column definitions, validation rules, and data entry rules.

This table will store TER/expense ratio, AUM, tracking error, and tracking difference for ETF comparison. AUM is intentionally nullable until a reliable scheme-level source is confirmed.

All three planned tables (`etfs`, `etf_daily_prices`, and `etf_metrics`) are now designed.

## Step 6 - Data Quality Rules

Data quality rules have been completed. See [Data Quality Rules](../data/data_quality_rules.md) for the full definitions.

The rules now cover:

- general data rules
- allowed ETF categories
- etfs table
- etf_daily_prices table
- etf_metrics table
- source traceability
- missing data handling
- no investment advice rule

## Step 7 - Phase 1 Review and Completion

Phase 1 review is complete. See [Phase 1 Review](phase_01_review.md) for the final summary, acceptance checklist, and completion status.

Phase 1 focused on data design and source planning only. Phase 2 has not started.
