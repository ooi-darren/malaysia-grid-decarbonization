# Data Dictionary

This document defines every variable used in the Malaysia Grid Decarbonization analysis (2017–2024). Every dataset is classified as **PUBLIC** (published directly by an official source), **DERIVED** (calculated from public data), or **ESTIMATED** (third-party market research, not government-verified).

---

## grid_emission_factor.csv

**Source:** Compiled by author from two Suruhanjaya Tenaga (Energy Commission Malaysia) publications (see `references/`) — "Grid Emission Factor (GEF) in Malaysia" (published 25 Nov 2024, covers 2017–2022) and "Grid Emission Factor (GEF) in Malaysia, 2022–2024 (Provisional)" (published 23 Feb 2026)
**Classification:** DERIVED — figures are the exact published values, transcribed by hand from source PDF tables (the source was not available as a machine-readable download)
**Frequency:** Annual
**Coverage used:** 2017–2024
**Description:** The generation-weighted average GHG emission per unit of electricity generated (GHG intensity of grid electricity), for Peninsular Malaysia, Sabah, and Sarawak separately. Peninsular and Sabah are calculated by the Energy Commission from the National Energy Balance; Sarawak's figure comes from Sarawak Energy Berhad's own Annual and Sustainability Reports (a different calculating body — see limitations).

### Columns

| Column | Type | Description |
|---|---|---|
| `year` | int | Calendar year |
| `region` | string | `Peninsular`, `Sabah`, or `Sarawak` |
| `gef_gg_co2e_per_gwh` | float | Grid emission factor, gigagrams CO2e per gigawatt-hour |
| `source_publication` | string | Which of the two source PDFs the figure was taken from |
| `provisional` | bool | `TRUE` for 2022–2024 figures, which ST publishes as provisional pending final government endorsement |

### Known limitations
- **2022 was revised between publications.** The Nov 2024 document reports Peninsular 2022 GEF as 0.774; the Feb 2026 document restates it as 0.769, noting the figure "were recalculated to reflect the latest updated data." This file uses the **restated** 2022 value from the more recent publication throughout, for consistency with 2023–2024. The original 0.774 figure is not used anywhere in this analysis.
- **2022–2024 figures are provisional**, not final — ST states they are "subject to final endorsement by the Government of Malaysia," though not expected to change materially.
- **Sarawak is not directly comparable to Peninsular/Sabah.** Its GEF comes from a different source (Sarawak Energy Berhad's own reporting, not the Energy Commission's National Energy Balance calculation), so cross-region comparisons should be read as directional, not a precise apples-to-apples figure.
- **No 2025 actual data exists yet.** A separate ST "Grid Emission Factor Projections" publication exists but is explicitly a forecast, not measured data — not included in this file. See `netr_re_capacity_targets.csv` for how this analysis handles forward-looking context instead.

## fuel_mix_peninsular.csv

**Source:** Compiled by author from the same two Suruhanjaya Tenaga publications as `grid_emission_factor.csv` (see `references/`)
**Classification:** DERIVED — exact published values, hand-transcribed from source PDF tables
**Frequency:** Annual
**Coverage used:** 2017–2024
**Description:** The fuel-level breakdown (coal, natural gas, diesel, fuel oil) underlying Peninsular Malaysia's GEF calculation — fuel consumption, GHG emissions, and net electricity generation by fuel type, by year. Used to explain *why* the GEF moved, not just that it did. Peninsular only: the source documents do not publish this level of fuel breakdown for Sabah or Sarawak.

### Columns

| Column | Type | Description |
|---|---|---|
| `year` | int | Calendar year |
| `fuel_type` | string | `Coal`, `Natural Gas`, `Diesel`, or `Fuel Oil` |
| `fuel_consumption_tj` | float | Fuel consumed, terajoules |
| `ghg_emission_gg_co2e` | float | GHG emissions from that fuel, gigagrams CO2e |
| `net_electricity_generation_gwh` | float | Total Peninsular net electricity generation for the year (repeated per fuel row — this is the shared denominator for GEF, not a per-fuel generation figure) |

### Known limitations
- **Renewable energy is not broken out.** Every source table lists a "Total RE" row with a blank/dash value — the published GEF calculation methodology (Note 4 in the source PDFs) restricts fuel consumption and generation to "Main Activity Producer" thermal plants only. This dataset cannot show a rising RE generation share directly; `netr_re_capacity_targets.csv` is used as a separate, policy-level proxy for the renewable transition instead, with that metric mismatch stated explicitly in the analysis.
- Uses the same 2022-restatement handling as `grid_emission_factor.csv` (recalculated figures from the newer publication).

## netr_re_capacity_targets.csv

**Source:** National Energy Transition Roadmap (NETR), Government of Malaysia (2023)
**Classification:** DERIVED — the three published target figures, hand-transcribed (no machine-readable source found)
**Frequency:** N/A — policy targets, not a time series
**Description:** Malaysia's published renewable energy **installed capacity** share targets: 31% by 2025, 40% by 2035, 70% by 2050.

### Columns

| Column | Type | Description |
|---|---|---|
| `target_year` | int | Year the target applies to |
| `re_installed_capacity_share_pct` | float | Target renewable energy share of installed generation *capacity*, percent |
| `source` | string | Source document |

### Known limitations
- **This is a capacity target, not a generation or emission-factor target.** Installed capacity share, generation share, and grid emission factor (GEF) are three different metrics — a plant can exist (capacity) without running much (generation), and GEF depends on generation mix, not capacity mix. This file is used in the analysis as directional policy context for where Malaysia says it's heading, **not** as a formal benchmark GEF is measured against, and the notebook says so explicitly at first use.
- Malaysia's other headline climate commitment — a 45% GHG-intensity-to-GDP reduction by 2030 (NDC, submitted to UNFCCC July 2021) — is an economy-wide metric (total national emissions ÷ GDP), not grid-specific, and is not included in this dataset for that reason: comparing it directly to GEF would conflate two different denominators.

## Context / Secondary Sources (Qualitative)

Notebook 02's "Why Is This Happening?" section adds secondary-sourced explanation (energy-sector research and reporting) for why coal's share rose despite NETR targets — not new PUBLIC/DERIVED data, so kept separate from the tables above.

- TransitionZero, "From tenaga to tindakan: sparking Malaysia's coal-to-clean transition." https://www.transitionzero.org/insights/tz-cat-malaysias-coal-to-clean-transition
- Ember, "Solar and grid flexibility critical for Malaysia's future electricity affordability and security." https://ember-energy.org/latest-insights/solar-and-grid-flexibility-critical-for-malaysia/
