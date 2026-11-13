# Architecture

**Status:** agreed in principle by Prateek, 12 November 2026. Bayesian framing added 13 November 2026. On 13 November 2026 Prateek chose to build on nostreambot's code (https://github.com/No-Stream/metaculus-bot, MIT licence, 15th of 277 in Summer 2026). Corrected the same day against nostreambot's tested results (see `proposals/2026-11-13-open-source-bots-review.md`).

This page is Prateek's view of the bot. The code-level walk-through is `docs/architecture.md`. What was tried, and why it was kept or dropped, is in `FUTURE.md`.

**The idea in one line:** start from the best prior available, update only on real evidence, then publish the median of independent forecasts.

```
Scheduler (GitHub Actions + an outside trigger, main branch only)
  -> Fetch open questions
  -> 1. Read the question     what exactly counts as YES; open the resolution source
  -> 2. Prior                 the best starting number we can find
  -> 3. Update                independent forecasters move it only for real evidence
  -> 4. Combine               median of the forecasters; cap extremes
  -> Post forecast + reasoning comment
  -> Log everything
```

## 1. Read the question
Restate the resolution criteria: what counts as YES, the resolution source, the deadline and the edge cases. A fetcher opens the pages the question names as its resolution source. Misreading the question is the cheapest way to lose points.

## 2. Prior
The shared research gathers the best starting number:
1. A prediction market price (Polymarket, Kalshi, Manifold, PredictIt), but only when the market is liquid and its rules match the question. Thin markets count for less. Prateek chose this on 13 November 2026.
2. For numeric questions, the history of the matching data series (FRED, yfinance) as an anchor.
3. Otherwise, a base rate from a reference class, applied only to the time left before the deadline.

Research also comes from AskNews and native web search. Every claim is tagged by source quality, A to D. Two gap-fill passes look for facts the first round missed.

## 3. Update
Several forecasters, from different model families, read the same shared research. Each forecasts on its own. A move of more than about 15 points from a computed number needs a named, specific piece of evidence. This is a rule in the prompt, not a clamp: a hard clamp was tested by nostreambot and hurt.

## 4. Combine
The published forecast is the median of the forecasters. For numeric questions, the median is taken point by point on the full distribution. Binary forecasts are capped at 1% and 99%. Multiple choice gets a small floor on every option.

**Dropped after the evidence:** calibrating the bot's numbers afterwards, and pooling on the odds scale. nostreambot tested both, along with several other methods, and none beat the plain median. The bias flips sign between seasons, so a correction fitted on one season hurts the next.

## What holds it together
**Tests on resolved questions.** Each change is a named variant, run on resolved questions with nothing posted, and kept only if it improves the score. The comparison is reported as a Bayesian estimate: "about an 85% chance v1 is better". Near 50% means the sample is too small to tell.

## Variants to test, in order
1. `v0-nostream`: nostreambot as it is, to get our baseline.
2. `v1-load-bearing`: each forecaster names the claims its forecast rests on. The worst misses come when every model trusts one wrong fact in the shared research. This is nostreambot's best-supported next idea, and close to our keystone idea.
3. `v2-bayes-update`: each forecaster updates in odds, one piece of evidence at a time, with related news grouped first so it is not counted twice.

## Parked ideas
Each gets built as a named variant only if the tests show the problem it fixes.
- Fizzle rate as its own step.
- Evidence grades (partly covered by source tiers).
- A forecaster that argues against the others. nostreambot found advisory critics never moved the number.
- Wider tails on numeric questions.
