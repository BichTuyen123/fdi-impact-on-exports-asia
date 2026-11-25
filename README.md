# Impacts of FDI on Exports: The Case of Asian Countries

A panel data study of how foreign direct investment (FDI) affects exports in
47 Asian countries over 1993-2022, estimated in Stata with Pooled OLS, fixed
and random effects, and two-step System GMM.

## Research Question

Does FDI promote exports in Asian countries, and does the effect differ
between upper-middle-income (UMI, 25 countries) and lower-middle-income
(LMI, 22 countries) economies?

## Data

| Item | Detail |
|---|---|
| Coverage | 47 Asian countries, 1993-2022 (unbalanced panel, up to 1,410 observations) |
| Sources | World Development Indicators (WDI), Human Development Reports (HDR) |

| Role | Variable | Definition |
|---|---|---|
| Dependent | EXP | Exports of goods and services (current US$) |
| Main independent | FDI | FDI net inflows (BoP, current US$) |
| Control | GDP | GDP growth (annual %) |
| Control | GOV_EX | Government final consumption expenditure (annual % growth) |
| Control | EX_RATE | Official exchange rate (LCU per US$) |
| Control | POP | Total population |
| Control | INF | Inflation, consumer prices (annual %) |
| Control | TRADE | Trade (% of GDP) |
| Control | HDI | Human Development Index |

Skewed variables (EXP, FDI, EX_RATE, POP, INF, TRADE) were log-transformed
after checking histograms, so coefficients on logged variables read as
elasticities.

## Methodology

1. **Descriptive statistics and correlation** (`sum`, `pwcorr`)
2. **Multicollinearity:** VIF (mean VIF = 2.07, maximum 3.78, all below 5)
3. **Model selection:** Pooled OLS vs. Fixed Effects (F-test), Fixed vs.
   Random Effects (Hausman test)
4. **Diagnostics:** Breusch-Pagan (heteroskedasticity) and Wooldridge
   (serial correlation). Both tests flag problems, so FE estimates are not
   reliable on their own.
5. **Two-step System GMM** (`xtabond2`, robust standard errors, collapsed
   instruments) to handle endogeneity between FDI and exports,
   heteroskedasticity and autocorrelation. Validity is checked with AR(1),
   AR(2), Sargan and Hansen tests.
6. **Subgroup estimation** for UMIs and LMIs.

## Key Results

### Two-step System GMM (dependent variable: ln_EXP)

| Variable | 47 Asian countries | UMIs | LMIs |
|---|---|---|---|
| L.ln_EXP | 0.617*** | 0.687*** | 0.844*** |
| **ln_FDI** | **0.0625\*\*** | **0.0789\*\*** | **0.105\*\*** |
| GDP | 0.0167*** | 0.0174** | 0.0239*** |
| GOV_EX | -0.00968 | -0.0125 | -0.00243 |
| ln_EX_RATE | -0.0199* | -0.0180 | 0.0191 |
| ln_POP | 0.342*** | 0.245** | 0.0660 |
| ln_INF | -0.0381* | -0.0136 | 0.0335 |
| ln_TRADE | 0.331*** | 0.277** | 0.0317 |
| HDI | 3.397*** | 3.101* | 0.102 |
| Observations / groups | 685 / 39 | 366 / 20 | 319 / 19 |
| AR(2) p-value | 0.077 | 0.190 | 0.287 |
| Hansen p-value | 0.355 | 0.894 | 0.844 |

\* p<0.1, \*\* p<0.05, \*\*\* p<0.01

### Main findings

- **FDI raises exports.** A 1% increase in FDI is associated with a 0.0625%
  increase in exports across the 47 countries, 0.0789% in UMIs and 0.105% in
  LMIs, all significant at 5%.
- **Export persistence.** Lagged exports are positive and significant in all
  samples, with coefficients below 1.
- **Other drivers (full sample):** GDP growth, population, trade openness and
  HDI have positive effects, while exchange rate and inflation have negative
  effects (10% level).
- **Heterogeneity.** Population, trade openness and HDI are significant in the
  full sample and in UMIs but not in LMIs. Government spending is
  insignificant everywhere.

## Policy Implications

- Improve the investment environment (e.g. tax incentives, simpler
  administrative procedures) to attract FDI.
- Invest in infrastructure, digital technology, energy, education and health
  to support growth and raise HDI.
- Join free trade agreements and promote trade to deepen participation in
  global value chains.
- Keep inflation and the exchange rate stable through coordinated fiscal and
  monetary policy.

## Repository Structure

```
├── data/          # Panel dataset (.dta) built from WDI and HDR
├── do-files/      # Stata scripts: descriptives, tests, OLS/FE/RE, System GMM
├── output/        # Regression tables and figures
├── paper/         # Full paper (PDF)
└── README.md
```

## How to Reproduce

Requires Stata and the user-written packages below.

```stata
ssc install xtabond2
ssc install estout
ssc install xttest3
ssc install xtserial

xtset COUNTRY_ID YEAR

* Fixed effects, random effects and Hausman test
xtreg ln_EXP ln_FDI GDP1 GOV_EX1 ln_EX_RATE ln_POP1 ln_INF HDI ln_TRADE, fe
est sto fe
xtreg ln_EXP ln_FDI GDP1 GOV_EX1 ln_EX_RATE ln_POP1 ln_INF HDI ln_TRADE, re
est sto re
hausman fe re

* System GMM, full sample
xtabond2 ln_EXP l.ln_EXP ln_FDI GDP GOV_EX ln_EX_RATE ln_POP ln_INF ln_TRADE HDI, ///
    gmm(L3.ln_EXP L.ln_FDI, lag(1 2) collapse) ///
    iv(GDP L2.GOV_EX ln_EX_RATE ln_POP ln_INF ln_TRADE HDI) two robust
```

The full set of commands (including the UMI and LMI subsamples) is in
`do-files/`.

## Limitations

- Unbalanced panel with missing values (government expenditure has only 974
  observations), which reduces the usable sample.
- Data limitations prevent a VAR/VECM approach; results show conditional
  associations controlled for endogeneity, not proof of causality.
- Income groups are limited to UMIs and LMIs; other classifications (OECD,
  newly industrialized, emerging) are not estimated.

## Tools

Stata, `xtabond2`, `estout`

*Undergraduate thesis project. For academic purposes only.*
