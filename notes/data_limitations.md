# Data Limitations & Interpretation Constraints

This document outlines the known limitations, scope boundaries, and interpretive constraints of the CBSE Class 12 (2022) appearance dataset used in this project.

These limitations do not invalidate the analysis; rather, they define the conditions under which the results should be read responsibly.

## Scope of the Dataset

This analysis is limited strictly to exam appearance counts and does not include:

- Student performance or scores
- Pass/fail outcomes
- Demographic attributes (gender, category, socioeconomic status)
- School-level operational practices
- Temporal trends beyond the 2022 examination year

As a result, no behavior, casual, or outcome-based conclusions can be drawn from this dataset.

## Aggregation Level Constraints

This dataset is aggregated at the level of:

- Region x School Type

This implies:

- Individual schools are not observable
- Within-region heterogeneity is masked
- High or low appearance rates may reflect localized effects that cannot be isolated further

All findings should therefore be interpreted as system-level signals, not institution-specific diagnostics.

## Structural Zeros vs Missing Data

Certain Region x School Type combinations show:

- Registered = 0
- Appeared = 0

These are treated as structural zeros, meaning:

- The school type does not operate in that region
- The absence reflects system structure, not data quality failure

Structural zeros are:

- Logged explicitly in the Data Quality Log
- Excluded from rate-based analysis
- Reported transparently to avoid misinterpretation

They should not be interpreted as underperformance or administrative error.

## Small-Base Instability

Small segments operate on very small registered counts.

Limitations introduced by small bases:

- Appearance rates become highly sensitive to small absolute changes.
- Perfect (100%) rates may occur due to scale rather than exceptional performance.
- Volatility metrics are less reliable in these segments.

To mitigate this:

- Small bases are explicitly flagged (`SMALL_BASE`)
- Such segments are excluded from system-level interpretation
- Their statistical weight is reported separately using coverage share

## Threshold Dependence

Key analytical thresholds used in this project include:

- `SMALL_BASE` < 100
- `LOW_RATE` < 0.98

These thresholds:

- Are analyst-defined, not universal constants
- Were chosen based on observed distribution behavior
- Are documented transparently in `threshold_justification.md`

Different thresholds may yield different classifications.
The analysis is therefore conditional, not absolute.

## Single-Year Snapshot

This project analyzes only one examination year (2022).

As a result:

- Trends over time cannot be assessed
- Stability across years cannot be inferred
- Exceptional events specific to 2022 cannot be separated from structural patterns

All conclusions are cross-sectional, not longitudinal.

## Rate Interpretation Caveats

Appearance rates are ratios and are subject to:

- Denominator fragility in small bases
- Masking effects when aggregated
- Over-interpretation if not paired with coverage weight

This project explicitly separates:

- Frequency (how many segments exist)
- Weight (how much of the system they represent)

Any interpretation that ignores coverage share is incomplete.

## What This Analysis Does Not Claim

This project does not claim:

- Casuality
- Fault or negligence
- Administrative effectiveness
- Policy success or failure

All results are diagnostic, not evaluative.

## Intended Use

This analysis is designed for:

- Data quality assessment
- Credibility screening before interpretation
- Identifying where caution is required
- Supporting further, more granular investigation

It is not intended to serve as a final decision-making instrument on its own.

## Summary

The findings in this repository are valid within clearly defined boundaries.

By explicitly documenting limitations, this project prioritizes:

- Transparency
- Reproducibility
- Responsible interpretation

These constraints are not weaknesses — they are an essential part of professional analytical practice.
