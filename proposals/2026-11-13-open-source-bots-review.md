# What the winning open-source bots teach us

**Written 13 November 2026, for Prateek to decide.**
**In short:** The best evidence available is from nostreambot, 15th of 277 in Summer 2026, with its code and a design log of what worked and what failed. It contradicts two parts of our plan: calibration afterwards, and pooling on the odds scale. It supports most of the rest. We could start from its code instead of the plain template.

## The bots read
- **nostreambot** (https://github.com/No-Stream/metaculus-bot). MIT licence, updated 9 November 2026. 15th of 277 in Summer 2026, above the all-bots average. It keeps a design log (`FUTURE.md`) of every idea it tested, including the ones that failed, with sample sizes.
- **Panshul42's bot** (https://github.com/Panshul42/Forecasting_Bot_Q2). Winner of Q2 2025. Five forecasters, some told to reason from the outside view, some from the inside view, combined by weighted average. It uses older models, so it is less relevant now.
- **GreeneiBot2**, winner of Spring 2026, is not open source. I could not read its public overview from here, because the network blocks Metaculus's pages.

## Where the evidence contradicts our plan
1. **Calibration afterwards does not work at our scale.** nostreambot tried isotonic, Platt, shrinkage towards 50% and one-sided shaves. All failed. The bias changes sign from season to season, so a correction fitted on one season hurts the next. Even with 400 questions, there was too little data to tell. Our step 4 should be dropped, except for the caps.
2. **The median beat pooling on the odds scale.** The geometric mean of odds was "a decisive null in every era". Mean, trimmed mean and stacking also lost. Keep the median.
3. **A hard rule against moving from the anchor hurt.** Forecasts that left their anchor scored better than those that stayed. What survives is a soft rule in the prompt: a move of more than about 15 points needs a named, specific piece of evidence. Our "burden of proof on moving" should be this soft form, not a clamp.

## Where the evidence supports our plan
- **Shared research quality matters most.** "The lever is shared-research quality", not each forecaster doing its own research. That is our structure.
- **Market prices as evidence**, with liquidity checks. Forecasters sit close to deep markets and further from thin ones.
- **Data anchors for numeric questions**, from FRED and yfinance.
- **Reading the resolution source itself.** A fetcher opens the pages the question names.
- **Source tiers.** Claims arrive tagged by source quality, A to D. This is our primary-source idea in a working form.
- **Time left before the deadline.** The prompt applies rates only to the time that remains.
- **Reading a search that found nothing.** "We could not find evidence of X" is not "X did not happen".

## New things to adopt
- **The worst misses come when every model agrees on a shared briefing.** One wrong fact in the research misleads all of them at once. Their best-supported next idea is to ask each forecaster which claims its forecast rests on. This is close to our keystone idea.
- **GitHub's scheduler is unreliable.** It fired only 22% of scheduled runs in one fortnight. They trigger the workflow from an outside scheduler (cron-job.org). This matters: a missed run means a missed question.
- **Cost is about $2.60 per question,** not the $1.50 I estimated. At 400 questions that is about $1,040 for a live season, before testing.
- **Testing method:** each change is compared on resolved questions, with confidence intervals, and recorded in the log whether it worked or not.

## What it does not tell us
- Their results come from one bot and its own question history. A null result in their data is evidence, not proof.
- Bayesian evidence-by-evidence updating (our `v2-bayes-update`) is untested there. Splitting forecasts into scenarios is listed as "highest ceiling, weakest evidence".

## The decision for Prateek
1. **Start from nostreambot's code (recommended).** It is MIT-licensed, current and documented, and it already has most of our plan, tested. We would add our variants on top. Risk: it is large (a lot of code, written mostly by AI), so changes need care. The rules allow building on open-source bots, if we credit it in `BOT_DESCRIPTION.md`.
2. **Stay on the plain template** and build our plan, adjusted for the evidence above. Simpler code, but months of work to reach where nostreambot already is.

Whichever you choose, I would update `ARCHITECTURE.md`: drop calibration (keep the caps), keep the median, and soften the anchor rule.
