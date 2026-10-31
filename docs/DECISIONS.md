# Decisions Log

Evidence labels are defined in [ATAS_SDK_NOTES.md](ATAS_SDK_NOTES.md). They identify sources supporting each decision's technical premise. **The decisions are ours**, not ATAS instructions or API members.

## Architecture and validation

| Decision | Evidence | Basis |
| --- | --- | --- |
| Build a regime-based adaptive system, not a single footprint-bounce indicator. Keep market data, regime, location, strategy, order flow, risk and execution separate. | `MULTI_SOURCE_CONFIRMED` | [Official candle and footprint API](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html) exposes distinct data inputs; the [reference repository](https://github.com/avillavicenciom/atas-Indicators) demonstrates separate calculations. Module boundaries are defined in [ARCHITECTURE.md](ARCHITECTURE.md). |
| Use footprint/order flow to confirm regime and location, rather than define the whole market state. | `MULTI_SOURCE_CONFIRMED` | [Official candles/footprint guide](https://docs.atas.net/en/chart-indicators/market-data/candles-and-footprint.html) distinguishes OHLCV from price-level executions; [ClusterSearch.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ClusterSearch.cs) analyzes footprint levels. |
| Prefer short tick-bounded zones over an exact reversal price. | `REFERENCE_REPO` | [ClusterSearch.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ClusterSearch.cs) and [StackedImbalance.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/StackedImbalance.cs) offer price-level patterns. **The zone policy is our judgment, not an ATAS API claim.** |
| Journal hypotheses and outcomes; require empirical and out-of-sample validation. | `REFERENCE_REPO` | Reference indicators expose measurements, not predictive-performance proof. **This validation policy is ours.** |
| Use `avillavicenciom/atas-Indicators` as an external implementation reference, not as the product repository. | `REFERENCE_REPO` | [Reference repository](https://github.com/avillavicenciom/atas-Indicators). Study StackedImbalance, ClusterSearch, Delta/CumulativeDelta/MarketPower, VWAP, RelativeVolume, ADX/ATR, DOM and DomV3 without copying full classes. |

## Data and regime

| Decision | Evidence | Basis |
| --- | --- | --- |
| Detect actual capabilities and degrade `MBO -> L2 -> Footprint -> Bars -> NO_TRADE`. | `MULTI_SOURCE_CONFIRMED` | [Official tariffs](https://docs.atas.net/en/common-api/tariffs.html) show that MBO subscription can complete with no data; [MboGridController.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MboGridController.cs) implements an L2 display fallback. **The full hierarchy is our policy, not an SDK fallback.** |
| Never present L2 changes as order identity or proof of cancellation; never present MBO order IDs as participant identity. | `MULTI_SOURCE_CONFIRMED` | [MarketDataArg API](https://docs.atas.net/api/ATAS.Indicators.MarketDataArg.html), [MarketByOrder API](https://docs.atas.net/api/ATAS.DataFeedsCore.MarketByOrder.html), [DOM.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DOM.cs). |
| Prefer ATR-scaled distances, percentiles and time-of-day relative volume to fixed instrument-specific thresholds. | `MULTI_SOURCE_CONFIRMED` | [ATR.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ATR.cs), [RelativeVolume.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/RelativeVolume.cs), [Relative Volume help](https://help.atas.net/en/support/solutions/articles/72000602457-relative-volume). **Percentiles and the ratio are our derived features.** |
| Give `MarketRegimeDetector` separate direction, strength/participation and volatility evidence; use hysteresis and transition states. | `REFERENCE_REPO` | [ADX.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ADX.cs), [ATR.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/ATR.cs), [VWAP.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/VWAP.cs), [BollingerSqueezeV3.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/BollingerSqueezeV3.cs) measure different aspects. **Weights and thresholds remain design hypotheses.** |
| Classify from completed bars; mark forming bars and unconfirmed pivots provisional. | `MULTI_SOURCE_CONFIRMED` | [Official lifecycle](https://docs.atas.net/en/chart-indicators/lifecycle.html) recalculates the forming bar; [SwingHighLow.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/SwingHighLow.cs) confirms pivots later; [Zigzag.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/Zigzag.cs) keeps a live leg. |

## Execution and access

| Decision | Evidence | Basis |
| --- | --- | --- |
| Keep early phases analysis-only; validate native `ChartStrategy` and the full order lifecycle before execution or an external bridge. | `OFFICIAL_DOCS` | [ChartStrategy API](https://docs.atas.net/api/ATAS.Strategies.Chart.ChartStrategy.html). **Live order capability is not validated for this project.** |
| Start with the available ATAS plan and feed; reassess paid access when a required capability is observed absent. | `OFFICIAL_DOCS` | [API tariffs](https://docs.atas.net/en/common-api/tariffs.html), [online market data help](https://help.atas.net/en/support/solutions/articles/72000649936-online-market-data-and-data-feed-in-atas). A Start/free plan is **not assumed sufficient** for live futures, MBO, replay or execution. |

## Source differences

| Difference | Evidence | Resolution |
| --- | --- | --- |
| [NotebookLM](https://notebooklm.google.com/notebook/32f0118c-cf5e-47d8-afbb-debc4ef93374) found MBO subscription, callback and snapshot but did not find `ExchangeOrderId`, `Priority` or the four update types. | `MULTI_SOURCE_CONFIRMED` | [Official MarketByOrder API](https://docs.atas.net/api/ATAS.DataFeedsCore.MarketByOrder.html), [update enum](https://docs.atas.net/api/ATAS.DataFeedsCore.MarketByOrderUpdateTypes.html) and [MboGridController.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/DomV3/MboGridController.cs) verify them. The notebook's omission is a coverage gap. |
| Relative Volume Help describes a ratio; RelativeVolume.cs plots volume and a time-of-day average separately. | `MULTI_SOURCE_CONFIRMED` | [Help Center](https://help.atas.net/en/support/solutions/articles/72000602457-relative-volume), [RelativeVolume.cs](https://github.com/avillavicenciom/atas-Indicators/blob/Develop/Technical/RelativeVolume.cs). Calculate and label our derived ratio explicitly. |
| MBO DOM Help names Rithmic; MBO tracker Help names Rithmic and Plus500 for those trackers. | `OFFICIAL_DOCS` | [MBO DOM](https://help.atas.net/en/support/solutions/articles/72000633231-mbo-dom), [MBO trackers](https://help.atas.net/en/support/solutions/articles/72000660331-mbo-indicators-icebergs-stops-sweeps). Treat the statements as product-specific, then test the active feed. |
| The SDK website describes development-branch members. | `OFFICIAL_DOCS` | [API reference](https://docs.atas.net/api/index.html). Verify installed assemblies before relying on members marked as future-version. |

## Codex context

Persist context in `AGENTS.md` and `docs/`; use the NotebookLM MCP for curated research. Notebook: **Integración ATAS MenthorQ**, ID `32f0118c-cf5e-47d8-afbb-debc4ef93374`. `NOTEBOOKLM`: [notebook](https://notebooklm.google.com/notebook/32f0118c-cf5e-47d8-afbb-debc4ef93374).
