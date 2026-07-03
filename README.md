# Closed-Loop Wearable Adherence Analysis

Code I wrote to look at patient adherence for a closed-loop analgesic wearable I'm working on. The device is still in the modeling phase, no real clinical data yet, so everything here runs on synthetic data.

## Why

It's easy to spend all your time on sensor accuracy and algorithm response time and forget that none of it matters if patients stop wearing the thing. Before touching hardware priorities again, I wanted to get a clearer picture of how closed-loop mode vs manual mode affects whether people actually keep using it.

## Data

Generated 1,000 fake patients with numpy. Closed-loop mode gets a flat +3 hour bump in wear time, and anything under a 2.5 stability score counts as high dropout risk. These numbers came from skimming a few pain management papers, not a real fit — once I have actual data this needs recalibrating.

## Analysis

This version replaces the earlier synthetic-data approach with a real clinical dataset — 686 breast cancer patients from the German Breast Cancer Study Group (GBSG2), via the `lifelines` package.

Kaplan-Meier curves showed a clear split in recurrence-free survival between patients who received hormonal therapy and those who didn't (log-rank p = 0.0034). But before trusting a Cox model's conclusions, I checked the proportional hazards assumption — turned out both `age` and `tumor grade` violated it, meaning their effect on risk wasn't constant over time. Stratifying on both fixed that, and it wasn't just a formality: concordance index went from 0.673 to 0.714 after the fix.

In the corrected model, hormonal therapy held up as a strong, independent predictor (HR = 0.69, p < 0.005) — about a 31% lower recurrence risk after controlling for nodal involvement, tumor size, age, and grade.

Also compared the corrected Cox model against a Random Survival Forest. Cox came out ahead on concordance index (0.714 vs 0.650), and interestingly, RSF's permutation importance ranked hormonal therapy as contributing almost nothing to prediction — which contradicts what the Cox model found. Worth noting as a case where two valid models disagree on how much a variable matters.

## What it shows

Patients with high baseline pain (around a 9) stick with closed-loop mode noticeably more than manual. Makes sense — if you're in that much pain, you don't want to be fiddling with settings yourself.

Also threw together a rough "treatment value at risk" number, assuming $2,000 in lost value per patient who drops off. Comes out to about $500k. That number is a placeholder to give people a sense of scale, not something to put in a real budget.

## TO DO

- Swap in real trial data once it exists
- Dropout logic doesn't account for battery life or skin irritation yet
- Need to check the $2,000/patient figure with someone on the clinical side, I made it up
