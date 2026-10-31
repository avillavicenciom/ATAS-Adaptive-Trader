# AGENTS.md — ATAS Adaptive Trader

## Purpose

This repository contains our own adaptive trading system for ATAS.

Do **not** treat this repository as a fork of ATAS indicators. The ATAS indicator repository is an external technical reference:

- https://github.com/avillavicenciom/atas-Indicators

The objective is not a single BUY/SELL indicator. The engine must first classify market regime, identify location/context, enable only compatible strategies, validate with order flow, apply risk rules, and only later consider execution.

## Source priority

Before implementing any ATAS-specific behavior, use this order:

1. Official ATAS SDK documentation  
   https://docs.atas.net/en/index.html
2. Official ATAS Help Center  
   https://help.atas.net/es/support/home
3. Reference code repository  
   https://github.com/avillavicenciom/atas-Indicators
4. NotebookLM notebook **Integración ATAS MenthorQ**
   - ID: `32f0118c-cf5e-47d8-afbb-debc4ef93374`
   - Access through the configured NotebookLM MCP
5. Community sources only as secondary references

If sources disagree, do not guess. Record the discrepancy and prefer the highest-priority current official source.

## Mandatory Codex rules

- Do not invent ATAS classes, methods, events, properties, enum values or order APIs.
- Verify every ATAS API in official docs or current ATAS reference code before using it.
- Use `avillavicenciom/atas-Indicators` to study implementation patterns, not as the product codebase.
- Do not blindly copy whole ATAS classes.
- Keep market data, regime detection, market location, strategy selection, order flow, risk and execution decoupled.
- `WAIT` / `NO_TRADE` is a valid system decision.
- No real-money execution during early development.
- Prefer relative/adaptive thresholds over instrument-specific fixed values.
- A single delta divergence, imbalance or footprint pattern is never sufficient proof of reversal.
- MBO/L3 must degrade safely to L2, footprint, bar-only or `NO_TRADE`.
- Avoid expensive allocations/LINQ in hot ATAS callbacks unless measured and justified.
- Do not silently fabricate missing market data.

## Read before coding

- `docs/PROJECT_CONTEXT.md`
- `docs/ARCHITECTURE.md`
- `docs/ATAS_SDK_NOTES.md`
- `docs/MARKET_REGIMES.md`
- `docs/ORDER_FLOW.md`
- `docs/DATA_CAPABILITIES.md`
- `docs/DEVELOPMENT_PLAN.md`
- `docs/DECISIONS.md`

## Intended modules

- `MarketDataLayer`
- `MarketDataCapabilities`
- `MarketRegimeDetector`
- `MarketLocationEngine`
- `FootprintAnalyzer`
- `DeltaCvdAnalyzer`
- `LiquidityAnalyzer` (later)
- `StrategySelector`
- `SignalEngine`
- `RiskManager`
- `TradeJournal`
- `ExecutionAdapter` / ATAS `ChartStrategy` layer (late phase)

## Current phase

Research/bootstrap.

Next milestone:

1. SDK/capability discovery.
2. Market-data abstraction.
3. Market-regime detector.
4. Validation/journaling.
5. Order-flow confirmation.

## ATAS reference files worth studying

In `avillavicenciom/atas-Indicators`:

- `Technical/StackedImbalance.cs`
- `Technical/ClusterSearch.cs`
- `Technical/Delta.cs`
- `Technical/CumulativeDelta.cs`
- `Technical/MarketPower.cs`
- `Technical/VWAP.cs`
- `Technical/RelativeVolume.cs`
- `Technical/ADX.cs`
- `Technical/ATR.cs`
- `Technical/BollingerSqueezeV3.cs`
- `Technical/VolumeSupResZones.cs`
- `Technical/DOM.cs`
- `Technical/DomV3/MainIndicator.cs`
- `Technical/DomV3/MboGridController.cs`

## Start-of-task protocol

For substantial tasks:

1. Read this file and the relevant project docs.
2. Query NotebookLM when ATAS behavior or SDK details are involved.
3. Confirm the exact API in official ATAS docs.
4. Search the ATAS reference repo for a known-good implementation.
5. State data/feed prerequisites.
6. Implement the smallest testable unit.
7. Add validation/logging before moving closer to execution.

## Safety boundary

Early development must remain analysis-only, alert-only or paper/simulation mode.

Any future live execution requires explicit:
- max loss/exposure limits;
- order-state handling;
- partial-fill handling;
- rejection handling;
- disconnect/reconnect behavior;
- duplicate-signal protection;
- emergency stop;
- post-trade audit logging.
