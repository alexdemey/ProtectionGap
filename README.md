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
