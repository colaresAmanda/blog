---
title: "Why Mood Tracking is Not Enough: The Logic of the Baseline"
date: 2026-03-21T11:00:00-03:00
draft: false
description: "How to notify a clinician without flooding their WhatsApp: The 8-Dimension Baseline."
tags: ["Data Science", "Psychology", "Python", "Clinical Support"]
categories: ["Research"]
---

The whole idea of the PsycheMap app is to send a clinician a notification when a patient shows a behavior that is different from their "normal." 

But I got stuck on a major problem. For example: if a patient has recurrent suicidal ideations as part of their chronic state, and I set an alert for every "Muito Ruim" entry, I would simply flood the psychologist's WhatsApp with notifications. The clinician would stop paying attention because the "noise" is constant.

This brought me to a critical conclusion: to help the clinician, the AI doesn't need to know what "Sadness" is in general. It needs to understand the **Baseline of Functioning** of that specific person.

Sometimes, if a patient has recurrent suicidal thoughts and they suddenly "improve" or become unusually calm, that is actually the alert signal (the Dangerous Calm). Without a baseline, the AI would think they are just getting better.

To solve this, I am developing 8 Dimensions to define what "Normal" looks like for each individual:

---

## 🗺️ The Complete 8-Dimension Framework

### DIMENSION 1: Mood Baseline
We aren't looking at population averages. We look at the personal weighted mood average and the **Personal standard deviation**. This tells us how much the patient naturally oscillates. If they are within their normal range, it's noise. If they move outside of it, it's a signal.
* **Personal weighted mood average:** (not population average).
* **Personal standard deviation:** — how much they naturally oscillate.
* **Normal oscillation range:** — what's noise vs what's signal for this person.
* **Baseline by time of day:** — morning baseline vs night baseline (they differ).
* **Baseline by day of week:** — some patients are consistently worse on Mondays.
* **Confidence level:** — how many days of data this baseline is built on.
* **Recency weighting:** — last 30 days weighted higher than 90 days ago but full history preserved.

### DIMENSION 2: Cycle Pattern
Does this patient have detectable cycles? We look for inflection points, not fixed windows. 
* **Cycle detection:** Scored 0.0–1.0 by the Cyclic lens.
* **Method:** Inflection points sustained over 3+ days, not fixed windows.
* **Phases:** Identifying if they are ascending / peak / descending / trough / transitional.
* **Historical inflection library:** Every peak and valley stored with what followed.
* **Elasticity:** Cycle length is elastic, not fixed — the shape is consistent, the duration is not.
* **Alert:** Post-peak descent is higher risk than the trough itself for bipolar-like patterns.

### DIMENSION 3: Entry Behavior Baseline
How they interaction with the tool as a biomarker.
* **Normal entries per day.**
* **Normal entry times:** — morning person? late night entries? (late night correlates with crisis in Amanda's data).
* **Normal entry length:** — how many words/characters typically.
* **Normal emotion count per entry:** — 1 emotion vs 3 emotions per entry.
* **Normal audio vs text ratio:** — shift from text to audio or vice versa is a signal.
* **Intensity range:** — their typical 1–10 distribution.
* **Self-categorization patterns:** — do they tend toward middle categories? Do they minimize?
* **Silence baseline:** — their personal maximum normal gap between entries (not population average).

### DIMENSION 4: Language Baseline
The most critical dimension. We map what words appear in "Ok" entries versus "Pre-crisis" entries.
* **Linguistic Early Warning Signal:** Words that appear only in pre-crisis entries.
* **Recovery language markers:** Words that appear only after crisis.
* **Minimization/Amplification:** Does the text suggest "Muito Ruim" while they categorize as "Ok"?
* **Confidence gap score:** Systematic difference between written language and chosen category.
* **Ideation Dimension:** Passive ("não quero existir") vs Active ("já pensei em como") language.
* **Dangerous calm:** Resolution tone, farewell language, unusual neutrality after crisis.
* **Language drift detection:** Is the vocabulary shifting over weeks toward crisis language?

### DIMENSION 5: Crisis Fingerprint
What happened in the 72h before each identified crisis event.
* **First signal detection:** Mood drop? language change? silence? behavior shift?
* **Peak-before-crash pattern:** Does this patient spike before crashing? (Amanda: Happy 10/10 → Crash within 48h).
* **Medication/Sleep signals:** Non-compliance or late-night entry increases before crisis.
* **Isolation language signal:** "não consigo mostrar pra ninguém" before silence.
* **Trigger patterns:** Which life domains trigger crisis (work? relationship? financial?).

### DIMENSION 6: Recovery Pattern
How they come out of bad periods — gradual ascent or sudden shift?
* **Recovery speed:** Unusually fast recovery from deep crisis is itself a flag.
* **Anchor activities:** What consistently correlates with mood improvement (swimming, drawing).
* **Dangerous calm detection:** Distinguishing genuine recovery from sudden neutrality after sustained crisis.
* **Silence after positive shift:** Patient was critical, now quiet and calm → highest risk pattern.

### DIMENSION 7: Clinician Context
* **Clinician-validated lens profile:** Which algorithmic pattern the psychologist confirmed fits this patient.
* **Clinical annotations:** Context the data doesn't capture (diagnosis, medication, therapeutic goals).
* **Psychologist override history:** When they said "não reflete" and why.
* **Known external stressors:** Job situation, relationship, family context flagged by the clinician.
* **Session cadence:** Alert urgency is higher if next session is far away.

### DIMENSION 8: Data Quality
* **AI categorization confidence scores:** Across all entries.
* **Self-categorization correction rate:** How often the patient corrects the bot's suggestion.
* **Confidence gap frequency:** How often written text contradicts the chosen category.
* **Data reliability score:** Overall confidence in this patient's dataset for algorithmic use.
* **Minimum data threshold:** Required days of data before each dimension is reliable.

---

### Conclusion
By mapping these 8 dimensions, we aren't just tracking emotions. We are building a "Digital Phenotype" that allows the system to be silent when the patient is in their "Normal Struggle" and only scream when a true deviation occurs. 

This is how we move from a simple diary to a scientific tool for clinical decision support.