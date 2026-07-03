# Patient Adherence & Discontinuation Risk Analysis

Started this as a way to test out survival analysis methods before applying them to a closed-loop analgesic wearable I'm working on — the device is still in the modeling phase, so there's no real usage data from it yet. Instead of running the methodology on made-up numbers, I applied it to a real clinical dataset to see if the approach actually holds up.

## Why

It's easy to spend all your time on sensor accuracy and algorithm response time and forget that none of it matters if patients stop using the thing. Before I had real device data to work with, I wanted to build and stress-test the analysis pipeline on something real, so it's ready to go once the device generates its own usage logs.

## Data

Used the GBSG2 dataset (German Breast Cancer Study Group, via the `lifelines` package) — 686 real patients, with recurrence-free survival time and whether each patient received hormonal therapy, along with age, tumor size, tumor grade, and nodal involvement.

## Analysis

Kaplan-Meier curves showed a clear split in recurrence-free survival between patients who received hormonal therapy and those who didn't (log-rank p = 0.0034). But before trusting a Cox model's conclusions, I checked the proportional hazards assumption — turned out both `age` and `tumor grade` violated it, meaning their effect on risk wasn't constant over time. Stratifying on both fixed that, and it wasn't just a formality: concordance index went from 0.673 to 0.714 after the fix.

In the corrected model, hormonal therapy held up as a strong, independent predictor (HR = 0.69, p < 0.005) — about a 31% lower recurrence risk after controlling for nodal involvement, tumor size, age, and grade.

Also compared the corrected Cox model against a Random Survival Forest. Cox came out ahead on concordance index (0.714 vs 0.650), and interestingly, RSF's permutation importance ranked hormonal therapy as contributing almost nothing to prediction — which contradicts what the Cox model found. Worth noting as a case where two valid models disagree on how much a variable matters.

## TODO

- Try a time-varying covariate approach for age and tumor grade, as an alternative to stratification, to see if it changes the hazard ratio estimates
  
- Add confidence intervals around the concordance index comparison (currently just point estimates) via bootstrapping
  
- Explore why RSF and the Cox model disagree on hormonal therapy's importance — could be sample size, could be RSF underweighting linear effects, worth digging into further

- Once the wearable has real usage data, apply this same pipeline to it
