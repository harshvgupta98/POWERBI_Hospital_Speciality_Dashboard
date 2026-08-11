# Hospital Waiting List Analysis - Paediatric Patients

A Power BI dashboard analysing paediatric outpatient waiting list data in Ireland, covering monthly snapshots from January 2023 to June 2026.

---

## Short Description

A Power BI dashboard built to track how the paediatric hospital waiting list is changing over time. It looks at total waiting list size, how many patients are waiting over 12 months, and how different specialities compare, using publicly available NTPF data.

---

## Business Objective

The goal of this dashboard is to give a clear view of how the paediatric waiting list is trending: whether it's growing or shrinking, how many children are waiting longer than 12 months, and which specialities need the most attention. It also compares waiting list size against long-wait rate, since a speciality with a large list doesn't always have the highest proportion of long waits.

---

## Tools Used

- Power BI Desktop
- Power Query
- DAX
- Data Modelling

---

## Data Source

Monthly outpatient waiting list snapshots published by the NTPF (National Treatment Purchase Fund, Ireland).

| Table | Rows | Description |
|-------|------|-------------|
| Hospital_CHI_Child | 42 | Monthly waiting list snapshot for Children's Health Ireland, by waiting time band |
| Speciality_Child | 1,528 | Monthly waiting list snapshot by speciality (45 specialities) for paediatric patients |

Date Range: January 2023 - June 2026 | Patient Group: Paediatric | Source: [NTPF Open Data](https://www.ntpf.ie/waiting-list-data/open-data/)

---

## Data Model

Two monthly snapshot tables rather than a traditional star schema:

- `Hospital_CHI_Child` and `Speciality_Child`, both connected to a `DimDate` calendar table via `StartofMonth`
- `Hospital Waiting Band`, a small disconnected table (0-6, 6-12, 12-18, 18+ months) that drives the waiting band selector

---

## DAX Measures

| Measure | Expression | Description |
|---------|------------|-------------|
| Hospital Total Waiting | `SUM(Hospital_CHI_Child[Total])` | Total patients waiting |
| Hospital Waiting Over 12 Months | `[Hospital Waiting 12-18 Months] + [Hospital Waiting 18 Months Plus]` | Patients waiting more than 12 months |
| Hospital % Waiting Over 12 Months | `DIVIDE([Hospital Waiting Over 12 Months], [Hospital Total Waiting], 0)` | Long-wait rate |
| Hospital YoY % Change Waiting Over 12 Months | `DIVIDE([Hospital YoY Change Waiting Over 12 Months], [Hospital Previous Year Waiting Over 12 Months])` | Year-on-year change in long waits |
| Speciality Total Waiting | `SUM(Speciality_Child[Total])` | Total waiting, by speciality |
| Speciality % Waiting Over 12 Months | `DIVIDE([Speciality Waiting Over 12 Months], [Speciality Total Waiting], 0)` | Long-wait rate, by speciality |
| Speciality Highest Waiting Specialty | `TOPN(1, ADDCOLUMNS(ALLSELECTED(Speciality_Child[Speciality]), "@Waiting", [Speciality Total Waiting]), [@Waiting], DESC)` wrapped in `CONCATENATEX` | Speciality with the largest waiting list, updates with slicers |
| Speciality Highest Long-Wait Rate | Same TOPN/CONCATENATEX pattern, ranked on `[Speciality % Waiting Over 12 Months]` | Speciality with the highest long-wait rate |
| Speciality Largest Reduction in Long Waits | Same pattern, ranked on the drop between this year and last year's long waits | Speciality with the biggest year-on-year improvement |
| Last Refreshed Date | `"Last Refreshed Date: " & FORMAT(NOW(), "dd MMM yyyy hh:mm AM/PM")` | Dynamic timestamp shown in the header |

---

## What I Built

- Hospital Summary page with 6 KPI cards (total waiting, 0-6, 6-12, over 12 months, long-wait rate, YoY change)
- Total Waiting List Trend (area chart)
- Patients Waiting Over 12 Months Trend (count + %)
- Waiting Time Mix Over Time (100% stacked column chart)
- Current Waiting Time Mix (donut chart)
- Current vs Previous Year Waiting Bands (clustered column chart)
- Speciality Analysis page with Top 10 Specialities by Waiting List and by Long-Wait Rate
- Waiting List vs Long-Wait Rate scatter chart, by speciality
- Speciality Performance table with YoY columns
- Dynamic cards for highest waiting speciality, highest long-wait rate, and largest long-wait reduction
- Year, Quarter, Month, and Speciality slicers throughout

---

## Key Numbers (June 2026 snapshot)

| Metric | Hospital Summary (CHI) | Speciality Analysis |
|--------|------------------------|----------------------|
| Total Waiting List | 32,620 | 73,032 |
| Over 12 Months Waits | 5,560 | 11,135 |
| Long-Wait Rate | 17.0% | 15.2% |
| YoY Long-Wait Change | -25.7% | -22.1% |

---

## Key Findings

- 32,620 total waiting list entries as of June 2026
- 5,560 patients waiting over 12 months, a 17.0% long-wait rate
- 25.7% reduction in long waits compared with the same month last year
- Dermatology had the highest long-wait rate at 36.9%
- Paediatric ENT showed 953 fewer long waits year-on-year, the biggest improvement of any speciality
- Paediatrics has the largest waiting list overall but a comparatively low long-wait rate, a large waiting list doesn't always mean a high long-wait rate

---

## How to Use

1. Download `Hospital Waiting List Analysis.pbix`
2. Open it in Power BI Desktop (data is already embedded, no need to reconnect)
3. Use the Hospital Summary / Speciality Analysis buttons to switch pages
4. Use the Year, Quarter, and Month slicers to change the selected snapshot
5. On the Speciality Analysis page, use the Speciality slicer to isolate one speciality

---

## Dashboard Preview

**Hospital Summary**

![Hospital Summary](Snapshot_Hospital_Summary.png)

**Speciality Analysis**

![Speciality Analysis](Snapshot_Speciality_Analysis.png)
