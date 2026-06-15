# Data Quality Rules - ETFWise India

## Purpose

This document defines data quality rules for Phase 1 of ETFWise India.

The app will compare Indian ETFs using official/public data sources. If the data is wrong, incomplete, duplicated, or guessed, the chatbot output will become unreliable.

The goal is to make sure that every ETF value used later in the app is:

- traceable to a source
- not fake
- not guessed
- validated before use
- marked nullable where source mapping is not yet confirmed

## General Data Rules

1. Do not invent ETF data.
2. Do not add fake rows for testing in the seed ETF file.
3. Do not guess values such as ISIN, benchmark, AMFI scheme code, AUM, expense ratio, tracking error, or tracking difference.
4. Every populated field should have a traceable source.
5. Official/public sources should be preferred in this order:
   - NSE
   - AMFI
   - AMC factsheets
6. Third-party websites should not be used as primary data sources in MVP.
7. If a value is unclear, keep it blank and mark it for review.
8. If NSE and AMFI names do not match exactly, keep both names separately.
9. Do not merge NSE scheme name and AMFI scheme name into one field.
10. MVP should include only active ETFs from the selected categories.

## Allowed MVP Categories

Only these ETF categories are allowed in MVP:

| Category Code | Meaning |
| --- | --- |
| NIFTY_50 | ETF tracking Nifty 50 |
| SENSEX | ETF tracking Sensex |
| BANK | ETF tracking bank index |
| GOLD | Gold ETF |
| SILVER | Silver ETF |

Any ETF outside these categories should not be included in MVP Phase 1 seed data.

## Table 1: etfs Quality Rules

The etfs table is the ETF master table.

### Required Fields

| Field | Rule |
| --- | --- |
| symbol | Required. Cannot be blank. Must be unique. |
| isin | Required. Cannot be blank. Should be unique. |
| scheme_name_nse | Required. Cannot be blank. |
| category | Required. Must be one of NIFTY_50, SENSEX, BANK, GOLD, SILVER. |
| nse_source_url | Required. Cannot be blank. |
| is_active | Required. Must be TRUE or FALSE. |

### Nullable Initially

| Field | Rule |
| --- | --- |
| scheme_name_amfi | Nullable initially. Fill only after AMFI mapping is confirmed. |
| benchmark | Nullable initially. Do not guess. |
| amc | Nullable initially. Fill only after source verification. |
| amfi_scheme_code | Nullable. Fill only after AMFI mapping is confirmed. |
| amfi_source_url | Nullable initially. Required once AMFI values are populated. |

### etfs Rejection Rules

Reject or do not add an ETF row if:

| Situation | Action |
| --- | --- |
| symbol is missing | Do not add row |
| ISIN is missing | Do not add row |
| NSE source is missing | Do not add row |
| category is not part of MVP | Do not add row |
| category is guessed | Do not add row |
| ETF appears inactive | Do not add row until verified |
| row is copied from unofficial source only | Do not add row |

## Table 2: etf_daily_prices Quality Rules

The etf_daily_prices table stores daily NAV and market data.

### Required Fields

| Field | Rule |
| --- | --- |
| symbol | Required. Must already exist in etfs.symbol. |
| date | Required. Cannot be blank. Cannot be a future date. |

### Nullable Initially

| Field | Rule |
| --- | --- |
| nav | Nullable initially. If present, must be greater than 0. |
| market_close_price | Nullable initially. If present, must be greater than 0. |
| volume | Nullable initially. If present, must be 0 or greater. |
| trade_value | Nullable. If present, must be 0 or greater. |
| nav_source_url | Nullable initially. Required when nav is populated. |
| market_source_url | Nullable initially. Required when market_close_price or volume is populated. |

### Unique Rule

There should be only one row for each combination:

symbol + date

### etf_daily_prices Rejection Rules

Reject or do not add a daily price row if:

| Situation | Action |
| --- | --- |
| symbol does not exist in etfs table | Do not add row |
| date is missing | Do not add row |
| date is in the future | Do not add row |
| nav is negative or zero | Reject nav value |
| market_close_price is negative or zero | Reject market price value |
| volume is negative | Reject volume value |
| source URL is missing for populated value | Mark row incomplete or reject value |
| data appears to be real-time tick data | Do not use for MVP |

## Table 3: etf_metrics Quality Rules

The etf_metrics table stores comparison metrics such as expense ratio, AUM, tracking error, and tracking difference.

### Required Fields

| Field | Rule |
| --- | --- |
| symbol | Required. Must already exist in etfs.symbol. |
| metric_date | Required. Cannot be blank. Cannot be a future date. |

### Nullable Initially

| Field | Rule |
| --- | --- |
| expense_ratio | Nullable initially. If present, should be between 0 and 5. |
| aum_crore | Nullable initially. If present, must be 0 or greater. |
| tracking_error | Nullable initially. If present, cannot be negative. |
| tracking_difference | Nullable initially. Can be positive, zero, or negative. |
| benchmark | Nullable initially. Do not guess. |
| expense_ratio_source_url | Required when expense_ratio is populated. |
| aum_source_url | Required when aum_crore is populated. |
| tracking_source_url | Required when tracking_error or tracking_difference is populated. |

### Unique Rule

There should be only one metrics row for each combination:

symbol + metric_date

### etf_metrics Rejection Rules

Reject or do not add a metrics row if:

| Situation | Action |
| --- | --- |
| symbol does not exist in etfs table | Do not add row |
| metric_date is missing | Do not add row |
| metric_date is in the future | Do not add row |
| expense_ratio is guessed | Do not add value |
| AUM source is unclear | Keep AUM blank |
| tracking error is guessed | Keep tracking_error blank |
| tracking difference is guessed | Keep tracking_difference blank |
| source URL is missing for populated metric | Mark value incomplete or reject value |

## Source Traceability Rules

For every populated value later:

| Data Type | Source URL Required? |
| --- | --- |
| ETF symbol | Yes |
| ISIN | Yes |
| NSE scheme name | Yes |
| AMFI scheme name | Yes when populated |
| NAV | Yes |
| Market close price | Yes |
| Volume | Yes |
| Expense ratio | Yes |
| AUM | Yes when populated |
| Tracking error | Yes |
| Tracking difference | Yes |

## Handling Missing Data

Missing data should be handled explicitly.

| Situation | Correct Action |
| --- | --- |
| AMFI mapping not found | Keep AMFI fields blank |
| NAV not available | Keep nav blank |
| Market price not available | Keep market_close_price blank |
| Volume not available | Keep volume blank |
| AUM not reliable | Keep aum_crore blank |
| Tracking error not found | Keep tracking_error blank |
| Tracking difference not found | Keep tracking_difference blank |

Do not fill missing data using estimates unless a future phase explicitly defines a calculation method.

## Low Liquidity Flag Rule for Later

This rule is for later business logic, not Phase 1 implementation.

If volume is 0 or very low, the app should flag the ETF as potentially low-liquidity.

Phase 1 will not decide the low-liquidity threshold yet.

## No Investment Advice Rule

The app must not give personalized financial advice.

Allowed:

- educational comparison
- explaining ETF terms
- comparing expense ratio, tracking error, AUM, NAV, and volume
- showing source-backed data

Not allowed:

- telling user to buy a specific ETF
- telling user to sell a specific ETF
- giving personalized portfolio allocation
- promising returns
- ranking ETFs without explaining criteria and data limitations

## Phase 1 Data Quality Acceptance Checklist

Phase 1 data design is acceptable only if:

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
