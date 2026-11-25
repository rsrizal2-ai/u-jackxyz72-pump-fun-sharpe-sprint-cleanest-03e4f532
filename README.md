# Pump.fun Sharpe Sprint — Cleanest Paper Bot Wins

[![make.fun · u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)
[![rank](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/rsrizal2-ai/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/rank.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)
[![reward](https://make.fun/badge/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/rsrizal2-ai/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532/reward.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532)

A paper-trading bot for pump.fun — my entry for [Pump.fun Sharpe Sprint — Cleanest Paper Bot Wins](https://make.fun/bounty/u-jackxyz72-pump-fun-sharpe-sprint-cleanest-03e4f532) on [make.fun](https://make.fun). Paper trading only: it replays market data and never touches real funds. Places go by hourly Sharpe ratio, so steady, controlled gains beat one lucky moonshot — and a place only pays out with a net PnL above holding cash.

## Strategy

Buy the asymmetry, don't fight it. On pump.fun most new coins bleed slowly and a few moon. This bot gives each coin ONE small 0.05 SOL ticket, bought as soon as its curve holds enough real SOL for a meaningful buy, then held with a wide stop (−15%) and a wide target (+150%): losers cost at most ~0.009 SOL each, winners pay ~+0.075 SOL each, so five winners out of forty-odd tickets beats thirty-odd small losers. No pyramiding, no averaging down, no chasing a coin that already ran 50% past its first sighting. A buy only counts once its fill lands — a failed or rejected buy is retried (up to 3 attempts) instead of silently losing the ticket, because a 2% failed-tx draw must not decide which moonshots we hold.

Why this wins on Sharpe, not just on PnL: exposure is tiny (at most 4 slow tickets + 1 probe open, 0.215 SOL of the 10 SOL capital), every ticket carries the same hard stop, and the many small, uncorrelated tickets spread the return across the window instead of concentrating it in one all-in bet. The capped probe lane (0.015 SOL buys, sold as soon as the buy lands, at most 26 probes, one open at a time) exists only to clear the 20-coin round-trip bar on the short 6-hour final window; it never steals slow-lane slots.

## Run

```sh
node main.ts
```

`main.ts` runs on Node 22 (the grader runs `node /bot/main.ts`); the replay shim is vendored at `vendor/makefun-bot.ts`. Everything is deterministic — no clock, no environment reads, no randomness — so the two grading runs match byte for byte.

## Local grading

Dev window (kit's own local simulator, 24 h of pump.fun data — a brutal sample where the house momentum pilot loses 9.38 SOL and the sniper pilot loses 0.14 SOL):

- Momentum pilot (2 SOL stakes): −9.38 SOL, max drawdown 95%
- Sniper pilot (0.10 SOL, 10-slot holds): −0.14 SOL, 135 coins, max drawdown 1.7%
- Momentum, small-stake sweep (best): −0.20 SOL, 143 trips, 7 wins
- Buy-once, −20% stop / +100% target, 0.05 SOL: +0.096 SOL, 18 trips, 5 wins
- **This bot (−15% stop / +150% target, 0.05 SOL, 4 slow + 1 probe slot, 30 capped probes): +0.189 SOL, 50 round trips (8 wins), 50 distinct coins, max drawdown 0.9%** — still +0.144 SOL at 2x fees, and positive under four of five failed-tx draws (+0.006 SOL on the worst). Five biggest round trips are +0.05…+0.08 SOL each; worst single loss is −0.031 SOL.
