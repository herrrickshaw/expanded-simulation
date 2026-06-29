# Election Data Analysis: Voters per Constituency

State-wise analysis of average voters per Lok Sabha constituency, population-growth
trends, and the structural mismatch between seat allocation (frozen since 1976 on a
1971-Census basis) and current population.

## Output

[`output/India_Election_Voters_per_Constituency_Analysis.xlsx`](./output/India_Election_Voters_per_Constituency_Analysis.xlsx) — 5 sheets:

| Sheet | Contents |
|---|---|
| Dashboard | KPI summary, top/bottom 10 states by electors/seat, key takeaways |
| README | Methodology, sourcing, known variance notes |
| State-wise LS Data | All states/UTs: 2024 electors, LS seats, 2011 Census population, Assembly seats |
| National Trend | 2014/2019/2024 electors, turnout, avg electors/seat with growth chart |
| Growth & Delimitation | Population-per-seat index vs. all-India average, representation-status flags |

## How it was built

Scripts in `scripts/` are numbered in run order:

1. `01_build_state_data.py` — compiles state-wise LS seats, 2024 electors (aggregated
   from constituency-level ECI data), 2011 Census population, Assembly seats → `data/state_data_raw.csv`
2. `02_build_national_trend.py` — national electors/turnout/seats for 2014/2019/2024 → `data/national_trend.csv`
3. `03_build_workbook_sheets1-2.py` — creates workbook, README + State-wise LS Data sheets
4. `04_build_national_trend_sheet.py` — adds National Trend sheet + line chart
5. `05_build_growth_delimitation_sheet.py` — adds Growth & Delimitation sheet, conditional formatting, bar chart
6. `06_build_dashboard.py` — adds Dashboard sheet as first tab, with summary charts

To re-run from scratch:
```bash
cd election-data-analysis
python3 scripts/01_build_state_data.py
python3 scripts/02_build_national_trend.py
python3 scripts/03_build_workbook_sheets1-2.py
python3 scripts/04_build_national_trend_sheet.py
python3 scripts/05_build_growth_delimitation_sheet.py
python3 scripts/06_build_dashboard.py
```
Each script reads/writes `election_analysis.xlsx` in the working directory and the CSVs
in `data/`. Adjust paths at the top of each script if running outside the original
sandbox layout.

## Key data notes

- 2024 elector figures are aggregated from constituency-level ECI statistical report
  data (via Wikipedia's compiled tables). State-level sums total ~97.36 crore vs. the
  EC's officially announced final electoral roll of 96.88 crore (~0.5% variance) —
  treat as accurate for comparative ranking, not as an official statutory figure.
- 2014/2019 national totals are rounded approximations pending full constituency-level
  reconciliation.
- Seat allocation has been frozen since the 42nd Amendment (1976) at 1971-Census
  proportions; current constituency boundaries reflect the 2001 Census via the 2002
  Delimitation Commission.
- The Delimitation Bill, 2026 (which would have shifted the basis to the 2011 Census and
  expanded the Lok Sabha to ~850 seats) was **defeated** in the Lok Sabha on 17 April 2026
  (298 votes for, 352 needed). The freeze remains in effect as of this writing.

## Sources

- Election Commission of India — 2024 Lok Sabha statistical reports
- Census of India 2011
- Wikipedia: "List of constituencies of the Lok Sabha", "Lok Sabha", "Delimitation
  Commission of India"
- PRS Legislative Research — The Delimitation Bill, 2026 and Constitution (131st
  Amendment) Bill, 2026 bill-track pages
