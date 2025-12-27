# Threshold Justification

## Purpose

This document explains why specific analytical thresholds were chosen in the project and how they were derived from the observed data distribution, rather than being arbitrary or hard-coded assumptions.

The intent is transparency:

- to make analytical decisions auditable,
- to allow reviewers to challenge or recalibrate thresholds, and
- to clearly separate judgemnent-based heuristics from mathematical facts.

## Thresholds Covered

This project uses two primary thresholds that directly infuence interpretation:

1. `SMALL_BASE` threshold → `Registered` < 100
2. `LOW_RATE` threshold → `Appearance Rate` < 0.98

Each is justified independently below.

## SMALL_BASE Threshold (Registered < 100)

### Why a base threshold is required?

Appearance rates are ratios. Ratios become statistically unstable when denominators are small.

For example:

- A change of ±2 students on a base of 20 produces a ±10% swing.
- The same change on a base of 2,000 produces only a ±0.1% swing.

Without explicitly accounting for denominator size, small segments can visually dominate analysis while carrying negligible statistical weight.

---

### Observed distribution of Registered counts (non-zero rows)

The following profiling was performed on non-zero `Registered` values:

| Metric | Value |
|--------| ---------|
| Smallest non-zero base | 22 |
| 25th percentile (non-zero) | 1,385.75 |
| Median (non-zero) | 4,067.5 |
| Count below 30 | 1 |
| Count below 50 | 2 |
| Count below 100 | 9 |
| Count below 1,000 | 16 |
| Total non-zero rows | 74 |
| Total rows | 96 |

---

### Interpretation

- Only 9 out of 96 rows (9.4%) have `Registered` < 100.
- These rows sit far below the 25th percentile, indicating extreme base fragility.
- Beyond ~100 registrations, appearance-rate variability visibly stabilizes.

---

### Decision

        SMALL_BASE := Registered < 100

This cutoff:

- isolates statistically fragile denominations,
- preserves interpretability for large-base segments,
- is conservative (err on the side of caution),
- and is explicitly documented for recalibration.

This is an analyst judgement informed by distributional evidence — not a universal constant.

## LOW_RATE Threshold (Appearance Rate < 0.98)

### Why a rate threshold is required

Most appearance rates clustered tightly near 1.00.
Small deviations near the lower tail are rare and therefore analytically meaningful only when base size is sufficient.

---

### Observed distribution of appearance rates

Profiling was performed on non-blank appearance rates:

| Metric | Value |
| -------- | --------- |
| Total rows | 96 |
| Non-blank appearance rates | 74 |
| Blank rows (structural zeros) | 22 |
| Count below 95% | 0 |
| Count below 96% | 1 |
| Count below 98% | 2 |
| Count below 98.5% | 3 |
| Count below 98.6% | 3 |
| Count below 98.7% | 4 |
| Count below 98.8% | 7 |
| Count below 98.9% | 9 |
| Count below 99% | 11 |
| 25th percentile (non-blank) | 0.9934 |
| Median | 0.9970 |

---

### Interpretation of LOW_RATE

- Appearance rates below 0.98 sit in the extreme lower tail.
- Only 2 large-base rows fall below this level.
- Rates above 0.98 represent the dominant operational norm.

---

### Decision of LOW_RATE

        LOW_RATE := Appearance Rate < 0.98

This threshold:

- isolates statistically uncommon behavior.
- avoids flagging noise.
- focuses reviewer attention on material deviations.

This threshold flags rarity, not error or wrongdoing.

---

### Interaction with Base Size (Critical)

LOW_RATE is never interpreted in isolation.

Rules applied in the project:

- LOW_RATE on SMALL_BASE → treated as high-risk but low-impact
- LOW_RATE on VALID_BASE → materially relevant
- Perfect rates on SMALL_BASE → flagged as `SUSPICIOUS_PERFECTION`

This prevents:

- small bases from driving narratives,
- perfect rates from being misinterpreted as excellence,
- low-impact anomalies from overstating risk.

## Where These Thresholds Are Applied

| Threshold | Applied In | Purpose |
| --------- | ---------- | ------- |
| SMALL_BASE | Power Query | Denominator stability classification |
| LOW_RATE | Power Query | Anomaly flagging |
| Coverage Share | Power Pivot (DAX) | Weight-aware interpretation |
| Integrity Checks | Data Quality Log | Logical validity auditing |

## Interpretation Boundaries

These thresholds:

- do not imply casuality
- do not indicate error or misconduct
- do not rank regions or institutions

They exist to protect interpretation, not to judge performance.

## Notes

- All thresholds are explicitly documented
- All are data-driven
- All can be recomputed or adjusted without restructuring the model
- No threshold is hidden inside formulas or visuals

This ensures the analysis is defensible, reproducible, and review-safe.
