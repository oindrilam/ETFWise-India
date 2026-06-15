# Phase 1 Review - ETFWise India

## Phase 1 Scope

Phase 1 focused only on data design and source planning for the ETFWise India AI ETF Comparison Assistant.

No backend, frontend, data ingestion scripts, scheduler, RAG, Groq integration, vector database, deployment, or Android app work was started in this phase.

## Completed Items

- Project architecture document created.
- Phase-wise documentation structure created.
- Official/public data sources documented.
- ETF master table designed.
- ETF daily prices table designed.
- ETF metrics table designed.
- Data quality rules documented.
- Seed ETF master CSV header created.
- MVP categories fixed as NIFTY_50, SENSEX, BANK, GOLD, SILVER.

## Files Created or Updated

- README.md
- docs/architecture/ARCHITECTURE.md
- docs/phases/phase_01_data_design.md
- docs/phases/phase_01_review.md
- docs/data/data_sources.md
- docs/data/table_design.md
- docs/data/data_quality_rules.md
- data/seed_etf_master.csv
- notes/phase1_todo.md

## Data Sources Confirmed

The following official/public sources are planned for MVP data ingestion:

- NSE Securities Available for Trading
- NSE ETF Market Watch
- NSE All Reports / Historical Reports
- AMFI NAV Download
- AMFI TER of MF Schemes
- AMFI Tracking Error and Tracking Difference
- AMFI Average AUM
- AMC Factsheets

Primary source priority is NSE, then AMFI, then AMC factsheets. Third-party aggregator sites are not primary MVP sources.

## Tables Designed

1. **etfs** — Master table for one row per MVP ETF, with separate NSE and AMFI scheme name fields.
2. **etf_daily_prices** — Daily NAV from AMFI and end-of-day market close price/volume from NSE.
3. **etf_metrics** — Non-daily comparison metrics including expense ratio, AUM, tracking error, and tracking difference.

## Data Quality Decisions

- No invented or fake ETF data.
- No sample ETF rows added to the seed file.
- Required and nullable fields are defined per table.
- Source traceability is required for populated values.
- AUM remains nullable until a reliable scheme-level source is confirmed.
- Real-time prices and WebSockets are out of MVP scope.
- Educational comparison only; not investment advice.

## Seed File Status

`data/seed_etf_master.csv` contains the approved header only:

`symbol,isin,scheme_name_nse,scheme_name_amfi,category,benchmark,amc,amfi_scheme_code,nse_source_url,amfi_source_url,is_active`

No ETF rows have been added yet.

## Not Started in Phase 1

- Data download or scraping
- Database creation
- Seed ETF population
- RAG / vector store
- Groq LLM integration
- Business logic
- Backend API
- Frontend UI
- Scheduler
- Deployment
- Android / Google Play Store work

## Phase 1 Acceptance

Phase 1 is complete when all of the following are true:

- ETF categories are clearly defined.
- All 3 tables have validation rules.
- Required and nullable fields are clearly marked.
- Source traceability rules are written.
- No fake ETF values are added.
- Missing data handling is documented.
- Official sources are prioritized.
- AUM is kept nullable until reliable scheme-level source is verified.
- Real-time data and WebSockets are out of MVP scope.
- The app disclaimer remains educational comparison only, not investment advice.

All acceptance criteria above are met.

## Phase 1 Result

**Phase 1 is complete.**

The project is ready to move to Phase 2 (data ingestion scripts) only after a deliberate decision to start Phase 2. Phase 2 has not started.

## Related Documents

- [Architecture](../architecture/ARCHITECTURE.md)
- [Phase 1 Data Design](phase_01_data_design.md)
- [Data Sources](../data/data_sources.md)
- [Table Design](../data/table_design.md)
- [Data Quality Rules](../data/data_quality_rules.md)
