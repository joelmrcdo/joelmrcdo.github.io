---
layout: post
title: "Moving Average Crossover Strategy"
date: 2025-06-03 00:00:00 +0000
---

Below is a minimal Python example illustrating a simple moving average crossover approach using daily SPY prices.

```python
import pandas as pd
import yfinance as yf

data = yf.download('SPY', start='2020-01-01', end='2020-12-31')
data['fast_ma'] = data['Close'].rolling(window=20).mean()
data['slow_ma'] = data['Close'].rolling(window=50).mean()
signals = (data['fast_ma'] > data['slow_ma']).astype(int)
print(signals.value_counts())
```

This generates a basic signal series where `1` represents times when the fast moving average is above the slow one.
