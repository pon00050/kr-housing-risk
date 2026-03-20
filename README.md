# 시군구별 주거신용위험 동향 분석

**District-Level Housing Credit Risk Time-Series for Korea (2010–Present)**

The first publicly available 시군구-level 등기통계 시계열 분석.

---

## What this is

A monthly district-level housing credit risk index covering all 228 시군구 across Korea,
built from 16 years of court registration data (2010–present) sourced from the official
등기정보광장 공식 OpenAPI.

Five registration types form a complete distress pipeline:

| Signal | What it measures | Role in pipeline |
|---|---|---|
| **근저당** | Mortgage origination volume | Baseline credit activity — earliest signal |
| **가압류** | Provisional asset seizure | Debt enforcement — creditors freezing assets before suit |
| **강제경매** | Foreclosure filings | Terminal distress — irreversible enforcement |
| **임차권** | Tenant lease rights protection | Landlord default — 전세사기 signal |
| **전세권** | Jeonse rights registrations | Jeonse market exposure by district |

No single signal is sufficient. The composite of all five — trended monthly over 16 years,
at 시군구 granularity — is the product.

---

## Why this matters

Everyone knows 전세사기 happened. No one has shown **where exactly** and **how it evolved
month by month by district**.

National statistics show a crisis. District-level data shows 228 different stories — some
districts peaked early and recovered, others are still deteriorating, and others were never
affected at all. That geographic specificity is what makes the data valuable for lending
decisions and what no existing product provides.

---

## Data provenance

All data is sourced from **등기정보광장 공식 OpenAPI** (대법원 전자등기소 운영,
data.iros.go.kr) using officially issued service keys. No scraping, no session hijacking,
no leaked data.

- **Source:** 등기정보광장 공식 Open API (서비스키 기반)
- **Coverage:** 228 시군구, 17 시도 (including 세종특별자치시)
- **Depth:** January 2010 – present (monthly granularity)
- **Types:** 임차권, 강제경매, 가압류, 전세권, 근저당

---

## Repository contents

| File | Description |
|---|---|
| `PRODUCT_SPEC.md` | Full product specification — what's in the index, who it's for |
| `METHODOLOGY.md` | Data sourcing and methodology (high-level) |
| `CROSS_VALIDATION.md` | Framework for validating against external public datasets |
| `reports/` | Published analysis outputs (when available) |
| `data/` | Sample data extracts (when available) |

---

## Status

**Data collection:** In progress. Full 228-district backfill completing.

**First report:** 시군구별 주거신용위험 동향 분석 (2010–2026) — forthcoming once 서울
coverage is complete.

---

## Contact

For institutional inquiries (저축은행, 상호금융, 보증기관, 연구기관):
- GitHub Issues on this repository
- Or reach out directly via the profile on this account

---

## License

Data analysis outputs in `reports/` are released under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/).
The underlying raw dataset and collection methodology are proprietary.
