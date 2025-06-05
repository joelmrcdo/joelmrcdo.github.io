---
layout: post
title: "Moving Average Crossover Strategy"
date: 2025-06-04 00:00:00 +0000
---

<style>
.stats-box {
  float: right;
  width: 30%;
  margin-left: 1em;
  padding: 0.5em;
  border: 1px solid #ddd;
  background: #f9f9f9;
}
.clearfix::after {
  content: "";
  display: table;
  clear: both;
}
</style>

<div class="clearfix">
<div>
<p>This strategy enters a long position when a short moving average crosses above a longer moving average and exits on the opposite crossover.</p>
<ul>
  <li><strong>FAST_MA:</strong> 3</li>
  <li><strong>SLOW_MA:</strong> 6</li>
</ul>
<button id="refresh-data">Refresh Data</button>

<pre data-executable="true" data-language="python">
import pandas as pd
import matplotlib.pyplot as plt
from io import StringIO
from IPython.display import display, HTML

FAST = 3
SLOW = 6

try:
    import yfinance as yf
    data = yf.download('SPY', start='2020-01-01', end='2020-12-31')
except Exception as e:
    print('Using fallback data:', e)
    csv = '''Date,Close
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
2020-12-01,370'''
    data = pd.read_csv(StringIO(csv), parse_dates=['Date']).set_index('Date')

data['fast_ma'] = data['Close'].rolling(FAST).mean()
data['slow_ma'] = data['Close'].rolling(SLOW).mean()
data['signal'] = (data['fast_ma'] > data['slow_ma']).astype(int)
data['returns'] = data['Close'].pct_change()
data['strategy'] = data['signal'].shift(1) * data['returns']
data['cum_pnl'] = (1 + data['strategy']).cumprod().fillna(1.0)

ax = data['cum_pnl'].plot(title='Cumulative PnL')
plt.show()

data['trade_signal'] = data['signal'].diff()
trades = data.loc[data['trade_signal'] != 0, 'cum_pnl']
per_trade = trades.pct_change().dropna()
display(per_trade.to_frame('Trade PnL'))

stats_df = pd.Series({
    'Total Return': data['cum_pnl'].iloc[-1] - 1,
    'Number of Trades': per_trade.count(),
    'Average Trade': per_trade.mean()
})
html_stats = '<div class="stats-box">' + stats_df.to_frame('Value').to_html() + '</div>'
display(HTML(html_stats))
</pre>
</div>
</div>

## Full Code

```python
import pandas as pd
import matplotlib.pyplot as plt
from io import StringIO
from IPython.display import display, HTML

FAST = 3
SLOW = 6

try:
    import yfinance as yf
    data = yf.download('SPY', start='2020-01-01', end='2020-12-31')
except Exception as e:
    print('Using fallback data:', e)
    csv = '''Date,Close
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
2020-12-01,370'''
    data = pd.read_csv(StringIO(csv), parse_dates=['Date']).set_index('Date')

data['fast_ma'] = data['Close'].rolling(FAST).mean()
data['slow_ma'] = data['Close'].rolling(SLOW).mean()
data['signal'] = (data['fast_ma'] > data['slow_ma']).astype(int)
data['returns'] = data['Close'].pct_change()
data['strategy'] = data['signal'].shift(1) * data['returns']
data['cum_pnl'] = (1 + data['strategy']).cumprod().fillna(1.0)

data['trade_signal'] = data['signal'].diff()
trades = data.loc[data['trade_signal'] != 0, 'cum_pnl']
per_trade = trades.pct_change().dropna()

stats_df = pd.Series({
    'Total Return': data['cum_pnl'].iloc[-1] - 1,
    'Number of Trades': per_trade.count(),
    'Average Trade': per_trade.mean()
})
```

<script>
document.getElementById('refresh-data').addEventListener('click', function() {
  thebe.bootstrap();
  setTimeout(function(){ if (thebe) { thebe.runAll(); } }, 1000);
});
</script>

**Financial disclaimer:** This material is for educational purposes only and is not financial advice.
