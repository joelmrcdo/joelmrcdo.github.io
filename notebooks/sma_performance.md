# Moving Average Performance Evaluation

This note describes how to evaluate a simple moving average crossover strategy. When real market data cannot be downloaded, the example falls back to a small sample dataset so the calculations can still run.

<button id="run-notebook">Run</button>

<pre data-executable="true" data-language="python">
import pandas as pd
import matplotlib.pyplot as plt

# load sample data if `yfinance` download fails
try:
    import yfinance as yf
    data = yf.download('SPY', start='2020-01-01', end='2020-12-31')
except Exception:
    data = pd.DataFrame({'Close': [100, 101, 102, 103, 104]})

data['fast_ma'] = data['Close'].rolling(window=20, min_periods=1).mean()
data['slow_ma'] = data['Close'].rolling(window=50, min_periods=1).mean()

# plot moving averages
data[['Close', 'fast_ma', 'slow_ma']].plot(title='Moving Average Crossover')
plt.show()

# trading statistics
data['position'] = (data['fast_ma'] > data['slow_ma']).astype(int)
data['trade'] = data['position'].diff().abs().fillna(0)
data['strategy_return'] = data['position'].shift(1) * data['Close'].pct_change()
total_return = (1 + data['strategy_return'].fillna(0)).prod() - 1
num_trades = int(data['trade'].sum())

pd.DataFrame({'total_return': [total_return], 'trades': [num_trades]})
</pre>

<script>
document.getElementById('run-notebook').addEventListener('click', function() {
  thebe.bootstrap();
});
</script>

**Financial disclaimer:** This material is for educational purposes only and is not financial advice.
