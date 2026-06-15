# Architecture

## 1. Project Name

ETFWise India - AI ETF Comparison Assistant

## 2. Project Goal

An educational AI assistant that compares Indian ETFs using official/public data sources such as NSE, AMFI, and AMC factsheets.

## 3. Important Disclaimer

This app is for ETF education and comparison only. It is not investment advice. It will not provide buy/sell recommendations or personalized financial advice.

## 4. MVP ETF Categories

- NIFTY_50
- SENSEX
- BANK
- GOLD
- SILVER

## 5. High-Level Architecture

User -> Streamlit UI -> FastAPI Backend -> Business Logic Layer -> SQLite/Postgres Database + Vector Store -> Groq LLM

## 6. Data Architecture

Static knowledge will go into vector store/RAG.
Dynamic ETF numbers such as NAV, market close price, volume, expense ratio, tracking error, and AUM will go into database tables.

## 7. Core Tables Planned

- etfs
- etf_daily_prices
- etf_metrics

## 8. Phase Plan

Phase 1: Data design and source planning
Phase 2: Data ingestion scripts
Phase 3: Database setup
Phase 4: RAG knowledge base
Phase 5: Business logic
Phase 6: Backend API
Phase 7: Frontend UI
Phase 8: Scheduler
Phase 9: Deployment
Phase 10: Android / Google Play Store exploration

## 9. Out of Scope for MVP

- Real-time trading
- WebSockets
- Buy/sell recommendations
- Payment or investment execution
- Personalized financial advice
- Scraping private or restricted data
- Copying third-party UI, logos, or copyrighted descriptions

## 10. Phase 1 Status

Current phase: Phase 1 only.
Backend, frontend, scraper, scheduler, RAG, Groq, deployment, and Android app are not started yet.
