# ATR Extension — RTH

A TradingView (Pine v6) indicator that flags stocks stretched a minimum number of
**daily ATRs** away from both the regular-session **opening price** and the
regular-session **VWAP**, long or short, above a market-cap floor.

## Requirements implemented

| # | Requirement | How |
|---|---|---|
| 1 | RTH only | `session.ismarket`, or a custom `0930-1600` session string |
| 2 | Min $500M market cap | `TOTAL_SHARES_OUTSTANDING` (FQ) x price |
| 3 | Long or short | Symmetric conditions, with a direction selector |
| 4 | Min 1 ATR from the open | `(close - rthOpen) / dailyATR` |
| 5 | Min 0.75 ATR from VWAP | `(close - rthVwap) / dailyATR` |

## Design decisions

- **ATR = previous completed daily ATR(14)**, fetched with a `[1]` offset so the
  value is fixed for the whole session and never repaints. Intraday ATR would
  produce a far smaller, constantly-moving yardstick.
- **VWAP is anchored to the 09:30 RTH open**, not to the extended-hours session
  start, so the number matches what a day trader reads off an RTH chart.
- **Direction is consistent**: a long signal needs price above *both* references;
  a short needs it below both.
- **One signal per session** by default, so alerts do not repeat all afternoon.

## Timeframe

Must be run **intraday** (1m–15m). Session VWAP resets every day, so on a daily
or higher chart it collapses to that bar's own average price and requirement 5
becomes meaningless.

## Usage

1. Pine Editor -> paste `atr_extension_rth.pine` -> Save -> Add to chart.
2. Set the chart to 5m and confirm the status table populates.
3. Right-click the plot -> Add alert, condition = the indicator.

For scanning, prefilter with the built-in Screener (Market cap >= 500M, average
volume) and save the result as a watchlist, then run this indicator over it.
