# SMA Crossover Backtest
This notebook downloads daily SPY data, computes fast and slow moving averages, and evaluates a simple crossover strategy.


```

import pandas as pd
import yfinance as yf
import matplotlib.pyplot as plt

# Download SPY daily data
spy = yf.download('SPY', start='2022-01-01', progress=False)
spy['fast_sma'] = spy['Close'].rolling(window=20).mean()
spy['slow_sma'] = spy['Close'].rolling(window=50).mean()

# Generate position: 1 if fast > slow else 0
spy['signal'] = (spy['fast_sma'] > spy['slow_sma']).astype(int)
spy['position'] = spy['signal'].shift(1).fillna(0)
spy['returns'] = spy['Close'].pct_change()
spy['strategy'] = spy['position']*spy['returns']
spy['cumulative_pnl'] = (1+spy['strategy']).cumprod()-1

# Identify trades
entries = spy[(spy['position']==1) & (spy['position'].shift(1)==0)].index
exits = spy[(spy['position']==0) & (spy['position'].shift(1)==1)].index
if len(entries) > len(exits):
    exits = exits.append(pd.Index([spy.index[-1]]))
trades = []
for entry, exit in zip(entries, exits):
    entry_price = spy.loc[entry,'Close']
    exit_price = spy.loc[exit,'Close']
    trades.append(exit_price/entry_price - 1)

wins = [p for p in trades if p>0]
losses = [p for p in trades if p<=0]
win_rate = len(wins)/len(trades) if trades else 0
avg_gain = sum(wins)/len(wins) if wins else 0
avg_loss = sum(losses)/len(losses) if losses else 0

# Figures
plt.figure()
spy['cumulative_pnl'].plot(title='Cumulative PnL')
plt.savefig('assets/images/sma_cumulative_pnl.png')
plt.close()

plt.figure()
plt.bar(range(len(trades)), trades)
plt.axhline(0,color='black')
plt.title('Per-Trade PnL')
plt.savefig('assets/images/sma_trade_pnl.png')
plt.close()

plt.figure()
plt.bar(['wins','losses'], [len(wins), len(losses)])
plt.title(f'Trade Stats
Win rate {win_rate:.1%}
Avg gain {avg_gain:.2%}
Avg loss {avg_loss:.2%}')
plt.savefig('assets/images/sma_trade_stats.png')
plt.close()

summary = {
    'trades': len(trades),
    'win_rate': win_rate,
    'avg_gain': avg_gain,
    'avg_loss': avg_loss
}
summary

```
