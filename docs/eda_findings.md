# Phase 1 — EDA Findings

## Dataset
Lending Club accepted loans, 2007-06 to 2018-12 (2,260,701 rows). Scoped to 24
origination-time columns + 8 performance-only columns (used solely to define
the target, never as model inputs — see leakage note in problem_statement.md).

## Target definition
Of 9 raw `loan_status` values, only `Fully Paid`, `Charged Off`, and `Default`
represent a fully resolved outcome. The rest (`Current`, `Late (31-120 days)`,
`In Grace Period`, `Late (16-30 days)` — ~912k loans, ~40% of the dataset) are
still open and were excluded rather than labeled as non-default. The two
`Does not meet the credit policy...` statuses (2,749 loans) were also excluded,
since those loans were originated under different, pre-policy-change
underwriting criteria.

Naive default rate on the full raw dataset: ~11.9% (misleadingly low, inflated
by censored "Current" loans). True default rate on the matured population:
~20%.

## Vintage / immaturity bias
Default rate by origination quarter rises fairly steadily from 2008 through a
peak around 2016, consistent with Lending Club's known volume-driven
underwriting loosening over that period. From 2017 Q3 onward, both matured
loan count and default rate break down — not a real improvement, but a
censoring artifact: loans that recent haven't had time to reach a terminal
status. Vintages from 2017 Q3 onward were excluded from all modeling.

## Out-of-time split
- Train: 2007-06 to 2015-12 (826,606 loans, 18.4% default rate)
- OOT validation: 2016-01 to 2017-06 (384,471 loans, 23.3% default rate)

The default rate gap between train and OOT is a real trend (rising credit
risk over time at Lending Club), not noise — and if anything the OOT figure
understates the true rate, since even loans inside that window haven't all
fully matured.

## Missingness (train set)
| Column | % missing |
|---|---|
| emp_length | 5.15% |
| last_pymnt_d | 0.08% |
| revol_util | 0.05% |
| dti | 0.0002% |

No major missingness issues. `emp_length` missingness is plausibly itself a
weak risk signal (e.g. non-traditional employment) rather than noise to
impute away — will get its own WOE bin in Phase 2 rather than imputation.