# ETH/USDT Momentum Strategy Research

This project tests whether recent ETH/USDT price moves help predict the next move. It uses Binance spot candles, mainly hourly data from August 2017 to November 2025. The notebooks are research experiments, not a live trading system.

## Process

The first test takes a long position when the sum of recent log returns is positive and a short position otherwise. Positions are shifted one bar before returns are measured. After an exploratory one-minute test, the analysis focuses on hourly candles, compares lookback windows, and selects windows on training periods for walk-forward tests.

The later hourly notebook scales momentum by recent volatility, limits position size, ignores weak signals, and combines the 15-, 18-, and 19-hour windows. It then adds estimated trading costs of 0.10% in fees and 0.03% in slippage per position change. A second notebook explores a four-hour EMA trend filter and a minimum holding period.

## Findings

- The one-minute test did not show a convincing signal. Hourly momentum looked more promising, but results varied substantially across test periods.
- In the initial walk-forward run, 14-, 18-, 19-, and 23-hour windows accounted for 59 of 76 selected windows. This concentration did not translate into stable performance.
- The hourly ensemble changed positions frequently: the costed notebook counts 69,351 changes. Costs therefore materially weaken the apparent gross result.
- The final notebook's saved output reports a 0.72 Sharpe ratio and 93% maximum drawdown for the filtered, minimum-hold version, versus 0.75 Sharpe for buy and hold. Treat these as provisional: the final call contains a syntax error, and the four-hour filter uses each candle's close at its opening timestamp, which can introduce lookahead bias.

The main result is a possible hourly momentum effect that remains too sensitive to trading costs, drawdown, and validation details to support a live strategy. Start with [`Strats/Momentum_ETH/ETHUSDT_1h_momentum.ipynb`](Strats/Momentum_ETH/ETHUSDT_1h_momentum.ipynb); the follow-up is [`ETHUSDAT_1h_momentum_v2.ipynb`](Strats/Momentum_ETH/ETHUSDAT_1h_momentum_v2.ipynb). The one-minute CSV is stored with Git LFS.
