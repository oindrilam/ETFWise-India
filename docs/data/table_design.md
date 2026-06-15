# Table Design - ETFWise India

## 1. etfs

### Purpose

The etfs table is the master table for all ETFs included in the MVP. It stores one row per ETF. This table will be used to identify each ETF before joining with NAV, market price, volume, expense ratio, tracking error, tracking difference, and AUM data.

### Why this table is needed

The same ETF may have:

- one NSE trading symbol
- one ISIN
- one AMFI scheme name
- one NSE display name
- one AMC/fund house
- one benchmark/category

NSE and AMFI names may not match exactly, so separate NSE and AMFI scheme name columns are needed.

### Planned MVP Categories

Allowed category values:

- NIFTY_50
- SENSEX
- BANK
- GOLD
- SILVER

### Table Columns

| Column Name | Data Type | Required or Nullable | Meaning | Primary Source | Validation Rule | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| symbol | TEXT | Required | NSE trading symbol for the ETF | NSE Securities Available for Trading - ETF CSV | Cannot be blank. Must be unique. | This will be the primary business identifier used in app queries. |
| isin | TEXT | Required | ISIN code of the ETF | NSE Securities Available for Trading - ETF CSV | Cannot be blank. Should be unique. | Useful for matching ETF across NSE, AMFI, and AMC sources. |
| scheme_name_nse | TEXT | Required | ETF name as shown by NSE | NSE Securities Available for Trading or NSE ETF Market Watch | Cannot be blank. | NSE name may differ from AMFI scheme name. |
| scheme_name_amfi | TEXT | Nullable initially | ETF scheme name as shown by AMFI | AMFI NAV Download | Can be blank during initial seed creation. Must be filled once AMFI mapping is confirmed. | Needed for NAV matching. |
| category | TEXT | Required | MVP category assigned to ETF | Manual classification based on ETF name, benchmark, NSE/AMC details | Must be one of NIFTY_50, SENSEX, BANK, GOLD, SILVER. | Do not guess category if unclear. Leave row out of MVP until verified. |
| benchmark | TEXT | Nullable initially | Index or asset tracked by ETF | NSE, AMC factsheet, AMFI data if available | Can be blank initially. Should be filled only after verification. | Examples may include Nifty 50, Sensex, Nifty Bank, Gold, Silver. Do not add fake values. |
| amc | TEXT | Nullable initially | Asset Management Company / fund house | AMFI scheme name or AMC factsheet | Can be blank initially. Must be verified before final MVP. | Example values should not be invented. |
| amfi_scheme_code | TEXT | Nullable | AMFI scheme code if available | AMFI NAV Download | Can be blank if AMFI mapping is not done yet. | Helpful for reliable NAV lookup later. |
| nse_source_url | TEXT | Required | NSE source link used to identify ETF | NSE source page | Cannot be blank. | Stores traceability. |
| amfi_source_url | TEXT | Nullable initially | AMFI source link used for scheme name/NAV mapping | AMFI NAV Download | Can be blank initially. Required after AMFI mapping. | Stores traceability. |
| is_active | BOOLEAN | Required | Whether ETF is active for MVP | NSE active listed ETF source | Must be TRUE or FALSE. | MVP should include active ETFs only. |
| created_at | DATETIME | Required later | Row creation timestamp | System generated | Auto-generated later when database is created. | Not needed in CSV seed file right now. |
| updated_at | DATETIME | Required later | Last row update timestamp | System generated | Auto-generated later when database is created. | Not needed in CSV seed file right now. |

### Primary Key Decision

Use symbol as the MVP business key.
Later, when database is created, use an internal id as the technical primary key and keep symbol unique.

### Important Design Decision

Do not merge NSE scheme name and AMFI scheme name into one field. Keep both separately because source names may not match exactly.

### CSV Seed File Mapping

The current data/seed_etf_master.csv should keep this header only:

symbol,isin,scheme_name_nse,scheme_name_amfi,category,benchmark,amc,amfi_scheme_code,nse_source_url,amfi_source_url,is_active

Do not add created_at and updated_at to the CSV seed file for now.

### Data Entry Rule for Later

When ETF rows are added later:

- Do not add any row without verified NSE symbol.
- Do not add any fake or guessed ISIN.
- Do not guess benchmark.
- Do not guess AMFI scheme code.
- If AMFI mapping is unclear, leave AMFI fields blank and mark for review.

## 2. etf_daily_prices

### Purpose

The etf_daily_prices table stores daily ETF values used for comparison. It combines NAV data from AMFI and market trading data from NSE.

This table will help the app answer questions such as:

- What is the latest NAV of an ETF?
- What was the market close price?
- How much was the traded volume?
- Is the ETF trading near NAV?
- Does the ETF have enough liquidity?

### Why this table is needed

ETF comparison needs both NAV and market data.

NAV tells the fair value of the ETF unit.
Market close price tells what price it traded at on the exchange.
Volume helps identify liquidity.
The difference between NAV and market price can later help show premium or discount.

### Planned Data Sources

AMFI NAV Download:
Used for NAV values.

NSE ETF Market Watch:
Used for market close price and traded volume.

NSE Historical Reports:
Used as backup source for historical price/volume if needed.

### Table Columns

| Column Name | Data Type | Required or Nullable | Meaning | Primary Source | Validation Rule | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| id | INTEGER | Required later | Internal database row ID | System generated | Auto-generated when database is created. | Not needed in CSV seed file. |
| symbol | TEXT | Required | NSE ETF trading symbol | etfs table | Must exist in etfs.symbol. | Used to join daily data with ETF master table. |
| date | DATE | Required | Date for NAV and market data | AMFI/NSE | Cannot be blank. Cannot be a future date. | One ETF should have only one row per date. |
| nav | DECIMAL | Nullable initially | Net Asset Value of ETF unit | AMFI NAV Download | If present, must be greater than 0. | Nullable initially because AMFI mapping may not be complete for all ETFs. |
| market_close_price | DECIMAL | Nullable initially | ETF closing/last traded market price on NSE | NSE ETF Market Watch or NSE Historical Reports | If present, must be greater than 0. | Use end-of-day value, not real-time price. |
| volume | INTEGER | Nullable initially | Number of ETF units traded | NSE ETF Market Watch or NSE Historical Reports | If present, must be 0 or greater. | Volume can be 0, but later app should flag low liquidity. |
| trade_value | DECIMAL | Nullable | Total traded value if available | NSE ETF Market Watch or NSE Historical Reports | If present, must be 0 or greater. | Optional for MVP. |
| nav_source_url | TEXT | Nullable initially | AMFI source URL used for NAV | AMFI NAV Download | Required when nav is populated. | Used for traceability. |
| market_source_url | TEXT | Nullable initially | NSE source URL used for market price and volume | NSE ETF Market Watch or NSE Historical Reports | Required when market_close_price or volume is populated. | Used for traceability. |
| created_at | DATETIME | Required later | Row creation timestamp | System generated | Auto-generated later when database is created. | Not needed in manual seed CSV now. |
| updated_at | DATETIME | Required later | Last update timestamp | System generated | Auto-generated later when database is created. | Useful when scheduler is added later. |

### Unique Key Decision

For MVP, each ETF should have only one daily price row per date.
Unique combination:

symbol + date

### Important Design Decision

Do not use real-time stock price data in MVP.
Use daily/end-of-day NAV and market close price only.
No WebSockets are needed for this project scope.

### Premium or Discount Calculation

Do not store premium/discount in this table initially.
Later, business logic can calculate it using:

premium_discount_percent = ((market_close_price - nav) / nav) * 100

Only calculate this when both nav and market_close_price are available.

### Data Entry Rule for Later

When daily price rows are added later:

- Do not add a row if symbol is not present in etfs table.
- Do not add fake NAV values.
- Do not add fake market prices.
- Do not guess missing volume.
- If AMFI NAV is unavailable, keep nav blank.
- If NSE market price is unavailable, keep market_close_price blank.
- Always store the source URL when a value is populated.

### Sample Questions This Table Will Support Later

- Show latest NAV for NIFTYBEES.
- Compare market price vs NAV for two ETFs.
- Which ETF has higher traded volume?
- Which ETF may have low liquidity?
- Show daily movement for an ETF.

## 3. etf_metrics

### Purpose

The etf_metrics table stores non-daily comparison metrics for ETFs.

This table will help the app compare ETFs using:

- expense ratio / TER
- AUM
- tracking error
- tracking difference

These values may not change daily like NAV or market price. Some may update monthly, quarterly, or when AMC/AMFI publishes new data.

### Why this table is needed

ETF comparison should not depend only on return or latest price.

For long-term ETF comparison, users need to know:

- Is the ETF low cost?
- Is the ETF large enough?
- Does it track its benchmark properly?
- Is tracking error low?
- Is tracking difference acceptable?

### Planned Data Sources

AMFI TER of MF Schemes:
Used for Total Expense Ratio / expense ratio.

AMFI Tracking Error and Tracking Difference:
Used for tracking error and tracking difference.

AMFI Average AUM:
Possible source for AUM, but AUM will remain optional until scheme-level reliability is verified.

AMC Monthly Factsheets:
Backup/validation source for AUM, expense ratio, benchmark, tracking error, tracking difference, and fund details.

### Table Columns

| Column Name | Data Type | Required or Nullable | Meaning | Primary Source | Validation Rule | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| id | INTEGER | Required later | Internal database row ID | System generated | Auto-generated when database is created. | Not needed in CSV seed file. |
| symbol | TEXT | Required | NSE ETF trading symbol | etfs table | Must exist in etfs.symbol. | Used to join metrics with ETF master table. |
| metric_date | DATE | Required | Date/month/period for which metric is valid | AMFI or AMC factsheet | Cannot be blank. Cannot be a future date. | For monthly data, use the last date of the month or source-published date consistently. |
| expense_ratio | DECIMAL | Nullable initially | Total Expense Ratio / annual fund cost percentage | AMFI TER of MF Schemes | If present, should be between 0 and 5. | Store as percentage, for example 0.05 means 0.05 percent if source reports percentage. |
| aum_crore | DECIMAL | Nullable initially | Assets under management in crore rupees | AMFI Average AUM or AMC factsheet | If present, must be 0 or greater. | Keep optional until reliable scheme-level AUM source is confirmed. |
| tracking_error | DECIMAL | Nullable initially | Difference/volatility of ETF returns compared with benchmark returns | AMFI Tracking Error | If present, cannot be negative. | Lower tracking error is usually better for index ETF comparison. |
| tracking_difference | DECIMAL | Nullable initially | Return difference between ETF and benchmark over a period | AMFI Tracking Difference | Can be positive, zero, or negative depending on source. | Important for understanding actual benchmark underperformance/outperformance. |
| benchmark | TEXT | Nullable initially | Index or asset used as comparison benchmark | AMC factsheet or verified fund source | Can be blank initially. Should not be guessed. | Can also be stored in etfs table; this field can capture metric-period benchmark if needed. |
| expense_ratio_source_url | TEXT | Nullable initially | Source URL used for expense ratio | AMFI TER page | Required when expense_ratio is populated. | Used for traceability. |
| aum_source_url | TEXT | Nullable initially | Source URL used for AUM | AMFI Average AUM or AMC factsheet | Required when aum_crore is populated. | AUM source may be AMC-specific. |
| tracking_source_url | TEXT | Nullable initially | Source URL used for tracking error and tracking difference | AMFI Tracking Error / Tracking Difference page | Required when tracking_error or tracking_difference is populated. | Used for traceability. |
| created_at | DATETIME | Required later | Row creation timestamp | System generated | Auto-generated later when database is created. | Not needed in manual seed CSV now. |
| updated_at | DATETIME | Required later | Last update timestamp | System generated | Auto-generated later when database is created. | Useful when metrics refresh is added later. |

### Unique Key Decision

For MVP, each ETF should have one metrics row per metric_date.
Unique combination:

symbol + metric_date

### Important Design Decision

Do not force AUM to be required in MVP.
AUM should remain nullable until a reliable scheme-level source is confirmed.

Do not use third-party websites as the primary source for metrics.
Use AMFI and AMC factsheets first.

### Data Entry Rule for Later

When metric rows are added later:

- Do not add a row if symbol is not present in etfs table.
- Do not add fake expense ratio.
- Do not add fake AUM.
- Do not guess tracking error.
- Do not guess tracking difference.
- If a metric is unavailable, keep it blank.
- Always store the source URL when a value is populated.

### Sample Questions This Table Will Support Later

- Which Nifty 50 ETF has the lowest expense ratio?
- Which ETF has lower tracking error?
- Compare AUM of two ETFs.
- Explain why tracking difference matters.
- Which gold ETF looks more efficient based on cost and tracking?
