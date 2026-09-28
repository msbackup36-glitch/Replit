# Artem Trade: setup spec

Living document. Change it only by agreement, and log every change in the changelog at the bottom.

## Definition (the outcome we want to catch)

- Open and close within 25% of the high/low of day (HOD/LOD).
- Low intraday volatility with a high intraday range, i.e. a consistent grind.
- Stocks, ETFs and indices (QQQ, SPY, IWM).
- Catalyst doesn't matter; price action is the tell.
- Market trend days are included for now. Separate criteria to come.
- Open: relative volume and ATR multiple thresholds.

Journal observation: the open-side condition holds on every example so far. The close side fails on
about half, because many trends ran out by late morning. Consider tracking a looser
"open within 25%" label as well.

## Initial filter: first 10 minutes (as originally drafted)

- Timeframe: first 10 minutes, as five 2-minute candles (or ten 1-minute).
- Basic profile: market cap > $250M, price > $1.
- Clear direction: LOD (long) or HOD (short) in the first 4 minutes; 4 or 5 of 5 candles green (long) or red (short).
- Trend consistency: 3 or 4 of 4 new higher lows (long) or lower highs (short); even spacing between them; shrinking lower (long) or upper (short) wicks; consistent body size (does it matter?).
- Range: total 10-minute range < X ATR.
- Volume: 10-minute relative volume at time > 1.5x (?); minimum dollar volume over the 10 minutes.

## Draft v0.2 (under discussion, not agreed)

Based on the Raschke analysis and the first 17 journal charts:

- **Extreme:** HOD/LOD set within roughly the first 15 minutes, and not revisited since.
- **Structure measured from the bar after the extreme, not from 09:30:** most bars make higher lows (long) or lower highs (short), plus an efficiency ratio (net move / sum of absolute bar moves) above a threshold. Candle colour gets little or no weight.
- **Close location:** the latest close in the top (long) or bottom (short) part of the range since the extreme.
- **Range:** between a minimum and a maximum fraction of daily ATR.
- **Score points (optional):** failed test of the pre-market or prior-day extreme; relative volume at time; relative strength against SPY/QQQ; sector peer also flagged.
- **Evaluation:** on each 2-minute close from 09:40 to about 10:00; record the first qualifying time. This catches delayed starts (CVS, CME, GME 9/25, NDAQ, PRGO, ABT).
- **Profile filters** (market cap, price) in the TradingView screener rather than in Pine.

Decisions still needed before Pine: 2m vs 1m base; hard gates vs a score; chart indicator vs Pine Screener vs multi-symbol scanner; which additions to keep.

## Next filter (first 30 minutes)

- Above (long) or below (short) the 13 EMA, but not stretched from it: shallow pullbacks, not parabolic.
- 3+ five-minute candles in the trend direction in the first 30 minutes.
- Outside the pre-market and prior-day range: does it matter? Track it as a measurement first.

## For consideration / backtest list

- Expected time of HOD/LOD. Typical number of EMA pullbacks before HOD/LOD and/or the first VWAP pullback.
- VWAP pullbacks and touches; ideal maximum ATR-multiple extension from VWAP.
- Typical range contraction over the day for trend stocks.
- False-signal library (the `false_signal` entries in this journal).
- Midday relative volume as a tell for an all-day trend vs a fade into chop.
- Whether prior-day and pre-market trading say anything about trend-day odds.
- Adding and recycling during the day.
- Day 2: pullback to Day 1 key levels; Raschke's pullback to the 20 EMA on the 15-minute chart (if not touched on Day 1); AVWAP; MACD (Raschke uses the 3/10/16 oscillator).

## Trade management

- No buy before 09:40. What extra confirmation? First higher low / lower high on the 1-minute chart?
- Trailing stop at the prior 2-minute (or 1-minute) low/high. Journal note: that may be tight for a grind; test against trailing below the 13 EMA or the prior 5-minute low.

## Changelog

- 2026-09-28: spec created from the Artem Trade Setup document; draft v0.2 added for discussion.
