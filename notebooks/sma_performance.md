# Moving Average Performance Evaluation

This note describes how to evaluate a simple moving average crossover strategy. When real market data cannot be downloaded, the example falls back to a small sample dataset so the calculations can still run.

```python
import pandas as pd

# load sample data if `yfinance` download fails
try:
    import yfinance as yf
    data = yf.download('SPY', start='2020-01-01', end='2020-12-31')
except Exception:
    data = pd.DataFrame({'Close': [100, 101, 102, 103, 104]})

data['fast_ma'] = data['Close'].rolling(window=2).mean()
data['slow_ma'] = data['Close'].rolling(window=4).mean()
```

**Financial disclaimer:** This material is for educational purposes only and is not financial advice.
