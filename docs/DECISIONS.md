# Decisions Log

## Adaptive architecture
Build a regime-based adaptive system instead of a single footprint-bounce indicator.

## Footprint role
Footprint/order flow confirms regime/location context; it does not define the whole market state.

## Zones over exact reversal prices
Use short tick-bounded zones instead of false exact-price precision.

## Statistical validation
Journal hypotheses and later outcomes. Require empirical/out-of-sample evaluation.

## ATAS reference code
Use `avillavicenciom/atas-Indicators` as the technical reference repository, not as the product repository.

Priority files:
- StackedImbalance
- ClusterSearch
- Delta / CumulativeDelta / MarketPower
- VWAP
- RelativeVolume
- ADX / ATR
- DOM / DomV3

## Execution separation
No real execution during early phases.

## ChartStrategy before external bridge
When execution work begins, evaluate native ATAS strategy APIs before introducing NinjaTrader or another bridge.

## Capability degradation
`MBO -> L2 -> footprint -> bars -> NO_TRADE`

## Relative thresholds
Prefer percentiles, z-scores, relative volume and instrument-aware normalization over fixed absolute thresholds.

## Regime hysteresis
Use transition states and different enter/exit thresholds.

## Initial ATAS tier
Begin with ATAS Start/free where sufficient; re-evaluate paid plans when real-time futures, replay, MBO or execution are required.

## Codex context
Persist durable context in `AGENTS.md` and `docs/`; use NotebookLM MCP for curated research.

Notebook:
**Integración ATAS MenthorQ**

ID:
`32f0118c-cf5e-47d8-afbb-debc4ef93374`
