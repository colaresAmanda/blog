---
title: "Mapping Emotional Oscillations: A 144-Day Quantitative Analysis"
date: 2026-03-20
tags: ["Data Science", "Python", "Predictive Modeling", "Mental Health"]
categories: ["Insights", "Publications"]
author: "Amanda Colares"
showToc: true
TocOpen: true
---

After approximately **144 days** of consistent data collection, resulting in **360 unique records**, this study synthesizes the initial findings from the **PsycheMap** project. By applying statistical analysis to longitudinal mood tracking, the research aims to move beyond anecdotal evidence into quantifiable psychological trends.

This analysis focuses on identifying the architecture of emotional "Peaks and Valleys" using a hybrid analytical approach.

## Dataset Metrics

The integrity of predictive modeling depends on the density of the data. Over the monitored period, the engagement metrics were as follows:

| Metric | Value |
| :--- | :--- |
| **Total Unique Records** | 360 |
| **Monitored Period** | 144 days |
| **Mean Entry Frequency** | 2.50 entries/day |

With an average of 2.5 entries per day, the dataset provides a high-resolution signal, allowing for the application of **Exponential Moving Averages (EMA)** to filter out short-term noise.

---

## Cycle Analysis: Peaks and Valleys

The system identifies "Crises" (Valleys) and "Stability" (Peaks). The intervals between these events reveal a non-linear but rhythmic pattern in emotional regulation.

### 1. Intervals Between Crisis Events (Red Points)
Tracking the distance between emotional troughs allows for an understanding of the frequency of high-risk periods.
* **Crisis 1 to 2:** 12 days (2025-11-04 to 2025-11-16)
* **Crisis 2 to 3:** 45 days (2025-11-16 to 2025-12-31)
* **Crisis 3 to 4:** 10 days (2025-12-31 to 2026-01-10)
* **Crisis 4 to 5:** 68 days (2026-01-10 to 2026-03-19)

### 2. Stability Intervals (Green Points)
Stability peaks represent periods of emotional equilibrium. The data shows a recurring baseline:
* **Stability 1 to 2:** 20 days
* **Stability 2 to 3:** 37 days
* **Stability 3 to 4:** 12 days
* **Stability 4 to 5:** 20 days
* **Stability 5 to 6:** 13 days

### 3. Correlation Analysis of Cycles
An analysis of the relationship between crisis frequency and stability duration suggests a compensatory pattern. Observations indicate that when multiple crises occur within a short window, they are often followed by a significantly longer period of stability. Conversely, extended periods of stability appear to precede more intense crisis events. 

This suggests a "rebound effect" in emotional regulation, where the system requires longer recovery times after high-frequency volatility. The research focuses on tracking this transition time to predict the shift from crisis to stability.

---

## Velocity and Decay (Dimension 2)

By measuring the transition from a "Peak" (Top) to a "Valley" (Bottom), the **Mean Time to Decay** is calculated. This is a critical metric for early intervention.

* **Mean Decay Time (Top -> Bottom):** 28.8 days
* **Mean Intensity Loss:** 1.18 pts (on a 5-point scale)

### 4. Comprehensive Decay Observations



| Peak Start | Valley End | Days to Fall | Intensity |
| :--- | :--- | :--- | :--- |
| 2025-11-28 | 2025-12-31 | 33 | 1.165 |
| 2025-12-18 | 2025-12-31 | 13 | 1.343 |
| 2026-01-24 | 2026-03-19 | 54 | 0.612 |
| 2026-02-05 | 2026-03-19 | 42 | 1.438 |
| 2026-02-25 | 2026-03-19 | 22 | 1.633 |
| 2026-03-10 | 2026-03-19 | 9 | 0.888 |

### Observation on Decay Trends
Recent data suggests a trend toward increased instability. The "time to fall" has become progressively shorter while the "intensity of the fall" has increased. This combination typically signals a high-stress period requiring active intervention. However, based on the historical patterns established in Sections 1 and 2, it is theorized that this volatility is cyclical and will resolve into a stable state, a trend supported by the predictive data in the next topic.

---

## Model Performance & Predictive Diagnostics

Using a **Random Forest Regressor**, the current model attempts to project future emotional states by identifying non-linear patterns in the historical data.

* **Mean Absolute Error (MAE):** 0.7501
* **Accuracy (R²):** 34.55%

### 1. Hybrid Predictive Diagnostic
The final output of the analysis presents a divergence between traditional linear statistics and AI modeling:

> **Statistical Trend:** FALLING (QUEDA)  
> **AI Projection:** STABLE (ESTÁVEL)

This diagnostic indicates that while recent momentum is downward, the AI model recognizes structural stability in the broader 144-day context, suggesting a plateau rather than a continued crash.

## Insights

1. **The 28-Day Window:** The discovery that it takes an average of 28.8 days to reach a valley provides a significant window for preventative intervention.
2. **Predictive Divergence:** The conflict between statistical falling and AI stability highlights the importance of using non-linear models (like Random Forest) to interpret human psychology.
3. **High-Frequency Accuracy:** The current R² of 34.55% confirms that mood tracking is viable as a data science discipline, provided check-in frequency remains high (above 2.0 entries per day).

Full exploration of the tools used in this analysis is available at the **[PsycheMap App](https://www.psychemapapp.com/app)**.

---
*This analysis was performed on a Pop!_OS environment using Python 3.12 and Scikit-Learn.*