# Project Context

## Objective

Build an adaptive ATAS-based trading system that analyzes the market environment before deciding which strategy, if any, may operate.

The system must not behave as:

`footprint signal -> automatic trade`

Preferred flow:

`market regime -> market location -> setup -> order-flow confirmation -> risk gate -> decision`

## Core market states

- `TREND_UP`
- `TREND_DOWN`
- `RANGE`
- `COMPRESSION`
- `BREAKOUT_CANDIDATE`
- `TRANSITION`
- `HIGH_VOLATILITY`
- `UNKNOWN`

## Initial strategy families

- Trend pullback / continuation
- Range reversion
- Breakout acceptance / retest
- No-trade

## Validation philosophy

Every hypothesis must be journaled and later evaluated.

Record at least:
- timestamp;
- instrument;
- active data capabilities;
- regime;
- regime score/confidence;
- location;
- strategy selected;
- order-flow evidence;
- decision;
- invalidation;
- simulated entry/exit;
- MFE;
- MAE;
- costs/slippage when available;
- outcome.

The aim is empirical validation, including out-of-sample behavior.

## Data candidates

- OHLC
- volume
- VWAP
- profile / POC / VAH / VAL
- Bid / Ask
- Delta
- CVD
- footprint by price level
- stacked imbalances
- relative volume
- ATR / volatility
- trend-strength measures
- L2 DOM
- MBO/L3 when available
- open interest when supported

## Repositories

Product repository:

- `avillavicenciom/ATAS-Adaptive-Trader`

ATAS technical reference:

- `avillavicenciom/atas-Indicators`

The reference repository is not the product codebase.

## NotebookLM

Notebook: **Integración ATAS MenthorQ**

ID:

`32f0118c-cf5e-47d8-afbb-debc4ef93374`

Codex should use the NotebookLM MCP to query it when validating ATAS behavior, APIs and development decisions.

## Initial rollout

Begin with ATAS Start/free where sufficient for development and supported live crypto data.

Re-evaluate paid plans when futures real-time data, replay, premium MBO features or execution become necessary.
