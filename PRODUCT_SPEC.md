# Product Specification — 월간 시군구 주거신용위험 지수

## Product name

**월간 시군구 주거신용위험 지수**
(Monthly District-Level Housing Credit Risk Index)

A monthly report covering all 228 시군구 with 16 years of history (2010–present) across
5 registration types. Delivered to institutional subscribers.

---

## The five-type distress pipeline

The five registration types are not independent signals. They form a temporal pipeline — each
stage leads to the next, with measurable lags between them. The composite of all five, trended
monthly over 16 years at 시군구 granularity, is the product. No single signal is enough.

```
근저당                  Baseline credit activity — mortgage origination volume.
(mortgage)              Rising 근저당 without matching transaction volume = leveraging
                        up on existing property. This is the earliest signal.
    │
    │  lag: months
    ▼
가압류 → 압류            Debt enforcement pipeline — provisional lien (가압류) followed
(provisional seizure)    by execution (압류). Creditors freezing debtor assets before
                        filing suit. Precedes litigation and foreclosure.
    │
    │  lag: months
    ▼
강제경매 + 임의경매       Terminal distress — forced foreclosure (court-ordered) and
(foreclosure)           voluntary foreclosure (bank-initiated). The endpoint of the
                        enforcement pipeline. Irreversible.
    │
    │  parallel signal
    │
임차권 + 임차권등기명령   Landlord default channel — voluntary tenant lease protection
(tenant protection)      (임차권) vs. court-confirmed landlord default (임차권등기명령).
                        Spikes when landlords fail to return 전세 deposits. The
                        전세사기 signal.
    │
    │  parallel signal
    │
전세권 + 전세금 평균      Jeonse exposure index — jeonse right registrations ×
(jeonse rights)          average deposit size = total jeonse exposure in the district.
                        Declining volume with stable deposits = structural 전세→월세
                        shift. Declining volume with falling deposits = market
                        contraction.
```

### Why the pipeline view matters

A single metric (e.g. "강제경매 count") is a lagging indicator — by the time foreclosures
spike, the damage is done. The pipeline view gives **lead time**:

- 근저당 diverging from transactions → months before 가압류 rises
- 가압류 rising → months before 강제경매 rises
- 임차권 spiking → months before HUG 보증사고 claims hit

Each upstream signal provides months of advance warning over its downstream consequence.
The value of the index is that lead time, not the historical archive alone.

---

## What's in the report

### Core content (Version 0 — 등기 data alone)

1. **District-level ranking tables:** All 228 시군구 ranked by each of the 5 registration
   types — absolute change, growth rate, and composite distress score over trailing 12 months,
   3 years, and 5 years.

2. **Distress pipeline dashboard per district:** For each 시군구, a single view showing the
   temporal relationship between the five signals. Where is this district in the pipeline?
   Early stage (근저당 divergence only)? Mid-stage (가압류 rising)? Terminal (강제경매 spiking)?

3. **전세사기 crisis before/after (2022–2024):** Which districts deteriorated fastest, which
   recovered, which are still elevated. Month-by-month geographic progression.

4. **근저당 as leading indicator:** Cross-correlation analysis showing the lag between 근저당
   peaks and subsequent 강제경매 peaks, by district and by region.

5. **Methodology note:** Data sourced from 등기정보광장 공식 OpenAPI (서비스키 기반).
   Clean provenance — not scraping, not session hijacking, not leaked data.

### Enhanced content (Version 1 — with cross-validation datasets)

All of the above, plus:

6. **실거래가 overlay:** MOLIT transaction volume and price trends layered onto the distress
   pipeline. 근저당 rising faster than transactions = refinancing/leverage signal.

7. **인구이동 overlay:** 통계청 population migration at 시군구 × month granularity. Districts
   with net outflow + rising distress = structural decline. Districts with net inflow + rising
   근저당 = growth.

8. **KB 전세가격지수 overlay:** 전세권 volume vs. jeonse price index. Divergence distinguishes
   market contraction from structural 전세→월세 shift.

---

## Target audience

Financial institutions with geographic lending exposure in Korean housing markets:
- 저축은행 and 상호금융 institutions assessing 비수도권 collateral risk
- Housing guarantee and insurance entities managing district-level exposure
- Credit bureaus seeking a macro risk context layer for property-level models
- Housing policy researchers and urban economists

---

## Format

Monthly PDF report. See `METHODOLOGY.md` for data sourcing details.

---

## Status

Dataset complete: 223,275 rows covering 228 시군구, January 2010 – March 2026. Updated monthly.
