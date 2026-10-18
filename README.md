# Credit Risk Scorecard & IFRS 9 Expected Credit Loss System

End-to-end PD / LGD / EAD / ECL system, built and documented the way a bank's model
development and model validation functions would. See
[`docs/problem_statement.md`](docs/problem_statement.md) for the full scope.

## Status

- [x] Phase 0 — Problem statement
- [ ] Phase 1 — Data & EDA
- [ ] Phase 2 — Feature engineering (WOE/IV)
- [ ] Phase 3 — PD model (scorecard + challenger)
- [ ] Phase 4 — Model validation
- [ ] Phase 5 — LGD, EAD, ECL, staging
- [ ] Phase 6 — Serving, tracking, monitoring, CI
- [ ] Phase 7 — Portfolio writeup

## Project layout

```
docs/           problem statement, model validation report, writeups
data/raw/       untouched source data (not committed)
data/processed/ cleaned / feature-engineered data (not committed)
notebooks/      exploratory work
src/            reusable pipeline code (feature engineering, training, serving)
reports/        generated reports and figures
```
