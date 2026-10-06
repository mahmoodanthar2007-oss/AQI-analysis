# Air Quality Degradation along the Manali–Ennore Industrial Corridor, Chennai

Statistical and spatio-temporal analysis of air quality at four CPCB/TNPCB monitoring stations
(Manali, Manali Village, Kodungaiyur, Royapuram) over **Jan 2024 – Dec 2025** at 15-minute resolution
(~274k rows).

The project is deliberately **statistics-led, not ML-first**: the goal is to understand *where*, *when*
and *why* pollution differs along the corridor, with defensible tests and honest handling of bad data.

## What's inside

| Step | Method |
|---|---|
| Data loading | Merge 4 station CSVs, normalise unit-symbol encodings (µg/m³ vs ug/m3) |
| Completeness audit | Per-station, per-pollutant % of non-missing readings |
| Cleaning | **Tiered missing-value handling**: linear interpolation only for gaps ≤ 1 h; long outages stay `NaN` |
| EDA | Station summaries, daily PM2.5 series, diurnal and seasonal profiles, wind-direction effect, correlation heatmaps |
| Trend testing | **Seasonal Mann-Kendall** (period = 12, monthly means) |
| Group differences | One-way ANOVA + **Tukey HSD** across stations and seasons |
| Meteorology | Monthly-aggregated PM2.5 vs RH / wind speed; mean PM2.5 by wind-direction sector |

## Results preview

![EDA dashboard](images/eda_dashboard.png)
![Correlation heatmaps](images/corr_heatmap.png)

## Key findings

1. **Season matters more than location for PM2.5.** The Winter–Summer gap (≈16 µg/m³) is larger than the biggest station-to-station gap (≈5.8 µg/m³).
2. **Different stations, different signatures.** SO₂ and CO (combustion markers) are highest at Manali / Manali Village and fall toward Royapuram; NO₂ shows the opposite gradient, peaking at Royapuram.
3. **Diurnal shapes support this.** Royapuram and Kodungaiyur show rush-hour double peaks; Manali stations show flatter, all-day patterns.
4. **Trends are mixed, not uniformly improving or worsening.** Kodungaiyur and Royapuram show rising PM2.5/PM10; Manali shows rising SO₂ but falling CO.
5. **Humidity correlates positively with PM2.5 at every station** (monthly scale). At Manali the highest PM2.5 occurs with winds from the S/SW/W sectors, which is consistent with, but does not prove, an upwind industrial source.

## Data-quality decisions (important)

- **Gandhi Nagar Ennore** station dropped: ~10% completeness over the full window.
- **Manali Village** had a ~7.5-month outage (2024-01-01 to 2024-08-13). It was *not* imputed, so it lacks the 24 months needed for several trend tests.
- **Manali Village ozone** excluded: night-time mean exceeds daytime and values reach 500 µg/m³, which indicates a sensor/calibration artifact.

## Limitations

- Only **24 monthly points per series** (2 per calendar month), so the seasonal Mann-Kendall test has little power and p-values bottom out near 0.009. Treat the trend results as indicative, not conclusive.
- ANOVA / Tukey treat 15-minute readings as independent, but they are strongly autocorrelated; p-values are therefore overstated. Effect sizes (mean differences) are more informative than the p-values.
- Two years of data cannot separate a real trend from year-to-year weather variability.
- Wind-sector results are associations; no emission inventory or source apportionment was used.

## Run it

```bash
git clone https://github.com/<your-username>/manali-ennore-air-quality.git
cd manali-ennore-air-quality
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
# download the 4 CSVs into data/ (see data/README.md)
jupyter notebook notebooks/Manali_Ennore_Corridor_Analysis.ipynb
```

The notebook paths are `data/...`, so launch Jupyter from the **repo root** (as above) or adjust the `FILES` paths.

## Repo layout

```
.
├── README.md
├── LICENSE
├── requirements.txt
├── .gitignore
├── data/                # put raw CSVs here (not committed); see data/README.md
├── images/              # figures used in this README
└── notebooks/
    └── Manali_Ennore_Corridor_Analysis.ipynb
```

## Data source & credit

CPCB / TNPCB monitoring data, accessed via [data.opencity.in](https://data.opencity.in/dataset/chennai-hourly-air-quality-reports).
