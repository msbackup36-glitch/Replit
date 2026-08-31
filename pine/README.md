# TradingView scanners

## `atr_extension_vwap_retest.pine` — current version

**Trend-continuation setup.** The move extends, pulls back *counter-trend* to
VWAP, then resumes in the original direction. An up extension is a LONG setup.

| Stage | What happens | Marker |
|---|---|---|
| 1 — Arm | mcap >= $500M, RVOL >= 1.5x, >= 1.00 ATR from the open, >= 0.75 ATR from VWAP | small circle |
| 2 — Pullback | price returns to VWAP against the trend — **this is the alert** | `VWAP` label + shaded background |
| 3 — Confirmation | price resumes in the direction of the original move | triangle |

Stage 2 is deliberately the alert so there is time to get to the chart and wait
for the resumption. Stage 3 has its own separate alert condition if you would
rather be told when it actually goes.

### Stage 3 confirmation

- **Break retest extreme** (default) — price takes out the high of the pullback
  (low, for shorts) and closes back on the trend side of VWAP.
- **Reclaim VWAP** — looser; just a close back on the trend side.
- `Min bars between pullback and confirmation` (default 1) stops a single wick
  through VWAP from counting as both stages.

### Invalidation

A pullback that keeps going is not a continuation trade. The setup is cancelled
if price closes more than 0.25 ATR through VWAP against the trend, or if the
continuation has not appeared within 12 bars.

### Anchors

- **ATR** — prior completed 14-day ATR, `[1]` offset, fixed all session, no repaint.
- **VWAP** — accumulates from the **pre-market open (04:00)**. Requires Extended
  Hours enabled; the status table shows the live anchor time.
- **Open** — the 09:30 RTH open by default; switchable to the PM open.
- **RVOL** — cumulative volume since the PM open over the average cumulative
  volume at the same slot in the session across the prior 20 sessions. Sessions
  that never reached the slot (half days, holidays) are excluded.

### Timeframe

Intraday only (1m-15m). Raises a runtime error on daily or higher.

## `atr_extension_rth.pine` — superseded

Single-stage version that alerted on the extension itself, RTH-anchored VWAP,
no RVOL gate. Kept for reference.
