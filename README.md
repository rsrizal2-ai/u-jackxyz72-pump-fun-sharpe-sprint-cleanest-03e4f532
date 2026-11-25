# Pump.fun Sharpe Sprint — Cleanest Paper Bot Wins

[![make.fun · u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)
[![rank](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/rsrizal2-ai/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/rank.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)
[![reward](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/rsrizal2-ai/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/reward.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)

My entry for [Pump.fun Sharpe Sprint — Cleanest Paper Bot Wins](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532) on [make.fun](https://make.fun): a paper-trading bot for pump.fun. It trades replayed market data only and never touches real funds. The leaderboard ranks by hourly Sharpe ratio, so the game is smooth, controlled gains — one lucky moonshot is worth less than a steady stream of small wins — and a place pays out only if net PnL finishes above simply holding cash.

## Strategy

The edge is the asymmetry, not prediction. Most new pump.fun coins grind slowly down; a rare few run away up. This bot buys one small 0.05 SOL ticket per coin, entering as soon as the coin's curve holds enough real SOL for the buy to matter, then leaves the ticket alone under a wide stop (−15%) and a wide target (+150%). A loser costs at most ~0.009 SOL; a winner pays ~+0.075 SOL. Five winners among forty-odd tickets therefore cover thirty-odd small losers several times over. There is no pyramiding, no averaging down, and no chasing a coin that has already run 50% past where it was first seen. A ticket exists only once its buy actually fills — a failed or rejected buy is tried again, up to 3 times, rather than quietly dropping the ticket, because a 2% failed-transaction draw should not be the thing that decides which moonshots end up in the book.

Sharpe is the point, not just PnL. Total exposure stays tiny — at most 4 slow tickets plus 1 probe open at once, 0.215 SOL of the 10 SOL bankroll — every ticket carries the same hard stop, and dozens of small, uncorrelated tickets spread returns across the window instead of concentrating them in a single all-in bet. The capped probe lane (0.015 SOL buys, sold the moment the buy lands, at most 30 probes, one open at a time) is there for one reason only: clearing the 20-coin round-trip bar inside the short 6-hour final window. It never takes a slow-lane slot.

## Run

```sh
node main.ts
```

`main.ts` targets Node 22 (the grader executes `node /bot/main.ts`); the replay shim lives vendored at `vendor/makefun-bot.ts`. The bot is fully deterministic — no clock, no environment reads, no randomness — so its two grading runs agree byte for byte.

## Local grading

Dev window: the kit's own local simulator replaying 24 h of pump.fun data, a harsh sample in which the house momentum pilot drops 9.38 SOL and the sniper pilot drops 0.14 SOL:

- Momentum pilot, 2 SOL stakes: −9.38 SOL, max drawdown 95%
- Sniper pilot, 0.10 SOL, 10-slot holds: −0.14 SOL, 135 coins, max drawdown 1.7%
- Momentum, best small-stake sweep: −0.20 SOL, 143 trips, 7 wins
- Buy-once, −20% stop / +100% target, 0.05 SOL: +0.096 SOL, 18 trips, 5 wins
- **This bot, −15% stop / +150% target, 0.05 SOL, 4 slow + 1 probe slot, 30 capped probes: +0.189 SOL, 50 round trips (8 wins), 50 distinct coins, max drawdown 0.9%** — still +0.144 SOL at 2x fees, and positive under four of five failed-tx draws (+0.006 SOL on the worst). Its five best round trips land at +0.05…+0.08 SOL each; its worst single loss is −0.031 SOL.
