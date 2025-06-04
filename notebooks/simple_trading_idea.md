# Simple Moving Average Crossover
This example downloads daily prices for SPY and computes two moving averages. The crossover of the fast and slow averages is used as a trading signal.


```python
import pandas as pd
import yfinance as yf

data = yf.download('SPY', start='2020-01-01', end='2020-12-31')
data['fast_ma'] = data['Close'].rolling(window=20).mean()
data['slow_ma'] = data['Close'].rolling(window=50).mean()
signals = (data['fast_ma'] > data['slow_ma']).astype(int)
signals.value_counts()
```
