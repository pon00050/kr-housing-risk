# Methodology

## Data source

All data is collected from the **등기정보광장 공식 Open API** (data.iros.go.kr), operated by
대법원 전자등기소. Access is via officially registered service keys issued through the standard
Open API application process.

The API provides aggregate registration statistics — monthly counts of registration events
(임차권, 강제경매, 가압류, 전세권, 근저당) by administrative district. These are counts of
court registration filings, not individual deed records or personal information.

## Geographic granularity

Data is collected at the **시군구** (city/county/district) level — the second tier of Korean
administrative divisions. This covers all 228 시군구 across 17 시도 (including 세종특별자치시).

시군구 is the most granular level available from the aggregate statistics API. It is also the
level at which financial institutions assess geographic lending concentration risk.

## Temporal coverage

- **Start:** January 2010 (the earliest date for which 등기정보광장 provides aggregate
  statistics via the API)
- **End:** Current month (updated monthly)
- **Granularity:** Monthly

This provides 16+ years of continuous monthly data at the district level — sufficient for
trend analysis, seasonal decomposition, and cross-correlation with economic cycles.

## Registration types

Five registration types are collected, each measuring a different stage of the housing
credit lifecycle:

| Type | Korean | What it measures |
|---|---|---|
| Mortgage registration | 근저당설정등기 | New mortgage originations — baseline credit activity |
| Provisional seizure | 가압류등기 | Pre-litigation asset freezes — early enforcement signal |
| Foreclosure filing | 강제경매개시결정등기 | Court-ordered foreclosure proceedings — terminal distress |
| Tenant lease protection | 임차권등기 | Tenant filings for deposit protection — landlord default signal |
| Jeonse rights | 전세권설정등기 | Formal jeonse contract registrations — jeonse market exposure |

## Sub-district aggregation

Some 시도 return data at a finer granularity than 시군구 (e.g. 수원시 권선구, 수원시 팔달구).
These sub-district values are aggregated to the parent 시 level (e.g. 수원시) to maintain a
consistent 228-district schema across all regions and time periods.

## Zero-fill convention

When no registrations of a given type occurred in a district for a given month, the value is
recorded as zero. This is a genuine observation (no activity), not missing data. Zero-fill
ensures the time series is complete for every (district × type × month) cell.

## Data quality

- **Provenance:** Official government API with service key authentication
- **Consistency:** Same API endpoint and response format across the full 2010–present range
- **Idempotency:** Each data cell is collected exactly once (tracked via an internal state log);
  the current calendar month is re-fetched monthly to capture provisional data revisions
- **Known limitation:** Some historical district names in the API differ from current
  administrative names (due to district mergers and renames since 2010). These are logged and
  documented; affected cells are flagged in the dataset.

## What is NOT in this dataset

- Individual property records or deed contents
- Personal information (names, addresses, ID numbers)
- Property-level valuation or transaction prices
- Any data requiring paid 등기정보 열람 (document viewing)

All data is derived from **aggregate statistics** — counts of registration events, not
individual records.
