# Development Plan

## Phase 0 — SDK and capability discovery
- verify local ATAS build;
- document exact APIs;
- implement capability detection;
- create a diagnostic indicator/tool;
- prove access to bars, footprint and real-time events.

Exit:
- no invented APIs;
- actual feed capabilities visible.

## Phase 1 — MarketDataLayer
- normalized candle snapshot;
- session information;
- footprint-level snapshot;
- normalized trade events;
- optional DOM/MBO adapters behind interfaces.

## Phase 2 — MarketRegimeDetector
States:
- `TREND_UP`
- `TREND_DOWN`
- `RANGE`
- `COMPRESSION`
- `BREAKOUT_CANDIDATE`
- `TRANSITION`
- `HIGH_VOLATILITY`
- `UNKNOWN`

Initial features:
- structure;
- VWAP slope/location;
- ADX;
- ATR percentile;
- relative volume;
- compression/expansion.

Exit:
- stable transitions;
- debug output;
- regime journal.

## Phase 3 — MarketLocationEngine
- VWAP/deviations;
- POC/value areas where available;
- prior high/low;
- range boundaries;
- volume support/resistance zones.

## Phase 4 — OrderFlowEngine
- diagonal imbalance;
- stacked imbalance;
- delta/CVD;
- cluster concentration;
- absorption candidates;
- exhaustion candidates.

Output evidence objects, not trades.

## Phase 5 — StrategySelector
- trend -> pullback/continuation;
- range -> edge reversion;
- breakout -> acceptance/retest;
- compression/unknown/high-volatility -> mostly no-trade.

## Phase 6 — SignalEngine + risk model
Combine regime, location, strategy, order flow, invalidation and execution cost.

No real execution.

## Phase 7 — TradeJournal and validation
Capture:
- inputs;
- capabilities;
- regime;
- strategy;
- evidence;
- decision;
- MFE/MAE;
- result;
- costs.

Evaluate:
- sample size;
- hit rate;
- expectancy;
- drawdown;
- performance by regime/setup;
- out-of-sample behavior.

## Phase 8 — Paper/simulation

## Phase 9 — Execution architecture
Evaluate ATAS `ChartStrategy` first.

Must handle:
- registration;
- partial fills;
- rejection;
- modify/cancel;
- disconnect/reconnect;
- duplicate signals;
- max loss/exposure;
- emergency stop.

## Phase 10 — Real execution
Only after explicit review of simulation evidence and risk controls.
