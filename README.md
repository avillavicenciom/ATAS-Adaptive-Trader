# ATAS Adaptive Trader

Adaptive trading research and development project for ATAS.

The project is designed around a modular decision pipeline:

```text
ATAS market data
    ↓
MarketDataLayer
    ↓
MarketRegimeDetector
    ↓
MarketLocationEngine
    ↓
StrategySelector
    ↓
OrderFlowEngine
    ↓
SignalEngine
    ↓
RiskManager
    ↓
TradeJournal
    ↓
ExecutionAdapter
```

## Current status

Research/bootstrap. No real-money execution.

The first development goals are:

1. Verify ATAS SDK capabilities and local build.
2. Implement market-data capability discovery.
3. Implement the market-regime detector.
4. Add order-flow confirmation only after context classification is stable.

## Knowledge sources

Priority:

1. Official ATAS SDK documentation: https://docs.atas.net/en/index.html
2. Official ATAS Help Center: https://help.atas.net/es/support/home
3. ATAS source/reference repository: https://github.com/avillavicenciom/atas-Indicators
4. NotebookLM: **Integración ATAS MenthorQ**
   - ID: `32f0118c-cf5e-47d8-afbb-debc4ef93374`

See `AGENTS.md` and `docs/` before making implementation changes.
