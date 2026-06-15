# Data Sources - ETFWise India

## Purpose

This document lists the official/public data sources planned for ETFWise India. These sources will be used later for ETF master data, NAV, market price, volume, expense ratio, tracking error, tracking difference, and AUM.

## Source Priority Rule

Primary sources should be official or public market/institution sources:

1. NSE
2. AMFI
3. AMC factsheets

Third-party websites like Groww, INDmoney, Tickertape, Moneycontrol, Dhan, or Zerodha should not be used as primary data sources in MVP. They may be used only for manual cross-checking later, not as the main source.

## Source List

| Source ID | Source Name | Official Link | Data Needed | Planned Usage | Manual or Automated Later | Risk / Notes | MVP Priority |
| --- | --- | --- | --- | --- | --- | --- | --- |
| NSE_ETF_MASTER | NSE Securities Available for Trading - ETF CSV | https://www.nseindia.com/static/market-data/securities-available-for-trading | ETF trading symbol, ISIN, listed ETF master list | Build etfs master table | Automated later if download is stable; manual fallback possible | NSE page structure/download behavior may change; script must be tested carefully | High |
| NSE_ETF_MARKET_WATCH | NSE ETF Market Watch | https://www.nseindia.com/market-data/exchange-traded-funds-etf | ETF market close price, traded volume, category, CSV download if available | Build etf_daily_prices table for market data | Automated later if CSV/API access is stable; manual fallback possible | This may be dynamic and may require headers/session handling in scripts later | High |
| NSE_HISTORICAL_REPORTS | NSE All Reports / Historical Reports | https://www.nseindia.com/all-reports | Historical price and volume reports if ETF market watch is insufficient | Backup source for historical market price and volume | Automated later if report download URL is stable | Correct report name and date format must be verified later | Medium |
| AMFI_NAV_DOWNLOAD | AMFI NAV Download | https://www.amfiindia.com/net-asset-value/nav-download | Latest NAV and historical NAV | Build etf_daily_prices table for NAV data | Automated later; manual download fallback possible | Historical NAV has date-period limits; scheme names may differ from NSE names | High |
| AMFI_TER | AMFI TER of MF Schemes | https://www.amfiindia.com/ter-of-mf-schemes | Total Expense Ratio / expense ratio | Build etf_metrics table | Automated later if accessible; manual fallback possible | Need to verify whether ETF-specific TER is cleanly available by scheme | High |
| AMFI_TRACKING | AMFI Tracking Error and Tracking Difference | https://www.amfiindia.com/otherdata/tracking-error | Tracking error and tracking difference | Build etf_metrics table | Automated later if accessible; manual fallback possible | Need to verify date and scheme filters later | High |
| AMFI_AVERAGE_AUM | AMFI Average AUM | https://www.amfiindia.com/aum-data/average-aum | Average AUM | Possible AUM source for etf_metrics table | Automated later if scheme-level ETF AUM can be obtained; otherwise use AMC factsheet | Mark AUM as optional until scheme-level reliability is confirmed | Medium |
| AMC_FACTSHEETS | AMC Monthly Factsheets | To be identified per AMC later | benchmark, AUM, expense ratio, tracking error, tracking difference, fund details | Backup or validation source for etfs and etf_metrics | Manual first; automated PDF extraction later only if stable | Each AMC uses different PDF format; extraction may break when format changes | Medium |

## Automation Decision for MVP

For the MVP, we will first design the database and seed file without scraping.
Later, data ingestion can be automated source by source.
If any official website changes, the ingestion script can be updated, tested, committed, pushed, and redeployed.
If only ETF values change but the source format remains stable, the scheduler can update data without redeploy.

## Step 2 Status

Step 2 is complete when:

- All official source links are documented.
- Each source has a clear data purpose.
- Each source has a risk note.
- No data has been scraped yet.
- No fake ETF rows have been added.
