# Problem Statement — Retail Credit Risk Scorecard & IFRS 9 Expected Credit Loss System

## 1. Background / Motivation

Retail and SME lenders need two related but distinct things from a credit risk function:

1. **At origination**: a way to estimate how likely an applicant is to default, so the
   lender can approve/decline and price risk consistently (a *scorecard*).
2. **On an ongoing basis, for financial reporting**: an estimate of expected credit
   losses on the existing book, as required under IFRS 9 (or, in the Indian context,
   the conceptually similar but not identical RBI expected-loss/ECL framework moving
   in from the older incurred-loss IRAC provisioning norms).

Both of these reduce to the same three building blocks:

- **PD** — Probability of Default: likelihood a borrower defaults within a given horizon.
- **LGD** — Loss Given Default: of the exposure that defaults, what fraction is actually lost
  after recoveries/collections.
- **EAD** — Exposure at Default: how much the lender is actually exposed to at the point
  of default (not always the same as the original loan amount — balances change over time).

Combined: **Expected Loss = PD × LGD × EAD**, calculated per loan and aggregated to a
portfolio-level reserve.

### Bridge to banking-side intuition

This largely renames things that already have direct equivalents in day-to-day retail/SME
banking operations:

| Quant / IFRS9 term | Everyday banking equivalent |
|---|---|
| Default | NPA (Non-Performing Asset) classification |
| 12-month ECL | Broadly parallels "standard asset" provisioning |
| Lifetime ECL (Stage 2/3) | Broadly parallels NPA / sub-standard provisioning |
| Staging (Stage 1 / 2 / 3) | Asset classification buckets (standard → SMA → NPA) |
| Scorecard / PD model | Credit appraisal / underwriting decisioning, made statistical |

**Caveat, stated plainly so it isn't glossed over**: IFRS 9 and the RBI's IRAC/ECL
framework are *not* identical — different trigger definitions, different staging
rules, different regulatory histories. The mapping above is a learning aid to connect
new vocabulary to lived experience, not a claim that the two regimes are interchangeable.
The system we build will follow the IFRS 9 framing (PD/LGD/EAD/ECL/staging), since that's
the vocabulary used in the job postings this project is aimed at.

## 2. Objective

Build an end-to-end system, developed and documented the way a bank's model
development + model validation functions would, that:

1. Estimates **PD** at loan origination using an interpretable scorecard
   (logistic regression on WOE-binned features) plus a gradient-boosted
   challenger model for comparison.
2. Estimates **LGD** on defaulted loans using recovery data.
3. Applies a simplified **EAD** treatment.
4. Combines these into **Expected Credit Loss**, with a basic **Stage 1/2/3**
   staging rule.
5. Is validated the way a real model would be: discrimination (AUC, KS, Gini),
   calibration, population stability (PSI), and explainability (SHAP) —
   written up as a short Model Validation Report.
6. Is wrapped as a lightweight **system**, not a notebook: a scoring API,
   containerized, with experiment tracking and a basic drift-monitoring script.

## 3. Data

Lending Club public loan-level historical data (originations, performance, and
recoveries on charged-off loans). Chosen because it is one of the few public
datasets with loan-level default outcomes *and* recovery amounts *and*
origination-time exposure in one place — letting PD, LGD and EAD all be built
from a single, consistent source instead of three incompatible datasets.

## 4. Validation Philosophy (stated up front, because it's easy to get wrong)

Credit risk models are validated **out-of-time (OOT)**, not with a random
train/test split. A model trained on 2015–2017 originations and validated on a
held-out *later* vintage (e.g. 2018) tells you whether it generalizes across an
economic cycle; a random split does not. All splits in this project will be
time-based for that reason.

## 5. Explicit Non-Goals (for this version)

- Not attempting regulatory-grade IFRS 9 compliance (no macroeconomic overlay /
  forward-looking scenario weighting, which a production ECL model would need).
- Not attempting to replicate IRAC/RBI provisioning rules specifically.
- LGD and EAD treatments are simplified versions of what a bank's model would
  actually use (real EAD models use credit conversion factors derived from
  drawn/undrawn facility behavior, which doesn't apply cleanly to Lending Club's
  term-loan structure).

These are named explicitly so the eventual README doesn't overstate what was built —
consistent with not overclaiming skills that weren't actually exercised.

## 6. Deliverables by Phase

| Phase | Deliverable |
|---|---|
| 0 | This problem statement |
| 1 | EDA report (vintage-based default trends, missingness, OOT split defined) |
| 2 | WOE/IV feature set |
| 3 | PD scorecard (logistic + WOE) and LightGBM challenger |
| 4 | Model Validation Report (AUC/KS/Gini, calibration, PSI, SHAP) |
| 5 | LGD model, EAD treatment, portfolio ECL + staging |
| 6 | Experiment tracking (MLflow), FastAPI scoring endpoint, Docker, CI, drift monitor |
| 7 | Public-facing README / writeup for the portfolio |
