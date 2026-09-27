# Automation Candidate Selection

## Candidate scorecard

Rate each factor from 1 (low) to 5 (high).

| Factor | Why it matters |
|---|---|
| Business risk | Higher-risk flows deserve repeatable protection |
| Execution frequency | Frequently repeated checks gain more from automation |
| Data determinism | Stable data makes automation trustworthy |
| Interface stability | Stable interfaces reduce maintenance cost |
| Manual effort | Time-consuming repetitive checks may deliver high ROI |
| Failure diagnosability | Poorly diagnosable tests create maintenance burden |

## Example decisions

| Scenario | Frequency | Risk | Stability | Decision |
|---|---:|---:|---:|---|
| Login | 5 | 5 | 5 | Automate |
| Checkout | 5 | 5 | 4 | Automate at API + selected UI |
| Price calculation permutations | 5 | 5 | 5 | Automate below UI |
| Experimental UI prototype | 1 | 2 | 1 | Explore manually first |
| CAPTCHA | 1 | 3 | 1 | Bypass/mock in test environment; do not automate real challenge |

## ROI reminder

Do not use a simplistic formula alone. Consider maintenance, execution infrastructure, triage cost, and the probability that a failure would lead to action.
