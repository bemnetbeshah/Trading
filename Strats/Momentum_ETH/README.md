# ETH 1h Momentum Strategy Research

## Project Overview

This project implements and evaluates a time-series momentum trading strategy for ETH/USDT on a 1-hour timeframe. The research focuses on identifying and quantifying momentum effects in cryptocurrency markets, with particular attention to risk management and strategy robustness.

## Data

- **Asset**: ETHUSDT
- **Timeframe**: 1 hour
- **Features**:
  - Close price
  - Log returns: `log(close_t / close_(t-1))`

## Strategy Description

### Type
Time-Series Momentum Strategy

### Signal Generation
The strategy uses a rolling sum of past log returns as the momentum signal:

```
prior_log_return_sum = rolling_sum(log_return, window=N)
```

### Position Rules
- **Long**: If momentum > 0
- **Short**: If momentum ≤ 0
- **Alignment**: `position_t = signal_(t-1)` (use previous period's signal for current period)

### Returns Calculation
```
strategy_return = position_(t-1) * log_return_t
```

### Equity Curve
```
equity_curve = exp(cumulative_log_returns)
```

## Backtesting Issues & Fixes

### Issues Detected

1. **Incorrect Drawdown Calculation**: Originally used absolute drawdown instead of percentage drawdown
2. **High Parameter Sensitivity**: Strategy performance varies significantly with window parameter
3. **Extreme Drawdowns**: Full long/short exposure leads to unbounded risk
4. **Inflated Sharpe Ratio**: Metrics were unstable without proper risk controls
5. **Regime Dependency**: Strategy behavior highly dependent on market conditions

### Fixes Applied

1. ✅ **Corrected Drawdown Calculation**: 
   ```python
   dd = equity / equity.cummax() - 1
   ```

2. ✅ **Walk-Forward Validation**: Implemented proper out-of-sample testing methodology

3. ✅ **Best Window Analysis**: Generated frequency distribution of optimal windows across validation periods

## Walk-Forward Validation Results

### Key Observations

1. **Momentum Edge Confirmed**: The strategy demonstrates consistent predictive power across validation periods

2. **Window Clustering**: Best-performing windows cluster strongly in the **14–24 hour range**
   - Most frequent winner: **Window = 19**
   - Other frequent winners: Windows 18, 23, 14

3. **Session Structure Alignment**: The optimal window cluster (14–24 hours) aligns with crypto market session structure:
   - Asia → EU → US session continuity
   - Captures momentum across trading day transitions

4. **Sharpe Ratio Instability**: 
   - Sharpe ratios swing between **-6 and +8**
   - Indicates structurally unstable system
   - Caused by unbounded raw exposure and lack of risk controls

### Interpretation

**Conclusion**: Momentum exists and shows predictive power, but the raw implementation is **untradeable** due to:
- Regime instability
- Leverage-like swings
- Lack of risk controls
- Extreme drawdowns

**Problem**: The extreme Sharpe ratio volatility (-6 to +8) indicates a fundamentally unstable system that requires risk management improvements.

## Next Steps

### Priority
Convert the raw momentum signal into a **robust, risk-controlled strategy** suitable for real-world evaluation.

### Implementation Roadmap

#### 1. Volatility Scaling
**Goal**: Normalize momentum by recent volatility to control risk

**Implementation**:
```python
scaled_signal = momentum / rolling_vol
position = tanh(scaled_signal)  # Bounded position sizing
```

#### 2. Signal Thresholding
**Goal**: Reduce whipsaw by avoiding trades during weak momentum

**Implementation**:
```python
if |momentum| < threshold:
    position = 0  # No trade
```

#### 3. Long-Only Variant
**Goal**: Leverage ETH's upward drift while reducing drawdowns

**Rationale**: Removing short positions stabilizes the system and reduces massive drawdowns, as ETH exhibits strong long-term upward bias.

#### 4. Ensemble Model
**Goal**: Reduce parameter sensitivity by combining multiple signals

**Implementation**: Combine signals from windows {14, 18, 19, 23} using weighted averaging or voting mechanisms.

#### 5. Regime Filters
**Goal**: Trade only when higher timeframe confirms direction

**Implementation**: 
- Add higher timeframe trend filter (e.g., daily trend)
- Add volatility regime detection
- Only trade when regime confirms momentum direction

#### 6. Re-run Walk-Forward Validation
**Goal**: Verify improvements in:
- Sharpe ratio stability
- Drawdown control
- Overall strategy robustness

## Project Goal

Produce a **stable, realistic 1-hour ETH momentum strategy** suitable for:
- Further refinement
- Feature engineering
- Real-world evaluation

The strategy should demonstrate:
- Consistent Sharpe ratios (reduced volatility)
- Controlled drawdowns
- Reduced parameter sensitivity
- Robustness across different market regimes

## Usage

See the Jupyter notebooks in this directory for:
- Strategy implementation
- Backtesting framework
- Walk-forward validation
- Performance analysis

## Notes

- This is research code and should not be used for live trading without extensive additional testing
- All results are based on historical data and may not reflect future performance
- Transaction costs, slippage, and other real-world constraints should be incorporated before live deployment




