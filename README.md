# Closed-Loop Wearable Adherence Analysis

Code I wrote to look at patient adherence for a closed-loop analgesic wearable I'm working on. The device is still in the modeling phase, no real clinical data yet, so everything here runs on synthetic data.

## Why

It's easy to spend all your time on sensor accuracy and algorithm response time and forget that none of it matters if patients stop wearing the thing. Before touching hardware priorities again, I wanted to get a clearer picture of how closed-loop mode vs manual mode affects whether people actually keep using it.

## Data

Generated 1,000 fake patients with numpy. Closed-loop mode gets a flat +3 hour bump in wear time, and anything under a 2.5 stability score counts as high dropout risk. These numbers came from skimming a few pain management papers, not a real fit — once I have actual data this needs recalibrating.

## What it shows

Patients with high baseline pain (around a 9) stick with closed-loop mode noticeably more than manual. Makes sense — if you're in that much pain, you don't want to be fiddling with settings yourself.

Also threw together a rough "treatment value at risk" number, assuming $2,000 in lost value per patient who drops off. Comes out to about $500k. That number is a placeholder to give people a sense of scale, not something to put in a real budget.

## TO DO

- Swap in real trial data once it exists
- Dropout logic doesn't account for battery life or skin irritation yet
- Need to check the $2,000/patient figure with someone on the clinical side, I made it up
