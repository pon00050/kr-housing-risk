# Cross-Validation Framework — Complementary External Datasets

## Why this matters

The 등기정보광장 dataset captures **legal events** — filings, registrations, court actions.
These are leading indicators. External datasets capture **economic outcomes** — prices, lending
balances, population movement, auction results, insurance claims. Outcomes follow legal events
by months.

Demonstrating that lag structure — consistently, across multiple independent datasets, at the
시군구 level — transforms the product from a historical archive into a predictive tool. It is
also the strongest possible validation story for institutional buyers: the data is not just
internally consistent, it is corroborated by every adjacent public dataset that measures the
downstream consequences.

---

## What 한국부동산원 provides (and does not)

한국부동산원 (R-ONE) publishes property **market** statistics: transaction prices (실거래가),
price indices, supply/demand indicators, rent levels, unsold inventory. Their 시군구-level data
(178 districts per R-ONE, vs. 228 in the 등기 dataset) covers market activity — what properties
sold for and how many traded.

등기정보광장 (IROS) — this project's data — is court **registration** statistics: how many
근저당 were filed, how many 강제경매 were initiated, how many 가압류 were registered. These
are legal events in the 대법원 court registry system, not market transactions.

These are fundamentally different data types. 한국부동산원 does not provide 등기 registration
counts at any frequency. The cross-validation opportunity is not direct overlap but signal
coherence: do independent datasets measuring different aspects of the same underlying reality
tell a consistent story?

---

## Validation pairs by registration type

### 1. 임차권 (tenant lease rights) ↔ HUG 전세보증보험 사고

| Dimension | 등기 data (this project) | HUG data |
|---|---|---|
| What it measures | Tenant files for lease rights protection at court | Guarantee insurer pays out on landlord default |
| Granularity | Monthly, 시군구 | Annual, regional |
| Source | 등기정보광장 공식 OpenAPI | HUG annual reports (khug.or.kr) |

**Expected relationship:** 임차권 registrations spike first (the tenant acts when they sense
risk). HUG 보증사고 follows with a lag (the guarantee claim triggers later when the landlord
actually fails to return the deposit). The lag duration itself is analytically valuable — it
measures how long the system takes to process a distress event from first legal action to
insurance payout.

**Validation test:** Do districts with the highest 임차권 growth in year N show elevated HUG
보증사고 in year N or N+1?

---

### 2. 강제경매 (foreclosure filings) ↔ 법원경매 auction data

| Dimension | 등기 data (this project) | Court auction data |
|---|---|---|
| What it measures | Foreclosure is **filed** at court | Foreclosure **proceeds to auction sale** |
| Granularity | Monthly, 시군구 | Per-case, with dates and locations |
| Source | 등기정보광장 공식 OpenAPI | courtauction.go.kr via Hyphen 경매다 API |

**Expected relationship:** Filing precedes auction by months (legal process duration). The
conversion rate — what fraction of filed foreclosures actually result in a completed auction —
varies by district and economic conditions. In strong markets, more cases settle before auction.
In weak markets, more proceed to sale.

**Validation test:** Does district-level 강제경매 filing volume predict auction volume with a
measurable lag? Does the conversion rate differ systematically between 수도권 and 비수도권?

**Note:** Hyphen 경매다 is confirmed (PRD.md) to cover 법원경매 from courtauction.go.kr. Free
demo: 100 calls/day × 3 months.

---

### 3. 근저당 (mortgage registrations) ↔ Multiple sources

근저당 is the richest validation target because mortgage activity intersects with lending,
transactions, and prices simultaneously.

#### 3a. 근저당 ↔ 한국은행 ECOS regional lending

| Dimension | 등기 data (this project) | BOK ECOS |
|---|---|---|
| What it measures | Individual mortgage registrations filed | Aggregate household lending balances |
| Granularity | Monthly, 시군구 | Quarterly, 시도 |
| Source | 등기정보광장 공식 OpenAPI | ecos.bok.or.kr (public API: data.go.kr 15000054) |

**Expected relationship:** 근저당 registrations are individual lending events. BOK regional
lending balances are the aggregate stock. Rising 근저당 in a region should correlate with
rising household debt balances in the same region with a short lag (registration → disbursement
→ balance sheet reporting).

**Validation test:** Does regional 근저당 trend direction match BOK 가계대출 잔액 trend at
the 시도 level?

#### 3b. 근저당 ↔ 국토교통부 실거래가 transaction volume

| Dimension | 등기 data (this project) | MOLIT real transaction data |
|---|---|---|
| What it measures | Mortgage registrations filed | Actual property purchase transactions |
| Granularity | Monthly, 시군구 | Monthly, 시군구 |
| Source | 등기정보광장 공식 OpenAPI | rt.molit.go.kr (public API) |

**Expected relationship:** A mortgage registration (근저당) almost always accompanies a property
purchase. Transaction volume and 근저당 counts should correlate strongly at the 시군구 level.

**Divergence signal:** When 근저당 rises faster than transaction volume, it indicates refinancing
or second-mortgage activity — borrowers extracting equity from existing properties. This
divergence is itself a distress leading indicator: leveraging up without new purchases suggests
financial stress or speculative behavior. If the divergence appears district-by-district before
later 강제경매 increases, that is a publishable finding.

**Validation test:** What is the 근저당-to-transaction ratio by district over time? Do districts
where this ratio spikes later show elevated 강제경매?

---

### 4. 가압류 (provisional seizure) ↔ 대법원 민사소송 통계

| Dimension | 등기 data (this project) | Court litigation statistics |
|---|---|---|
| What it measures | Pre-litigation asset seizure filed | Civil lawsuit filed and adjudicated |
| Granularity | Monthly, 시군구 | Annual, 시도/전국 |
| Source | 등기정보광장 공식 OpenAPI | court.go.kr 사법연감 (annual publication) |

**Expected relationship:** 가압류 is a pre-litigation provisional measure — the creditor freezes
the debtor's property before filing suit. Civil litigation follows. If district-level 가압류
predicts later regional civil litigation volume, that validates its leading-indicator status.

**Limitation:** Court litigation statistics are annual and regional (시도, not 시군구). The
match is coarser than for other pairs. Directionally useful for validating the signal, not
for granular cross-referencing.

**Validation test:** Do 시도-aggregated 가압류 counts in year N predict 시도-level 민사소송
접수건수 in year N+1?

---

### 5. 전세권 (jeonse rights) ↔ KB부동산 전세가격지수

| Dimension | 등기 data (this project) | KB real estate data |
|---|---|---|
| What it measures | Jeonse rights formally registered at court | Jeonse price index by district |
| Granularity | Monthly, 시군구 | Monthly, 시군구 |
| Source | 등기정보광장 공식 OpenAPI | KB부동산 데이터허브 |

**Expected relationship:** 전세권 registration is the legal act of formalizing a jeonse
contract at the court registry. The KB 전세가격지수 measures the market price level of jeonse
contracts.

**Decline scenario:** If 전세권 registrations decline in a district while the jeonse price
index also falls, that validates a genuine market contraction — both fewer contracts and lower
values.

**Structural shift scenario:** If 전세권 registrations decline but jeonse prices hold or rise,
it suggests 전세→월세 structural conversion — tenants shifting to monthly rent, not market
distress. Distinguishing these two scenarios at the district level is analytically valuable
and impossible without both datasets.

**Validation test:** What is the correlation between 전세권 registration volume change and
전세가격지수 change at the 시군구 level? Where do they diverge?

---

## Cross-cutting validations (all five types)

### All types ↔ 통계청 인구이동 통계

| Dimension | 등기 data (this project) | KOSIS population data |
|---|---|---|
| What it measures | Court registration activity by type | Net population in/out migration |
| Granularity | Monthly, 시군구 | Monthly, 시군구 |
| Source | 등기정보광장 공식 OpenAPI | kosis.kr (public API) |

**Expected relationship:** Districts experiencing net population outflow often show rising
distress signals. People leave districts that are deteriorating. If the 등기 distress composite
(강제경매 + 가압류 + 임차권 rising) correlates with net population outflow in the same
district, that is a coherent and compelling story.

**Reverse signal:** Districts with net inflow and rising 근저당 are growth districts (people
moving in, taking on mortgages). Districts with net outflow and rising 강제경매 are decline
districts. The combination of 등기 + population data creates a 2×2 district classification
that no single dataset can provide.

---

### All types ↔ 한국부동산원 미분양 (unsold housing inventory)

| Dimension | 등기 data (this project) | R-ONE unsold inventory |
|---|---|---|
| What it measures | Court registration activity | Unsold new housing units |
| Granularity | Monthly, 시군구 | Monthly 시도 / quarterly 시군구 |
| Source | 등기정보광장 공식 OpenAPI | R-ONE (data.go.kr 15134761) |

**Expected relationship:** Rising 미분양 in a district concurrent with rising 강제경매 and
가압류 tells a coherent oversupply + distress story — too many units built, not enough demand,
developers and borrowers defaulting. Rising 미분양 with stable 등기 signals suggests a
supply-side problem (overbuilding) without borrower distress (yet).

**Leading indicator test:** Does 미분양 increase predict later 강제경매 increase in the same
district? If so, the combination of oversupply data + 등기 distress data creates a two-stage
early warning system.

---

## Summary: the validation architecture

```
Legal events (등기 — this project)          Economic outcomes (external datasets)
────────────────────────────────────        ────────────────────────────────────
임차권 registrations spike          ──lag──→  HUG 보증사고 claims rise
근저당 registrations surge          ──lag──→  BOK household debt rises
근저당 outpaces transactions        ──lag──→  강제경매 filings rise later
강제경매 filed                      ──lag──→  Court auctions proceed
가압류 filed                        ──lag──→  Civil litigation rises
전세권 volume declines              ──with──→ KB 전세가격지수 falls (or diverges)
All distress signals rise           ──with──→ Population outflow accelerates
강제경매 + 가압류 + 미분양 all rise  ──────→  District in structural decline
```

The consistent pattern: **legal events lead, economic outcomes follow.** Each validation pair
independently demonstrates this. Taken together, they make the case that the 등기 dataset is
not just historically interesting but predictively useful — and that is what institutional
buyers pay for.

---

## Data access summary

| External dataset | Access method | Cost | Granularity match |
|---|---|---|---|
| HUG 전세보증사고 | Annual reports (khug.or.kr) | Free | Annual / regional (coarser) |
| Hyphen 경매다 (법원경매) | API (courtauction.go.kr) | Free demo: 100/day × 3mo | Per-case (finer) |
| 한국은행 ECOS 가계대출 | Public API (data.go.kr 15000054) | Free | Quarterly / 시도 (coarser) |
| 국토교통부 실거래가 | Public API (rt.molit.go.kr) | Free | Monthly / 시군구 (exact match) |
| 대법원 사법연감 | Annual PDF (court.go.kr) | Free | Annual / 시도 (coarser) |
| KB부동산 전세가격지수 | KB부동산 데이터허브 | Free/registered | Monthly / 시군구 (exact match) |
| 통계청 인구이동 | KOSIS API (kosis.kr) | Free | Monthly / 시군구 (exact match) |
| R-ONE 미분양 | Public API (data.go.kr 15134761) | Free | Monthly 시도 / quarterly 시군구 |

Three datasets match at the exact same granularity (monthly × 시군구): MOLIT 실거래가, KB
전세가격지수, and 통계청 인구이동. These are the highest-value validation targets — they
permit district-level, month-level correlation analysis with no aggregation loss.
