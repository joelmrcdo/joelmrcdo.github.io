---
layout: post
title: "Moving Average Crossover Strategy"
date: 2025-06-04 00:00:00 +0000
---


<button id="run-notebook">Run</button>

<pre data-executable="true" data-language="python">
import pandas as pd
import matplotlib.pyplot as plt
try:
    import yfinance as yf
    data = yf.download('SPY', start='2020-01-01', end='2020-12-31')
except Exception as e:
    print('Using fallback data:', e)
    csv = """Date,Close
2020-01-01,320
2020-02-01,330
2020-03-01,280
2020-04-01,290
2020-05-01,300
2020-06-01,310
2020-07-01,320
2020-08-01,330
2020-09-01,340
2020-10-01,350
2020-11-01,360
2020-12-01,370
"""
    from io import StringIO
    data = pd.read_csv(StringIO(csv), parse_dates=['Date']).set_index('Date')

data['fast_ma'] = data['Close'].rolling(3).mean()
data['slow_ma'] = data['Close'].rolling(6).mean()
ax = data[['fast_ma', 'slow_ma']].plot(title='SMA Crossover')
plt.show()
</pre>
<script>
document.getElementById('run-notebook').addEventListener('click', function() {
  thebe.bootstrap();
});
</script>

=======
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

