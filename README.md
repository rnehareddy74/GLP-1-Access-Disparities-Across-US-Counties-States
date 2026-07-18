# GLP-1 Access Disparity Analysis

Who is NOT getting GLP-1 medications despite needing them? This analysis combines four public government datasets to find where obesity/diabetes burden is high but GLP-1 prescribing is low, then tests whether race, income, and insurance status explain that gap — at both the state and county level.

## Research Question
Do states and counties with more Black/Hispanic residents, lower incomes, and higher uninsured rates have worse access to GLP-1 medications, independent of clinical need?

## Data Sources
- CMS Medicare Part D by Geography 2024 — GLP-1 prescribing (semaglutide, tirzepatide, liraglutide, dulaglutide)
- CDC PLACES 2025 — county-level obesity and diabetes prevalence
- Census ACS 2024 — income and race/ethnicity
- CDC SVI 2022 — social vulnerability index, uninsured rate
- County-level only: NPPES + ZIP-to-county FIPS crosswalk, used to map prescriber ZIPs to counties

## Methodology
**Gap Score** = z-scored demand (0.5 × obesity + 0.5 × diabetes prevalence) − z-scored GLP-1 utilization per 1,000 people.
Positive = under-served (high need, low use). Negative = well-served.

Obesity and diabetes prevalence are excluded from the regression predictors since they're already baked into the gap score's construction (demand_z) — including them would be circular. Regression tests pct_black, pct_hispanic, median_income, svi_overall, and pct_uninsured as independent predictors, with VIF checked for multicollinearity.

## Key Findings — State Level (49 states)
- 3,954,762 total GLP-1 Medicare patients; $25.37B total drug spend
- Most under-served: Virginia, Oregon, South Dakota, Maryland, Nebraska
- Most well-served: West Virginia, Vermont, New York, Maine, New Hampshire
- Correlation with gap score: median income (r=−0.346, p=0.015) and % uninsured (r=0.445, p=0.001) both significant; % Black and % Hispanic not significant at state level
- Regression: R²=0.314 (31.4% of variance explained), overall p=0.005. % uninsured is the only clearly significant independent predictor (coef=0.107, p=0.014); income, SVI, and % Hispanic are marginal; % Black is not significant once controlling for the others

## Key Findings — County Level (2,781 counties)
- 2,402,684 total GLP-1 patients; 17,047,893 claims. 328 counties excluded from ranking/regression due to CMS beneficiary-count suppression (<11, privacy rule)
- Most under-served: Greene, Bullock, and Perry counties (Alabama — Black Belt), Humphreys County (Mississippi Delta), Todd County (South Dakota — Rosebud Reservation)
- At this sample size (n=2,781), all correlations are statistically significant, including % Black (r=0.191), % Asian (r=−0.282), income (r=−0.354), SVI (r=0.138), and % uninsured (r=0.165)
- Regression (n=2,453): R²=0.165, overall p<0.000001. % Black (coef=0.0173, p<0.0001), % Hispanic (coef=0.0079, p=0.0001), income, SVI, and % uninsured are all significant independent predictors

## Limitations
- Medicare Part D only — captures the 65+ population, not all ages
- State-level analysis masks within-state county variation
- SVI data is from 2022; other sources are 2024–2025
- County-level prescriber-to-patient mapping uses ZIP as a proxy for county; ZIPs spanning multiple counties default to the first-listed county when no weighting column is available — treat affected county counts as directional, not exact
- Correlation does not imply causation
- Territories and a few states (Kentucky, Pennsylvania) drop out at the state level due to inconsistent naming/coverage across source files

## How to Run
1. Place all six source CSVs in `/content/` (Medicare Part D by geography, PLACES county data, ACS demographics, SVI county data, CMS GLP-1 prescriber file, NPPES ZIP file, ZIP-to-FIPS crosswalk)
2. Run cells top to bottom — state-level analysis (Steps 1–10) is independent of county-level analysis (Steps 11–16)
3. Outputs: `chart1_gap_states.png` plus in-notebook scatter plots for obesity vs. utilization, race vs. gap, and income vs. gap
