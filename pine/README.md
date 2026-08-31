# TradingView scanners

## `atr_extension_vwap_retest.pine` — current version

Two-stage setup.

**Stage 1 — arm** (regular trading hours only):

| Gate | Default |
|---|---|
| Market cap | >= $500M |
| RVOL, time-of-day, anchored at the pre-market open | >= 1.5x |
| Extension from the opening price | >= 1.00 daily ATR |
| Extension from VWAP | >= 0.75 daily ATR |

**Stage 2 — trigger (the alert):** price returns to VWAP, still within RTH.

### Anchors

- **ATR** — prior completed 14-day ATR, fetched with a `[1]` offset so it is
  fixed for the whole session and never repaints.
- **VWAP** — accumulates from the **pre-market open (04:00)**.
  Requires Extended Hours enabled on the chart. The status table shows the live
  anchor time so you can confirm you are getting 04:00 and not 09:30.
- **Open** — the **09:30 RTH open** by default; switchable to the PM open.
- **RVOL** — cumulative volume since the PM open divided by the average
  cumulative volume at the same slot within the session over the prior 20
  sessions. Sessions that never reached that slot (half days, holidays) are
  excluded rather than counted as zero.

### Timeframe

Intraday only (1m-15m). The script raises a runtime error on daily or higher,
because session VWAP collapses to that bar's own average price.

### Reading the chart

- Small hollow circle — armed (stage 1 conditions met).
- `VWAP` label — the retest fired. This is the alert.
- Teal bands: open +/- 1 ATR. Purple bands: VWAP +/- 0.75 ATR.

## `atr_extension_rth.pine` — superseded

Single-stage version that alerts on the extension itself, with an RTH-anchored
VWAP and no RVOL gate. Kept for reference.
