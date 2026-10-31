# Market Regimes

## TREND_UP
Possible evidence:
- HH/HL structure;
- positive VWAP slope;
- acceptance above VWAP;
- directional efficiency;
- trend strength;
- supportive participation.

Allowed family:
- long pullback / continuation.

## TREND_DOWN
Mirror of `TREND_UP`.

Allowed family:
- short pullback / continuation.

## RANGE
Possible evidence:
- flatter VWAP;
- stable POC;
- repeated rotation;
- weak directional efficiency;
- rejected breakouts;
- contained volatility.

Allowed family:
- reversion near validated edges.

Avoid center-of-range entries.

## COMPRESSION
Possible evidence:
- low/falling ATR percentile;
- squeeze;
- low relative volume;
- contracting range.

Default:
- wait for expansion.

## BREAKOUT_CANDIDATE
Possible evidence:
- volatility expansion;
- relative-volume expansion;
- departure from balance;
- directional order-flow support.

Default:
- wait for acceptance or valid retest.

## TRANSITION
Used to prevent abrupt regime switching.

## HIGH_VOLATILITY
Default:
- reduce activity or no trade.

## UNKNOWN
Conflicting evidence or insufficient data.

Default:
- no trade.

## Candidate feature groups

Direction:
- structure;
- VWAP slope;
- price vs VWAP.

Strength:
- ADX;
- directional efficiency;
- persistence.

Volatility:
- ATR percentile;
- range percentile;
- squeeze/expansion.

Participation:
- relative volume;
- volume expansion/contraction.

Order-flow context:
- CVD slope;
- delta persistence;
- divergence warnings.

## Scoring

Use multidimensional scores, not one binary indicator.

A confidence score describes confidence in regime classification, not win probability.

## Hysteresis

Use different enter/exit thresholds and persistence requirements.
