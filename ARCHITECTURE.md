# Architecture

**Status:** draft agreed in principle by Prateek, 12 November 2026. Bayesian framing added at his request, 13 November 2026. Nothing below is built yet. The bot still runs the plain template.

**The idea in one line:** start from the best prior available, update only on real evidence, then check the updates against reality.

```
Scheduler (GitHub Actions, every 20 min, main branch only)
  -> Fetch open questions
  -> 1. Read the question     what exactly counts as YES
  -> 2. Prior                 the best starting number we can find
  -> 3. Update                move it only for real new evidence
  -> 4. Correct               fix the bot's measured biases
  -> Post forecast + short reasoning comment
  -> Log everything to data/forecasts.jsonl
```

## 1. Read the question
Restate the resolution criteria: what counts as YES, the resolution source, the deadline and the edge cases. Every later step works from this restatement, not from the raw question text. Misreading the question is the cheapest way to lose points.

## 2. Prior
Find the best starting number, in this order:
1. A prediction market price (Polymarket, Kalshi, Manifold), but only when both conditions hold:
   - the market is liquid enough that a few traders cannot set the price (Manifold's play money counts for less);
   - its resolution rules match the question. A market on a different question is not a prior for this one.

   Markets are known to overprice long shots, and long-dated ones sit too close to 50%. The update step may correct for both. Prateek chose this option on 13 November 2026, with the conditions.
2. For numeric questions, a prior distribution from a simple statistical model of the historical data, such as FRED.
3. Otherwise, a base rate from a reference class, scaled to the time left before the deadline.

The prior and its source are written into the research brief. Most of the accuracy should come from this step.

## 3. Update
Five forecasters, from different model families, each read the prior, the news and the status quo ("what happens if nothing changes?"). Each one says whether to move away from the prior, by how much and why. The burden of proof is on moving, not on staying.

The five forecasts are combined on the odds scale: average their log-odds, then push the result slightly away from 50% ("extremising"). This has beaten the plain median in research on combining forecasters (Satopää and colleagues; the Good Judgment Project). The plain median stays available as a setting, so the two can be compared.

For numeric questions, the update shifts or widens the prior distribution.

## 4. Correct
Correct the bot's raw numbers for biases measured on resolved questions, for example "says 80% but is right 70% of the time". The correction is Bayesian: it stays close to "no change" until the test data clearly shows a bias, so a small sample does not make it chase noise. Cap binary forecasts at 1% and 99%. For multiple choice, give every option a small floor. For numeric, run the distribution through the package's CDF standardisation.

## What holds it together
**A test set of resolved questions**, with news searches limited to before each question opened. Every step is a separate module switched on in `config/pipeline.yaml`. Each configuration has a name (for example `v0-template`, `v1-spine`). A step stays only if it improves the score on the test set.

Variants are compared with a Bayesian estimate of the difference in score. The report says "about an 85% chance v1 is better than v0", not just "v1 scored better". When the chance is near 50%, the report says the sample is too small to tell.

## Build order
1. `v0-template`: the plain template, to get a baseline score.
2. `v1-spine`: steps 1 to 4 above, with log-odds pooling and Bayesian calibration.
3. `v2-bayes-update`: in step 3, each forecaster updates in odds, one piece of evidence at a time. It asks how much more likely each piece of evidence is if YES is true than if NO is true. Related news is grouped by underlying fact first, so the same event is not counted twice. The total move away from the prior is capped. Tested against v1's free-form update, because LLMs are poor at putting numbers on these ratios.
4. Later variants, one change at a time, only to fix a problem the tests show.

## Not planned
A full formal Bayesian model of each question, such as a network of linked causes. It would be slow and fragile, and the LLM would be inventing most of the numbers.

## Parked ideas
These address problems we have not measured yet. Each gets built as a named variant only if the tests show the problem it fixes.
- Fizzle rate as its own step (now part of the status quo check).
- Evidence grades: evidenced, assumed, contested.
- Keystone assumption, for questions with several conditions.
- A primary-source check on key figures.
- A forecaster that argues against the others.
- Wider tails on numeric questions.
- Spending more on hard questions.

Several of these come from Dark Matter Labs' risk work. See `proposals/2026-11-13-v1-spine.md`.
