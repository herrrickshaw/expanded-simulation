# Lok Sabha Coalition Game Theory Simulator

A structural simulation of majority formation in India's Lok Sabha using cooperative
game theory — Minimal Winning Coalitions and the Banzhaf Power Index — applied only to
political parties that exist today (no hypothetical new parties).

**This is not a forecast.** It does not predict vote shares or election outcomes. It
answers: *given a seat distribution (real or hypothetical), which coalitions can reach
272/543, and how much bargaining leverage does each party hold?*

## Output

[`output/Lok_Sabha_Coalition_Game_Theory_Simulator.xlsx`](./output/Lok_Sabha_Coalition_Game_Theory_Simulator.xlsx) — 6 sheets:

| Sheet | Contents |
|---|---|
| Dashboard | Scenario summary table, Monte Carlo headline %, key findings |
| Monte Carlo Simulation | 20,000-draw randomized permutation results, histogram, conditional Banzhaf |
| README | Full methodology, game theory definitions, data sourcing, caveats |
| Scenarios | 6 hand-picked seat distributions (editable blue cells) |
| Minimal Winning Coalitions | All MWCs per scenario |
| Banzhaf Power Index | Pivotality index per party per scenario, comparison chart |

## Game theory concepts used

- **Majority quota**: 272 of 543 seats.
- **Minimal Winning Coalition (MWC)**: a set of parties summing to ≥272 seats where
  removing any single member drops it below quota — i.e. every member is necessary.
- **Banzhaf Power Index**: for each party, the share of all possible coalitions in which
  that party is *critical* (its addition flips a losing coalition into a winning one).
  Captures real bargaining leverage in a way raw seat counts can't — a small party can
  have substantial power if it's reliably the pivotal vote; a large party can have *zero*
  power if a rival bloc already has a majority alone.

## How it was built

Scripts in `scripts/` are numbered in run order:

1. `01_party_seat_data.py` — current Lok Sabha party-wise seat data (post the June 2026
   TMC→NCPI merger and Shiv Sena(UBT)→Shiv Sena migration) → `data/party_seats.csv`
2. `02_bloc_level_game_theory.py` — exact Banzhaf + MWC analysis treating NDA/INDIA as
   cohesive blocs against unaligned parties (today's actual configuration)
3. `03_scripted_scenarios.py` — defines and analyzes 6 hand-picked seat scenarios →
   `scenario_results.pkl` (regenerated, not committed)
4. `04_monte_carlo_simulation.py` — 20,000-draw randomized simulation → `data/monte_carlo_draws.csv`
   + `monte_carlo_summary.pkl` (regenerated, not committed)
5–10. `build_*.py` scripts — assemble each workbook sheet (README, Scenarios, MWC,
   Banzhaf, Dashboard, Monte Carlo) using openpyxl
11. `11_reorder_sheets.py` — final sheet ordering + README methodology appendix
12. `12_insert_mc_into_dashboard.py` — inserts Monte Carlo headline KPIs into Dashboard

To re-run from scratch:
```bash
cd coalition-game-theory
python3 scripts/01_party_seat_data.py
python3 scripts/02_bloc_level_game_theory.py
python3 scripts/03_scripted_scenarios.py
python3 scripts/04_monte_carlo_simulation.py
python3 scripts/05_build_workbook_readme.py
python3 scripts/06_build_scenarios_sheet.py
python3 scripts/07_build_mwc_sheet.py
python3 scripts/08_build_banzhaf_sheet.py
python3 scripts/09_build_dashboard.py
python3 scripts/10_build_montecarlo_sheet.py
python3 scripts/11_reorder_sheets.py
python3 scripts/12_insert_mc_into_dashboard.py
```
Scripts 03 and 04 produce `.pkl` files that scripts 06–10 read; run them in order in the
same working directory. Adjust hardcoded paths if your layout differs from the original.

## Monte Carlo methodology (the randomizer)

Each of 20,000 draws assigns every party/bloc a seat count sampled uniformly from a
historically grounded range (e.g. NDA 220–360, reflecting its actual 293–353 span across
2014/2019/2024 plus the 2026 realignment; YSRCP 0–25, reflecting its real swing from 22
seats in 2019 to 0 in 2024), then rescales all values to sum to exactly 543. The same
exact MWC/Banzhaf game theory is recomputed for every draw.

**Caveat on interpretation**: ranges are sampled *uniformly*, not poll-weighted, so the
resulting percentages describe how much of the *plausible-outcome space* is hung — not a
calibrated probability of a hung parliament actually occurring in 2029. Random seed is
fixed (`42`) for reproducibility.

## Key data note: the June 2026 TMC/NCPI realignment

20 Trinamool Congress (TMC/AITC) MPs merged into the pre-existing, ECI-registered (2023)
Nationalist Citizens Party of India and aligned with NDA; 6 Shiv Sena (UBT) MPs migrated
to Shiv Sena (also NDA). This pushed NDA's actual current strength to **318 seats** (from
293 at the original 2024 declaration) — comfortably past the 272 majority threshold.
Because NCPI predates this analysis, it satisfies a "no new parties" constraint even
though it's a relatively obscure vehicle for the defection.

## Sources

- Wikipedia: "List of members of the 18th Lok Sabha" (party tally as of 28 March 2026)
- Wikipedia: "National Democratic Alliance" (June 2026 merger/migration update)
- Various June 2026 news reporting on the TMC parliamentary split (Business Standard,
  Outlook India, The Wire)
