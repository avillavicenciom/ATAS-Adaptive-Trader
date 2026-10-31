# Order Flow Design

## Principle

Order flow confirms context.

Preferred sequence:

`regime -> location -> setup -> order-flow confirmation -> risk gate`

## Footprint

Primary price-level data:
- Bid;
- Ask;
- Volume;
- Delta;
- local concentration / POC behavior.

References in `avillavicenciom/atas-Indicators`:
- `Technical/ClusterSearch.cs`
- `Technical/StackedImbalance.cs`

## Diagonal / stacked imbalance

Use adjacent diagonal comparison, ratio threshold, volume threshold and optional consecutive-level requirement.

Do not replace true footprint imbalance with whole-candle Ask/Bid ratios.

## Absorption

Treat as a hypothesis.

Candidate evidence:
- strong aggressive flow into a zone;
- limited price progress;
- rejection/failure to continue;
- opposing response;
- optional delta/CVD confirmation.

A large delta alone is insufficient.

## Exhaustion

Possible evidence:
- diminishing aggressive volume near an extreme;
- poor continuation;
- rejection/failed auction behavior.

Must be location-aware and statistically validated.

## Delta and CVD

Use:
- candle delta;
- Min/Max Delta;
- session CVD;
- CVD slope/persistence;
- divergence as warning/confirmation.

Divergence alone is not an entry.

## Cluster zones

Prefer short zones to false exact-price precision.

A zone may report:
- price range;
- total volume;
- Bid;
- Ask;
- Delta;
- local POC;
- market location;
- evidence;
- confirmation required;
- invalidation.

## DOM / L2

Later phase:
- total depth;
- bid/ask imbalance;
- stacking;
- pulling;
- persistence/cancellation.

Visible liquidity can be cancelled; do not equate it with committed intent.

## MBO / L3

Advanced optional phase:
- order count;
- order-size distribution;
- priority;
- refill behavior;
- iceberg clues where observable.

Do not infer institutional identity.

## Data-quality labels

- `L3_MBO`
- `L2_DOM`
- `FOOTPRINT_ONLY`
- `BAR_ONLY`
- `INSUFFICIENT`

Signal logic should become more conservative as data quality decreases.
