# Data Capabilities

## Goal

Detect what the active instrument/feed actually provides and adapt safely.

## Suggested flags

```text
HasCandles
HasVolume
HasBidAsk
HasFootprintLevels
HasTrades
HasCumulativeTrades
HasDomL2
HasMboL3
HasOpenInterest
HasReplay
CanSendOrders
```

## Degradation hierarchy

### Full
MBO/L3 + L2 + trades + footprint + bars.

### L2
L2 + trades + footprint + bars.

Disable L3-only features.

### Footprint
Trades/footprint + bars.

No DOM assumptions.

### Bars only
OHLC/volume.

Regime analysis may remain possible; order-flow strategies should normally be disabled.

### Insufficient
Return `NO_TRADE`.

## Availability may vary by

- instrument;
- exchange;
- connector;
- data package;
- ATAS tariff;
- replay/live context.

Never fabricate missing data.

## Initial scope

V1:
- bars;
- volume;
- VWAP;
- ATR;
- ADX;
- relative volume;
- footprint Bid/Ask/Delta where available.

V2:
- L2 DOM.

V3:
- MBO/L3.

## Journal requirement

Record active capabilities with each signal so later statistics can distinguish feed/data quality.
