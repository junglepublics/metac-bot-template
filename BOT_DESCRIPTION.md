# Bot description

This bot is built on **nostreambot** by No-Stream (https://github.com/No-Stream/metaculus-bot), used under its MIT licence (see `LICENSE`). nostreambot was itself forked from the Metaculus bot template (https://github.com/Metaculus/metac-bot-template) and uses the forecasting-tools package. The code was copied from nostreambot at commit `8bc3df0` (9 November 2026).

How the bot works: for each question, it gathers shared research (AskNews, web search, prediction-market prices, economic data, and the question's own resolution source). It asks several language models from different families to forecast on their own. It publishes the median, with a comment showing each model's reasoning. See `ARCHITECTURE.md` for the plain-language view and `docs/architecture.md` for the code.

Our changes are tested as named variants on resolved questions and listed in `ARCHITECTURE.md`.
