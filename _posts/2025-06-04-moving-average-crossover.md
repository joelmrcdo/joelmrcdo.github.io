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
<p>This strategy enters a long position when a short moving average crosses above a longer moving average and exits on the opposite crossover. Use the fields below to adjust the moving average windows before running the example.</p>
<ul>
  <li><strong>FAST_MA:</strong> <input id="fast-input" type="number" value="3" /></li>
  <li><strong>SLOW_MA:</strong> <input id="slow-input" type="number" value="6" /></li>
</ul>
<button id="refresh-data">Refresh Data</button>

<p>Click this button to run the code and render the chart.</p>

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
window.addEventListener('load', function () {
  thebe.bootstrap();
  let kernelReady = false;
  thebe.once('kernel_ready.Kernel', () => {
    kernelReady = true;
    thebe.runAll();
  });
  setTimeout(function () {
    if (!kernelReady) {
      console.error('Thebe kernel did not start within 10 seconds.');
      const msg = document.createElement('div');
      msg.textContent = 'Failed to start Python kernel. See console for details.';
      msg.style.color = 'red';
      document.body.prepend(msg);
    }
  }, 10000);

document.getElementById('refresh-data').addEventListener('click', function() {
  const fast = parseInt(document.getElementById('fast-input').value) || 3;
  const slow = parseInt(document.getElementById('slow-input').value) || 6;
  thebe.bootstrap();
  thebe.once('kernel_ready.Kernel', () => {
    thebe.kernel.execute(`FAST = ${fast}\nSLOW = ${slow}`);
    thebe.runAll();
  });
});
});
</script>

**Financial disclaimer:** This material is for educational purposes only and is not financial advice.
