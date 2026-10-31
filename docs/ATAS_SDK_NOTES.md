# ATAS SDK Notes

Research record; check the installed ATAS DLL before implementation. The [official API reference](https://docs.atas.net/api/index.html) is generated from the development branch and can describe unreleased members.

Evidence labels:

- `OFFICIAL_DOCS`: current ATAS SDK documentation or Help Center.
- `REFERENCE_REPO`: verified only in the [reference repository](https://github.com/avillavicenciom/atas-Indicators) for this record.
- `NOTEBOOKLM`: verified only in [Integración ATAS MenthorQ](https://notebooklm.google.com/notebook/32f0118c-cf5e-47d8-afbb-debc4ef93374).
- `MULTI_SOURCE_CONFIRMED`: supported by at least two of those source groups.

Follow the source priority in `AGENTS.md` for discrepancies. Design calculations derived from ATAS data are not SDK APIs.

## Candles and footprint

| API or observation | Evidence | Verification |
| --- | --- | --- |
| `GetCandle(bar)` supplies an `IndicatorCandle` with OHLC, Volume, Bid, Ask, Delta, MinDelta and MaxDelta. | `MULTI_SOURCE_CONFIRMED` | [Official guide](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html); NotebookLM sources “Examples of indicators - ATAS” and “Development of a user indicator - ATAS”. Valid bar range: 0 to CurrentBar minus 1. |
| `GetPriceVolumeInfo(price)` and `PriceVolumeInfo.Bid`, `.Ask`, `.Volume` give executed footprint volume at a price; per-level delta is Ask minus Bid. | `MULTI_SOURCE_CONFIRMED` | [StackedImbalance.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/StackedImbalance.cs), [ClusterSearch.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ClusterSearch.cs), NotebookLM sources above. Footprint volume is not resting DOM liquidity. |
| `GetAllPriceLevels()` traverses levels; an overload accepts a reusable `PriceVolumeInfo`, overwritten on each iteration. | `OFFICIAL_DOCS` | [Performance guide](https://docs.atas.net/en/chart-indicators/performance.html). Do not retain references to the reused object; check installed overload signatures before coding. |
| `InstrumentInfo.TickSize` is used to scan adjacent price levels. | `REFERENCE_REPO` | [StackedImbalance.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/StackedImbalance.cs). **This scanning pattern is confirmed here only by reference code.** |
| `IsNewSession(bar)`, `IsNewWeek(bar)`, `IsNewMonth(bar)` identify period boundaries. | `MULTI_SOURCE_CONFIRMED` | [Official indicator guide](https://docs.atas.net/en/md_DataFeedsCore_2Docs_2en_20020__CustomizingOurIndicator.html), [VWAP.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/VWAP.cs). |
| `OnCalculate` handles history and repeats for the forming live bar. | `OFFICIAL_DOCS` | [Lifecycle](https://docs.atas.net/en/chart-indicators/lifecycle.html). State updates for one bar must be idempotent. |

## Trades

| API or observation | Evidence | Verification |
| --- | --- | --- |
| `OnNewTrade(MarketDataArg trade)` receives an individual live trade. | `MULTI_SOURCE_CONFIRMED` | [Official lifecycle](https://docs.atas.net/en/chart-indicators/lifecycle.html), [MarketPower.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/MarketPower.cs). |
| `OnCumulativeTrade(CumulativeTrade trade)` and `OnUpdateCumulativeTrade(CumulativeTrade trade)` distinguish a new cumulative trade from an update. | `REFERENCE_REPO` | [MarketPower.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/MarketPower.cs). **This callback pair is confirmed here only by reference code; verify against the installed SDK.** |
| `RequestForCumulativeTrades(CumulativeTradesRequest request)` requests historical cumulative trades. | `OFFICIAL_DOCS` | [ExtendedIndicator API](https://docs.atas.net/api/ATAS.Indicators.ExtendedIndicator.html). Historical request and live callbacks are separate paths. |
| `MarketDataArg` is also used for depth and best quotes; interpret its fields in callback context. | `OFFICIAL_DOCS` | [MarketDataArg API](https://docs.atas.net/api/ATAS.Indicators.MarketDataArg.html). A resting Bid/Ask level is not an executed trade. |

## DOM / Level 2

| API or observation | Evidence | Verification |
| --- | --- | --- |
| Nullable `MarketDepthInfo` provides `GetMarketDepthSnapshot()`, returning current `IEnumerable<MarketDataArg>` depth levels. | `MULTI_SOURCE_CONFIRMED` | [Indicator API](https://docs.atas.net/api/ATAS.Indicators.Indicator.html), [depth provider](https://docs.atas.net/api/ATAS.Indicators.IMarketDepthInfoProvider.html), [DOM.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DOM.cs). |
| `MarketDepthChanged(MarketDataArg depth)`, `MarketDepthsChanged(IEnumerable<MarketDataArg> depths)` and `OnBestBidAskChanged(MarketDataArg depth)` are depth and best-quote callbacks. | `MULTI_SOURCE_CONFIRMED` | [ExtendedIndicator API](https://docs.atas.net/api/ATAS.Indicators.ExtendedIndicator.html), [DOM.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DOM.cs), [DomV3/MainIndicator.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MainIndicator.cs). The docs do not guarantee individual and batch callbacks are mutually exclusive for a change. |
| `MarketDataArg.DataType`, `.Price`, `.Volume`, `.Time`, `.IsBid`, `.IsAsk` describe a level. Zero volume removes that level in the reference cache. | `MULTI_SOURCE_CONFIRMED` | [MarketDataArg API](https://docs.atas.net/api/ATAS.Indicators.MarketDataArg.html), [MboGridController.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MboGridController.cs). Updates replace a level's current size; they are not additive volume deltas. |
| `MarketDepthInfo.CumulativeDomAsks/Bids` are current visible depth totals by side. | `MULTI_SOURCE_CONFIRMED` | [Depth provider](https://docs.atas.net/api/ATAS.Indicators.IMarketDepthInfoProvider.html), [DOM.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DOM.cs). They are not net changes over an interval. |
| Stacked volume, pulled volume and local `NetVolume = StackedVolume - PulledVolume` compare successive visible sizes. | `REFERENCE_REPO` | [DOM.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DOM.cs). **These are example calculations, not SDK members.** A decline alone does not prove cancellation. |
| Direct `Indicator.GetMarketDepthSnapshot()` and `Indicator.CumulativeDomAsks/Bids` are obsolete in favor of `MarketDepthInfo`. | `OFFICIAL_DOCS` | [Indicator API](https://docs.atas.net/api/ATAS.Indicators.Indicator.html). |

## MBO / Level 3

| API or observation | Evidence | Verification |
| --- | --- | --- |
| `SubscribeMarketByOrderData()` returns `Task`; `OnMarketByOrdersChanged(IEnumerable<MarketByOrder> values)` receives changes; `MarketByOrders` is the current `IEnumerable<MarketByOrder>` snapshot. | `MULTI_SOURCE_CONFIRMED` | [ExtendedIndicator API](https://docs.atas.net/api/ATAS.Indicators.ExtendedIndicator.html), [DomV3/MainIndicator.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MainIndicator.cs), NotebookLM. Task completion does not prove data arrived. |
| `MarketByOrder` exposes `ExchangeOrderId`, `Priority`, `Price`, `Volume`, `Side`, `Type`, `Time`, `Security`; `MarketByOrderUpdateTypes` includes `Snapshot`, `New`, `Change`, `Delete`. | `MULTI_SOURCE_CONFIRMED` | [MarketByOrder API](https://docs.atas.net/api/ATAS.DataFeedsCore.MarketByOrder.html), [enum](https://docs.atas.net/api/ATAS.DataFeedsCore.MarketByOrderUpdateTypes.html), [MboGridController.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MboGridController.cs). Order ID does not identify a participant. |
| DomV3 uses L2 when its MBO grid is empty and creates a synthetic display item with ID and priority zero. | `REFERENCE_REPO` | [MainIndicator.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MainIndicator.cs), [MboGridController.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MboGridController.cs). **Fallback is confirmed only by reference code, not an automatic SDK feature.** |
| MBO depends on feed, connector, data subscription and ATAS tariff. A restricted subscription may complete with an empty snapshot and no callback. | `OFFICIAL_DOCS` | [API tariffs](https://docs.atas.net/en/common-api/tariffs.html), [Rithmic help](https://help.atas.net/en/support/solutions/articles/72000602593). |
| `MboCapabilities` and `MarketByOrder.Reason/Flags/ReceivedTime/SourceTimeNs` appear in development documentation but are marked unavailable in ATAS 8.0.15. | `OFFICIAL_DOCS` | [Capabilities API](https://docs.atas.net/api/ATAS.DataFeedsCore.MboCapabilities.html), [MarketByOrder API](https://docs.atas.net/api/ATAS.DataFeedsCore.MarketByOrder.html). Check the installed release. |

## Regime indicator reference patterns

| Calculation | Evidence | Verification |
| --- | --- | --- |
| ADX uses OHLC directional movement and smoothing; ATR uses OHLC true range. | `REFERENCE_REPO` | [ADX.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ADX.cs), [ATR.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ATR.cs). **ATR percentile is our proposed calculation, not an ATR.cs output.** |
| VWAP accumulates volume-weighted price and can reset by session. | `MULTI_SOURCE_CONFIRMED` | [VWAP.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/VWAP.cs), [VWAP help](https://help.atas.net/en/support/solutions/articles/72000602503-vwap). |
| RelativeVolume compares current bar volume with a same-time average on time or seconds charts; the file emits two series, not a ratio. | `MULTI_SOURCE_CONFIRMED` | [RelativeVolume.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/RelativeVolume.cs), [Help Center](https://help.atas.net/en/support/solutions/articles/72000602457-relative-volume). Our ratio must be derived explicitly. |
| BollingerSqueezeV3 plots a StdDev-to-ATR ratio; SwingHighLow confirms pivots after a delay; Zigzag retains a provisional live leg; Donchian scans a range. | `REFERENCE_REPO` | [BollingerSqueezeV3.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/BollingerSqueezeV3.cs), [SwingHighLow.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/SwingHighLow.cs), [Zigzag.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/Zigzag.cs), [Donchian.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/Donchian.cs). **These formulas and patterns are confirmed here only by reference code.** |

## Discrepancies and boundaries

- `NOTEBOOKLM`: notebook sources did not document MBO `ExchangeOrderId`, `Priority`, the four update types or fallback. `OFFICIAL_DOCS` and `REFERENCE_REPO` confirm the fields and enum; `REFERENCE_REPO` alone confirms the fallback. This is a notebook coverage gap.
- `MULTI_SOURCE_CONFIRMED`: Relative Volume Help describes a ratio; RelativeVolume.cs plots volume and a time-of-day average separately. The proposed detector ratio is derived.
- `OFFICIAL_DOCS`: [MBO DOM Help](https://help.atas.net/en/support/solutions/articles/72000633231-mbo-dom) names Rithmic; [MBO tracker Help](https://help.atas.net/en/support/solutions/articles/72000660331-mbo-indicators-icebergs-stops-sweeps) names Rithmic and Plus500 for those trackers. Their product scopes differ; neither is a universal connector guarantee.
- `OFFICIAL_DOCS`: [performance guidance](https://docs.atas.net/en/chart-indicators/performance.html) calls for light, allocation-conscious hot callbacks. Execution APIs require a separate order-lifecycle review.
