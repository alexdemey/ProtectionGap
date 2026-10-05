# ProtectionGap
# Who Bears Disaster Risk? Institutions, Income and the Insurance Protection Gap

Independent research project by Alex Demey, 2026.

## Question
Why is so much more disaster damage uninsured in poorer countries? This project tests
three explanations: income, financial development (credit-to-GDP) and institutional
quality (rule of law).

## Main finding
Across 574 natural disasters in 67 countries (2000–2024), the uninsured share of losses
ranges from 46.7% in high-income countries to 96.1% in low-income countries. Rule of
law is the strongest predictor of the gap; once it is included, income's association
falls by about two-thirds and is no longer significant. These are correlations, not
causal estimates.

## Files
- `writeup.pdf`: two-page summary of findings, confidence and limitations
- `protection_gap.ipynb`: full analysis (Python, statsmodels), with outputs
- `decisions.rtf`: log of data and modelling decisions

## Data
- Disaster data: EM-DAT, CRED / UCLouvain. Not included here under EM-DAT's terms of
  use; access can be requested at emdat.be.
- Country indicators: World Bank (GDP per capita, domestic credit to private sector)
  and Worldwide Governance Indicators (rule of law, government effectiveness).

## Methods
OLS and fractional logit with year and hazard-type fixed effects and standard errors
clustered by country. Robustness checks include three treatments of missing insured-loss
data, an alternative governance measure, VIF collinearity checks and sample splits.

###
Recreating the datasets

The cleaned CSVs are not included because EM-DAT's terms of use restrict sharing its
data. They can be rebuilt in about two hours with the steps below. The notebook
handles all remaining cleaning, so once the three CSVs exist, uploading them to Colab
and choosing Runtime → Run all reproduces every result.

### 1. Disaster data (EM-DAT)
1. Register for free non-commercial access at emdat.be and download the full public
   table as an Excel file.
2. Keep the raw download untouched and work on a copy.
3. Filter to **natural hazards only** (Disaster Group = Natural) and **Start Year 2000
   onwards**. Insured-damage reporting before 2000 is too sparse to use.
4. Keep these columns: DisNo., ISO, Country, Disaster Type, Start Year,
   Total Damage Adjusted and Insured Damage Adjusted. Damage figures are in thousands
   of US dollars. The inflation-adjusted pair is used for both, so the same correction
   applies to both sides of the ratio.

### 2. Country data (World Bank)
1. From World Development Indicators (databank.worldbank.org), download for all
   countries, 2000–2024, with years running down the page (one row per country-year):
   - GDP per capita, PPP (constant international $)
   - Domestic credit to private sector (% of GDP)
2. From the Worldwide Governance Indicators, download **Rule of Law** and
   **Government Effectiveness** (estimate scores) for the same countries and years.
   **[check: WGI vintage/release year, from decisions.txt]**
3. World Bank exports mark missing values as `..`, which should be treated as blank.

### 3. Joining
Join the country data onto each disaster by **ISO3 country code and year**, not by
country name, since names differ between sources (e.g. "Korea, Rep." vs
"South Korea") and mismatches silently drop rows.
**[check: same year as the disaster, or lagged?]**

### 4. The outcome variable
For each disaster:

    uncovered share = 1 − (Insured Damage Adjusted ÷ Total Damage Adjusted)

0 means fully insured; 1 means nothing was insured.

### 5. The three versions of the dataset
EM-DAT never records an insured loss of zero, so a blank insured-damage field could
mean either no insurance or no report. The project tests both readings:

| File | Treatment of blank insured damage | Rows |
|---|---|---|
| `drop_blanks.csv` | Events with blank insured damage removed (main sample) | 574 |
| `blanks_zero.csv` | Blank insured damage treated as zero, so uncovered share = 1 | 2,709 |
| `both_present.csv` | Only events with both damage figures reported; identical to `drop_blanks` by construction | 574 |

In all three, events with blank total damage are removed, since the ratio can't be
calculated without it. **[check]** Each file needs the World Bank columns joined on.

Export each as CSV. If you export from Mac Excel, the notebook's loader already strips
the comma thousand-separators and stray spaces this introduces.

### 6. In the notebook
The loader then, for all three files identically:
- computes `log_gdp` and `log_damage` from the raw values
- drops rare hazard types with too few events to estimate **[check: which ones / threshold]**
- removes rows missing any model variable

The final main sample is 574 disasters across 67 countries, 2000–2024.
