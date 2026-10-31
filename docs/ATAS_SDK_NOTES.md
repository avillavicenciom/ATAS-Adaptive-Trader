# ATAS SDK Notes

Re-verify exact API signatures against current official documentation before implementation.

## Candle access

Common pattern:

```csharp
var candle = GetCandle(bar);
```

Relevant candle values include:
- Open
- High
- Low
- Close
- Volume
- Bid
- Ask
- Delta
- MinDelta
- MaxDelta
- OI
- VWAP where applicable

## Footprint / price levels

Reference implementation in `avillavicenciom/atas-Indicators` confirms:

```csharp
var info = candle.GetPriceVolumeInfo(price);
```

Useful price-level values include:
- Bid
- Ask
- Volume

Price-level delta can be derived from Ask - Bid where appropriate.

Primary references:
- `Technical/StackedImbalance.cs`
- `Technical/ClusterSearch.cs`

## Stacked imbalance

`Technical/StackedImbalance.cs` scans levels using `InstrumentInfo.TickSize` and compares adjacent levels diagonally. Use it as the main implementation reference for our imbalance detector.

## Delta / CVD

References:
- `Technical/Delta.cs`
- `Technical/CumulativeDelta.cs`
- `Technical/MarketPower.cs`

These demonstrate candle delta, Min/Max Delta, session CVD and event-driven trade processing.

## Real-time trade events

Current ATAS reference code demonstrates:
- `OnNewTrade(MarketDataArg trade)`
- `OnNewTrades(IEnumerable<MarketDataArg> trades)`
- `OnCumulativeTrade(CumulativeTrade trade)`
- `OnUpdateCumulativeTrade(CumulativeTrade trade)`
- `RequestForCumulativeTrades(...)`

Prefer event-driven real-time flow where appropriate instead of placing all logic in `OnCalculate`.

## Sessions

Common helpers:
- `IsNewSession(bar)`
- `IsNewWeek(bar)`
- `IsNewMonth(bar)`

## VWAP

Reference:
- `Technical/VWAP.cs`

## DOM / L2

Reference:
- `Technical/DOM.cs`

Useful concepts include:
- depth snapshot/update;
- stacked volume;
- pulled volume;
- net liquidity change.

## MBO / L3

References:
- `Technical/DomV3/MainIndicator.cs`
- `Technical/DomV3/MboGridController.cs`

Current reference code demonstrates MBO subscription, `MarketByOrder`, order IDs/priority and L3-to-L2 fallback.

MBO availability depends on feed/connector/tariff.

## Strategy / execution

ATAS documentation includes `ChartStrategy` and order-management APIs. Re-verify exact asynchronous methods and callbacks before implementing execution.

Execution work must verify:
- open/modify/cancel;
- order-change callbacks;
- registration/modify/cancel failures;
- current position;
- fills/partial fills;
- reconnect behavior;
- simulation vs real account behavior.

## Build

The current ATAS reference repository uses .NET 10-era project files. Verify the installed local ATAS version before fixing target frameworks.

## Performance

Avoid unnecessary allocations, repeated full scans and expensive LINQ in hot handlers.
