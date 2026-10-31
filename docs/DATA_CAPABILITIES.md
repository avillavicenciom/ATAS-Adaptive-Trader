# Data Capabilities

## Goal

Detect what the active instrument and feed actually provide. Evidence labels are defined in [ATAS_SDK_NOTES.md](ATAS_SDK_NOTES.md). Each flag is a **proposed project flag**, not an ATAS API property. A label identifies evidence for the underlying data path; it does not certify availability on a particular installation.

## Proposed flags

| Flag | Evidence | Condition for availability |
| --- | --- | --- |
| `HasCandles` | `OFFICIAL_DOCS` | Valid candles returned by `GetCandle(bar)`. [Official guide](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html) |
| `HasVolume` | `OFFICIAL_DOCS` | Usable `IndicatorCandle.Volume` and enough complete bars for the intended feature. [Official guide](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html) |
| `HasBidAsk` | `MULTI_SOURCE_CONFIRMED` | Bid/Ask candle or best-quote values actually populated, with the origin identified. [Official guide](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html), [DomV3/MainIndicator.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MainIndicator.cs) |
| `HasFootprintLevels` | `MULTI_SOURCE_CONFIRMED` | Executed volume by price is available, distinct from resting DOM size. [Performance guide](https://docs.atas.net/en/chart-indicators/performance.html), [ClusterSearch.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ClusterSearch.cs) |
| `HasTrades` | `MULTI_SOURCE_CONFIRMED` | Individual `OnNewTrade` callbacks or `OnNewTrades` collections observed for the active instrument/mode; normalize their content without assuming the callback paths are disjoint. [Official ExtendedIndicator API](https://docs.atas.net/api/ATAS.Indicators.ExtendedIndicator.html), [MarketPower.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/MarketPower.cs) |
| `HasCumulativeTrades` | `MULTI_SOURCE_CONFIRMED` | Cumulative trade data received; track `OnCumulativeTradesResponse` for requests and the `OnCumulativeTrade`/`OnUpdateCumulativeTrade` callbacks separately. [Official ExtendedIndicator API](https://docs.atas.net/api/ATAS.Indicators.ExtendedIndicator.html), [MarketPower.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/MarketPower.cs) |
| `HasDomL2` | `MULTI_SOURCE_CONFIRMED` | Nonempty current depth snapshot and sufficiently current level updates. [Depth provider](https://docs.atas.net/api/ATAS.Indicators.IMarketDepthInfoProvider.html), [DOM.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DOM.cs) |
| `HasMboL3` | `MULTI_SOURCE_CONFIRMED` | Real order-level snapshot or updates with usable IDs. Subscription completion or an L2 synthetic display item is insufficient. [ExtendedIndicator API](https://docs.atas.net/api/ATAS.Indicators.ExtendedIndicator.html), [MboGridController.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MboGridController.cs), [tariffs](https://docs.atas.net/en/common-api/tariffs.html) |
| `HasOpenInterest` | `OFFICIAL_DOCS` | Open-interest candle data actually supplied. Existence of the field does not prove feed coverage. [Official guide](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html) |
| `HasReplay` | `OFFICIAL_DOCS` | Replay mode and the required data are actually present. Do not equate a live MBO subscription with past MBO history. [Official lifecycle](https://docs.atas.net/en/chart-indicators/lifecycle.html), [MBO help](https://help.atas.net/en/support/solutions/articles/72000660331-mbo-indicators-icebergs-stops-sweeps) |
| `CanSendOrders` | `OFFICIAL_DOCS` | **Pending execution-phase validation.** `ChartStrategy` exists; account, permissions, connector and safe order handling need separate checks. [ChartStrategy API](https://docs.atas.net/api/ATAS.Strategies.Chart.ChartStrategy.html) |

Absence of updates in a quiet market is inconclusive. Record `unknown`, `waiting`, `available`, `stale` or `unavailable` with the reason, and require positive observations before marking a data flag available. `OFFICIAL_DOCS`: [API tariffs](https://docs.atas.net/en/common-api/tariffs.html), [MBO indicator help](https://help.atas.net/en/support/solutions/articles/72000660331-mbo-indicators-icebergs-stops-sweeps).

## Feed and version prerequisites

| Constraint | Evidence | Consequence |
| --- | --- | --- |
| Real-time quotes, order book and trades require an external data source. | `OFFICIAL_DOCS` | Inspect instrument and connection. [Online data help](https://help.atas.net/en/support/solutions/articles/72000649936-online-market-data-and-data-feed-in-atas) |
| DOM depth may be limited by feed and visible-level settings. | `OFFICIAL_DOCS` | L2 totals and changes cover only received levels. [Common settings](https://help.atas.net/es/support/solutions/articles/72000602625-ajustes-comunes) |
| The documented Rithmic MBO setup needs the relevant data subscription and nonaggregated quotes. | `OFFICIAL_DOCS` | Diagnose connection settings before declaring MBO absent. [Rithmic help](https://help.atas.net/en/support/solutions/articles/72000602593) |
| A tariff can restrict third-party MBO while subscription still completes. | `OFFICIAL_DOCS` | Empty `MarketByOrders` and no callbacks can reflect tariff or feed restriction. [API tariffs](https://docs.atas.net/en/common-api/tariffs.html) |
| The SDK website can describe development-branch members ahead of the installed release. | `OFFICIAL_DOCS` | Check installed DLLs before using version-marked members. [API reference](https://docs.atas.net/api/index.html) |

## Degradation hierarchy

The hierarchy is a **project policy** supported by `MULTI_SOURCE_CONFIRMED` data paths above. ATAS does not expose one automatic fallback API.

| Mode | Permitted analysis | Withhold |
| --- | --- | --- |
| MBO / L3 | Visible per-order IDs, sizes, counts and priorities where the feed delivers them; conditional refill hypotheses. | Participant identity and certain iceberg intent. |
| L2 DOM | Visible volume by price/side, imbalance, persistence and derived stacking/pulling. | Order IDs, true order count and queue priority. |
| Footprint | Executed Bid/Ask/volume/delta by price. | Resting-book claims. |
| Bars | OHLCV regime and context if enough valid bars and volume exist. | Strategies requiring absent order-flow confirmation. |
| `NO_TRADE` | Insufficient or stale data for the selected strategy. | Signals requiring missing evidence. |

`REFERENCE_REPO`: [DomV3/MboGridController.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MboGridController.cs) has an L2 **display** fallback. **This behavior is confirmed only in reference code**, not as an SDK promise. Degradation is feature-specific: bars-only regime classification may remain possible when an order-flow strategy is disabled.

## Initial scope and journal

- V1: OHLCV, session-aware VWAP, ADX, ATR percentile, a derived relative-volume ratio and compression/expansion; footprint Bid/Ask/Delta only where available. `MULTI_SOURCE_CONFIRMED`: [official candles](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html), [ADX.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ADX.cs), [ATR.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ATR.cs), [VWAP.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/VWAP.cs). Percentile and regime classification are proposed project calculations.
- V2: L2 DOM. `MULTI_SOURCE_CONFIRMED`: [depth provider](https://docs.atas.net/api/ATAS.Indicators.IMarketDepthInfoProvider.html), [DOM.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DOM.cs).
- V3: optional MBO/L3. `MULTI_SOURCE_CONFIRMED`: [ExtendedIndicator API](https://docs.atas.net/api/ATAS.Indicators.ExtendedIndicator.html), [DomV3/MainIndicator.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MainIndicator.cs).
- Journal each signal's observed flags, freshness, feed, calculation readiness and fallback cause. This is a **project decision**. `OFFICIAL_DOCS`: [tariffs](https://docs.atas.net/en/common-api/tariffs.html) show that API presence does not ensure data availability.
