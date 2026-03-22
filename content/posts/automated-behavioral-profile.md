---
title: "From Script to System: The First Automated Behavioral Profile"
date: 2026-03-22
tags: ["Data Science", "Python", "Infrastructure", "Mental Health", "EMA", "Predictive Modeling"]
categories: ["Insights", "Publications"]
author: "Amanda Colares"
showToc: true
TocOpen: true
cover:
    image: "https://github.com/user-attachments/assets/11032fb4-99d0-451c-9bc5-4a88c0ba653a"
    alt: "PsycheMap Automated Behavioral Profile"
    caption: "First automated patient profile — execution BATCH-20260322-213132"
    relative: false
---

The previous article in this series ran a Python exploration script manually against a CSV export. This article documents a different kind of milestone: the first time the system computed a behavioral profile automatically, wrote it to a production database, and tagged it with a traceable execution code — without any manual intervention.

Execution code: `BATCH-20260322-213132`. Computed at 21:31 UTC on March 22, 2026.

This post documents what that first computation found, how the underlying algorithm works, and what it means for the evolution of PsycheMap from an analytical tool into a clinical infrastructure.

---

## What Changed: From Exploration to Production

The previous analysis used a standalone Python script with `RandomForestRegressor`, `scipy.signal.find_peaks`, and a manually exported CSV. It was designed for exploration — answering the question "what does this data show?"

The new system is designed for operation. It runs inside a FastAPI service, reads directly from the production Supabase database, and is structured around eight behavioral dimensions that are computed independently and composed into a single persistent profile. Each weekly execution inserts a new row into `patient_profile` — preserving full history — and tags every row with a human-readable execution code in the format `BATCH-YYYYMMDD-HHMMSS`.

The key architectural decision was determinism. Given the same input data, the computation must always produce identical output. The first validation of this property happened the same evening: a second execution at 21:36 UTC returned identical values across all 36 metrics. The system is stable.

---

## Dataset

| Metric | Value |
| :--- | :--- |
| **Patient ID** | 1 |
| **Execution code** | BATCH-20260322-213132 |
| **Total records** | 245 |
| **Monitoring period** | 144 days |
| **Confidence level** | High |

The confidence level of `high` is reached after 90 days of data. At this threshold, the full personal baseline alert system becomes active — meaning the system no longer compares this patient against population averages, but against their own established behavioral patterns.

---

## Dimension 1: Mood Baseline

The mood baseline is computed from all journal entries with categorized emotions, aggregated to daily means and measured across multiple temporal slices.

| Metric | Value |
| :--- | :--- |
| **mood_mean** | 3.023 |
| **mood_std** | 1.476 |
| **mood_min** | 1.0 |
| **mood_max** | 5.0 |
| **morning_baseline** (before 12h BRT) | 2.977 |
| **night_baseline** (after 20h BRT) | 3.034 |
| **monday_baseline** | 2.841 |
| **recency_weighted_mean** | 3.059 *(second run)* |

The standard deviation of 1.476 on a 1–5 scale is clinically significant. It means that on any given day, this patient's mood is likely to be roughly 1.5 points away from their own average — in either direction. This is not noise. It is the shape of how this person experiences affect.

The Monday baseline of 2.841 is the lowest temporal slice measured, sitting 0.18 points below the overall mean. The pattern is consistent enough across 144 days to be retained as a behavioral signal rather than dismissed as variance.

The `recency_weighted_mean` of 3.059 — computed by weighting the last 30 days at 2× and earlier data at 1× — sits slightly above the overall mean of 3.023. This suggests a mild recent improvement in baseline mood relative to the full monitoring period, which is consistent with the longitudinal trend observed in the previous analysis.

---

## Dimension 3: Entry Behavior Baseline

| Metric | Value |
| :--- | :--- |
| **entries_per_active_day** | 2.23 |
| **preferred_hour** | 17h BRT |
| **late_night_ratio** | 13.5% |
| **avg_note_length** | 225 characters |
| **avg_emotions_per_entry** | 1.47 |
| **silence_baseline_days** | 3.0 |

The preferred logging hour of 17h (5pm) is consistent with end-of-workday reflection. The late night ratio of 13.5% — entries logged between 22h and 04h BRT — is a behavioral marker. In this patient's history, late night entries cluster around periods of elevated distress. Any week where this ratio increases above baseline triggers a passive signal to the clinician dashboard.

The silence baseline of 3.0 days represents the 95th percentile of gaps between active logging days. This means that a gap of more than 3 days without a journal entry is anomalous for this patient. Silence, in this context, is data.

The average note length of 225 characters indicates substantive entries. This matters for Dimension 4 — language baseline analysis — which is currently disabled pending LLM cost optimization but is architecturally ready to activate.

---

## Dimension 2: Cycle Pattern

| Metric | Value |
| :--- | :--- |
| **cycle_detected** | True |
| **cycle_score** | 0.676 / 1.0 |
| **current_phase** | post_peak_descending |
| **current_phase_days** | 5 |
| **avg_peak_to_trough_days** | 10.3 |
| **avg_peak_to_trough_intensity** | 1.129 pts |
| **avg_crisis_interval_days** | 16.8 |
| **avg_stability_interval_days** | 17.2 |

The cycle detection algorithm applies `scipy.signal.find_peaks` to a 7-day exponential moving average of daily mood scores. Peaks require a smoothed score above 3.2 with a minimum distance of 7 days between events. Valleys require a smoothed score below 2.8 with the same spacing constraint. Only peak-to-valley transitions with an intensity drop greater than 0.3 points are retained as meaningful cycles.

The cycle score of 0.676 is a composite of two factors: volume (how many complete cycles have been detected) and consistency (how regular the intervals are, measured by coefficient of variation). A score of 0.676 indicates that this patient has a detectable, moderately regular cycle — not a fixed-period clock, but a recognizable shape.

The current phase is `post_peak_descending`, active for 5 days at the time of computation. The phase classification is derived from the slope of the last 7 days of smoothed data combined with the absolute level of the last data point. A slope below -0.01 per day with a smoothed score above 2.5 triggers `post_peak_descending` — the phase the system flags as highest clinical risk, because the contrast between recent peak and current descent is where acute distress most commonly appears in this patient's history.

Given the historical average of 10.3 days from peak to trough, this patient was approximately 5 days from their historical floor at the time of computation.

The near-symmetry between average crisis interval (16.8 days) and average stability interval (17.2 days) is a structural finding. This patient alternates between crisis and stability at nearly identical cadence — a rhythm the system has now learned and will use to probabilistically estimate the next high-risk window.

---

## Predictive Diagnostics

| Metric | Value |
| :--- | :--- |
| **model_mae** | 0.9811 |
| **model_r2_pct** | 4.08% |
| **statistical_trend** | ESTÁVEL |
| **ai_projection** | Queda |

The predictive module uses two separate methods. The statistical trend is derived from a `LinearRegression` fit over the full monitoring window — it sees the 144-day arc as approximately flat. The AI projection is derived from the slope of the 7-day EMA over the last 14 days — it sees the recent post-peak descent and calls it a fall.

Both are correct. They are measuring different things. The linear model describes structural stability over months. The EMA slope describes directional momentum over days. The divergence between them — `ESTÁVEL` vs `Queda` — is not a contradiction. It is the system saying: the long-term trajectory is intact, but the short-term is descending. That combination is exactly the clinical context a `post_peak_descending` phase requires.

The R² of 4.08% is low in absolute terms and is documented as such. The goal of the predictive module is not accuracy in the traditional machine learning sense. It is directional signal — detecting when something is changing — combined with behavioral baseline deviation from Dimensions 1 and 3. Those three signals together form the alert logic. No single metric triggers clinical action alone.

---

## What This Execution Established

This first automated computation establishes three things that did not exist before March 22, 2026.

First, a persistent behavioral fingerprint for patient 1 — a 36-metric baseline that will be recomputed weekly and compared against itself over time. The question the system will answer next Sunday is not "how is this patient doing?" but "how is this patient doing *compared to who they were last week, and the week before?*"

Second, a traceable execution history. Every profile row is linked to the `profile_computation_log` table via `execution_code`. If the algorithm changes, every historical row still reflects the computation that produced it. The history is immutable.

Third, a validated determinism guarantee. Two independent runs on the same data produced identical results across all 36 metrics. The system is stable enough to trust.

---

## What Comes Next

Dimension 4 — language baseline analysis — is architecturally complete but disabled. It will activate when the LLM inference pipeline is cost-optimized for weekly batch execution. The infrastructure that will run it is already in place: average note length of 225 characters provides sufficient text volume for linguistic drift detection and passive/active ideation classification.

The `crisis_frequency_baseline` and `crisis_intensity_baseline` fields returned null in this first execution. These require the crisis emotion marker to be present in the emotion records data. They will populate once the relevant query path is confirmed.

The alert system is ready to activate for this patient. The confidence level is `high`. The behavioral fingerprint exists. The current phase is clinically significant. The clinician has not yet been notified — because that last step, the notification pipeline, is what gets built next.

---

*Profile computed via execution BATCH-20260322-213132. Source: PsycheMap production database, patient ID 1, monitoring window 2025-01-01 to 2026-03-22, filtered to entries with entry_date_time >= 2025-01-01. Algorithm: FastAPI service, Python 3.12, pandas, scipy, scikit-learn, Supabase Python client.*