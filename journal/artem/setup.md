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

## Stage 1: stock profile (before the open) — agreed 2026-09-28

| # | Filter | Setting |
|---|---|---|
| 1.1 | Price | ≥ $2, on the prior regular-session close |
| 1.2 | Market cap | ≥ $250M |
| 1.3 | Average daily dollar volume | ≥ $15M, 20-day average |
| 1.4 | Security type | Common stock or ADR on a US exchange (NYSE, Nasdaq, NYSE American); no OTC or pink sheets; no ETFs for now |
| 1.5 | Volatility | 14-day daily ATR ≥ 2% of price |
| 1.6 | Gap / pre-market activity | Not filtered: price action is the tell |
| 1.7 | Short-sale restriction / borrow | Not handled; 1.2 and 1.3 should remove most hard-to-borrows |

Review later: log good trend stocks that Stage 1 excluded, with the reason, to test the floors.

## Stage 2: first 10 minutes

### Agreed so far

| # | Filter | Setting |
|---|---|---|
| 2.1 | Base bars | 2-minute bars, regular session only. The 10-minute window is bars 1 to 5 (09:30 to 09:40), evaluated once at the 09:40 close. |
| 2.2 | Extreme | Long: the regular-session low of the window is set in bar 1 or 2 (the first 4 minutes), and bars 3 to 5 do not trade more than the tolerance below it. Tolerance = the larger of $0.01 or 1.5% of the 14-day daily ATR. Short: mirror image on the high. Pre-market prices are ignored. |
| 2.3 | Structure | Long: at least 3 of the 4 bar-to-bar comparisons (bar 2 vs 1, 3 vs 2, 4 vs 3, 5 vs 4) make a higher or equal low. Short: higher or equal is replaced by lower or equal highs. Equal means literally equal. Proviso: at most one of the counted comparisons may instead miss by no more than the 2.2 tolerance (the larger of $0.01 or 1.5% of the 14-day ATR). |
| 2.4 | Candle colour | Not a filter. Record the green (long) or red (short) count and test in the backtest. |
| 2.5 | Efficiency ratio | ≥ 0.6, where ER = abs(09:40 close − 09:30 open) ÷ the sum of abs(close-to-close moves) over bars 1 to 5, with bar 1 measured from the 09:30 open. |
| 2.6 | Close location | Long: the 09:40 close is in the top 20% of the 10-minute range. Short: bottom 20%. |
| 2.7 | Close vs open | Long: the 09:40 close is above the 09:30 open. Short: below. The prior close is not used. |
| 2.8 | Range | The 10-minute range (high minus low, bars 1 to 5) is between 0.1 and 0.6 × the 14-day daily ATR. |
| 2.9 | Wicks and bodies | Dropped: shrinking wicks and consistent bodies are not used. |
| 2.10 | Relative volume at time | Volume in bars 1 to 5 ≥ 1.5 × the average volume of the same 09:30–09:40 window over the prior 10 sessions (lookback is a default, adjustable). |
| 2.11 | Dollar volume in the window | ≥ $1M traded in bars 1 to 5. |
| 2.12 | Relative strength vs SPY/QQQ | Not a filter. Record it and test in the backtest. |
| 2.13 | Failed test of pre-market / prior-day extreme | Not a filter. Record it and test in the backtest. |
| 2.14 | Gap size (ATR multiple) | Not a filter. Record it and test in the backtest. |

**Combining the conditions (agreed 2026-09-28):** version 1 uses hard rules only. A stock triggers only if it passes every agreed filter. Every condition's value and pass/fail is still recorded for every Stage 1 stock, triggered or not, so a score can be tested in the backtest later without re-collecting data. Scoring is in the backlog.

All Stage 2 items are now defined.

### As originally drafted

- Timeframe: first 10 minutes, as five 2-minute candles (or ten 1-minute).
- Basic profile: market cap > $250M, price > $1 (superseded by Stage 1).
- Clear direction: LOD (long) or HOD (short) in the first 4 minutes; 4 or 5 of 5 candles green (long) or red (short).
- Trend consistency: 3 or 4 of 4 new higher lows (long) or lower highs (short); even spacing between them; shrinking lower (long) or upper (short) wicks; consistent body size (does it matter?).
- Range: total 10-minute range < X ATR.
- Volume: 10-minute relative volume at time > 1.5x (?); minimum dollar volume over the 10 minutes.

### Draft v0.2 (under discussion, not agreed)

Based on the Raschke analysis and the first 17 journal charts:

- **Extreme:** HOD/LOD set early in the 10-minute window, and not revisited by 09:40.
- **Structure measured from the bar after the extreme, not from 09:30:** most bars make higher lows (long) or lower highs (short), plus an efficiency ratio (net move / sum of absolute bar moves) above a threshold. Candle colour gets little or no weight.
- **Close location:** the latest close in the top (long) or bottom (short) part of the range since the extreme.
- **Range:** between a minimum and a maximum fraction of daily ATR.
- **Score points (optional):** failed test of the pre-market or prior-day extreme; relative volume at time; relative strength against SPY/QQQ; sector peer also flagged.
- **Evaluation:** once, at the 09:40 close, over the first 10 minutes only (agreed 2026-09-28). Delayed starts (CVS, CME, GME 9/25, NDAQ, PRGO, ABT, GME 9/24) are out of scope for this filter; a later "recovery" filter will pick them up.

Decisions still needed before Pine: 2m vs 1m base; hard gates vs a score; chart indicator vs Pine Screener vs multi-symbol scanner; which additions to keep.

## Later: recovery filter (delayed starts)

To be designed. Catches trends that begin about 09:40 to 10:00, after a flush, pop or sideways box.

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

## Backlog

- Test a score in place of hard rules: use the recorded per-condition results to see whether an "N of M conditions" threshold beats all-must-pass.

- Check the journal tickers against Stage 1 (price, market cap, 20-day dollar volume, ATR %) and record any excluded.

- Calibrate the 10-minute thresholds on real bars: export 1-minute CSVs from TradingView (regular hours, the signal day plus about 10 prior days) for the 12 clean drives (9/24: A, ILMN, LLY, EOSE, LRMR, ETSY, FDX, MNST; 9/25: ACAD, FRVO, MGM; 9/23: MCD) and for KO, MSFT, JPM, HD (9/24). Store them in `data/`. Deferred 2026-09-28: tuning will come mainly from live output instead.

## Changelog

- 2026-09-28: spec created from the Artem Trade Setup document; draft v0.2 added for discussion.
- 2026-09-28: scope agreed. The initial filter evaluates the first 10 minutes only, at 09:40. Delayed starts move to a later recovery filter.
- 2026-09-28: Stage 1 profile filters agreed (1.1 to 1.7). 14-day ATR; price is checked on the prior regular-session close.
- 2026-09-28: Stage 2 items 2.1 (2-minute bars) and 2.2 (extreme in the first 4 minutes, regular session only, one-tick tolerance) agreed.
- 2026-09-28: 2.2 tolerance changed from one tick to the larger of $0.01 or 1.5% of the 14-day ATR.
- 2026-09-28: Stage 2 items 2.6, 2.8 to 2.11 agreed; 2.12 to 2.14 recorded as test-only measurements.
- 2026-09-28: version 1 uses hard rules (all filters must pass); all condition results are recorded for later score testing. 10-session RVOL lookback and 2.13 as record-only confirmed.
- 2026-09-28: Stage 2 items 2.3 (3 of 4 higher/lower-or-equal), 2.5 (efficiency ratio ≥ 0.6) and 2.7 (beyond the open only) agreed.
- 2026-09-28: 2.4 candle colour made record-only; 2.3 near-miss proviso added (one comparison may miss by up to the 2.2 tolerance). Stage 2 fully defined.
