# Architecture

```text
ATAS DATA
   |
   +-- Candles / OHLC / sessions
   +-- Volume / VWAP / profile
   +-- Footprint / PriceVolumeInfo
   +-- Trades / cumulative trades
   +-- DOM L2
   +-- MBO L3 (optional)
          |
          v
    MarketDataLayer
          |
    +-----+----------------+
    |                      |
    v                      v
MarketRegimeDetector   OrderFlowEngine
    |                      |
    +----------+-----------+
               v
        MarketLocationEngine
               |
               v
         StrategySelector
               |
               v
           SignalEngine
               |
               v
           RiskManager
               |
               v
          TradeJournal
               |
               v
        ExecutionAdapter
```

## Responsibilities

### MarketDataLayer
Normalize access to bars, trades, footprint, DOM and MBO. Expose capabilities instead of assuming they exist.

### MarketRegimeDetector
Classify context only. It must not place trades.

Candidate inputs:
- structure;
- VWAP slope/location;
- ADX;
- ATR percentile;
- directional efficiency;
- relative volume;
- compression/expansion;
- CVD persistence as context.

### MarketLocationEngine
Identify meaningful areas:
- VWAP/deviations;
- POC;
- VAH/VAL;
- previous high/low;
- range extremes;
- volume zones;
- breakout/retest areas.

### OrderFlowEngine
Combine:
- footprint;
- Bid/Ask;
- delta/CVD;
- diagonal/stacked imbalance;
- absorption candidates;
- exhaustion;
- trades;
- optional DOM/MBO evidence.

### StrategySelector
Enable only strategy families compatible with the active regime.

### SignalEngine
Return evidence-based states such as:
- `BUY_CANDIDATE`
- `SELL_CANDIDATE`
- `WAIT`

### RiskManager
Independent from regime and signal logic.

### TradeJournal
Persist decisions and future outcomes for statistical analysis.

### ExecutionAdapter
Late phase only. Evaluate native ATAS `ChartStrategy` first.

## Regime stability

Use hysteresis and transition states. Avoid changing regime on a single marginal candle.

Example:

```text
RANGE
  -> COMPRESSION
  -> BREAKOUT_CANDIDATE
  -> TRANSITION
  -> TREND_UP / TREND_DOWN
```

## Performance

Real-time ATAS callbacks must remain allocation-light and non-blocking.
