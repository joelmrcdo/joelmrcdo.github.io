---
layout: post
title: "Option Price Target Calculator"
date: 2025-06-04 00:00:00 +0000
---

<style>
.calc-box {
  max-width: 450px;
  padding: 1em;
  border: 1px solid #ddd;
  background: #f9f9f9;
}
.calc-box label {
  display: block;
  margin-bottom: 0.5em;
}
.calc-box input,
.calc-box select {
  width: 100%;
  padding: 0.3em;
  margin-top: 0.2em;
}
.calc-box button {
  margin-top: 0.5em;
  padding: 0.4em 1em;
}
</style>

<p>This calculator estimates the value of a plain vanilla option at a profit
 target or stop level using the Black‑Scholes formula.</p>

<div class="calc-box">
  <label>Current underlying price
    <input id="underlying" type="number" step="any" />
  </label>
  <label>Target price
    <input id="target" type="number" step="any" />
  </label>
  <label>Stop price
    <input id="stop" type="number" step="any" />
  </label>
  <label>Strike price
    <input id="strike" type="number" step="any" />
  </label>
  <label>Days until expiry
    <input id="days" type="number" step="any" />
  </label>
  <label>Implied volatility (%)
    <input id="iv" type="number" step="any" />
  </label>
  <label>Risk‑free rate (%)
    <input id="rate" type="number" step="any" value="5" />
  </label>
  <label>Option type
    <select id="otype">
      <option value="call">Call</option>
      <option value="put">Put</option>
    </select>
  </label>
  <label>Delta override (optional)
    <input id="delta" type="number" step="any" />
  </label>
  <label>Gamma override (optional)
    <input id="gamma" type="number" step="any" />
  </label>
  <label>Theta override/day (optional)
    <input id="theta" type="number" step="any" />
  </label>
  <label>Vega override (optional)
    <input id="vega" type="number" step="any" />
  </label>
  <button id="calc">Calculate</button>
  <pre id="result"></pre>
</div>

<script>
function normCdf(x) {
  var t = 1 / (1 + 0.2316419 * Math.abs(x));
  var d = 0.3989423 * Math.exp(-x * x / 2);
  var prob = d * t * (0.3193815 + t * (-0.3565638 + t * (1.781478 + t * (-1.821256 + t * 1.330274))));
  return x >= 0 ? 1 - prob : prob;
}

function normPdf(x) {
  return Math.exp(-0.5 * x * x) / Math.sqrt(2 * Math.PI);
}

function blackScholes(S, K, T, r, sigma, type) {
  var d1 = (Math.log(S / K) + (r + 0.5 * sigma * sigma) * T) / (sigma * Math.sqrt(T));
  var d2 = d1 - sigma * Math.sqrt(T);
  if (type === 'call') {
    return S * normCdf(d1) - K * Math.exp(-r * T) * normCdf(d2);
  }
  return K * Math.exp(-r * T) * normCdf(-d2) - S * normCdf(-d1);
}

function blackScholesGreeks(S, K, T, r, sigma, type) {
  var d1 = (Math.log(S / K) + (r + 0.5 * sigma * sigma) * T) / (sigma * Math.sqrt(T));
  var d2 = d1 - sigma * Math.sqrt(T);
  var pdf = normPdf(d1);
  var delta = type === 'call' ? normCdf(d1) : normCdf(d1) - 1;
  var gamma = pdf / (S * sigma * Math.sqrt(T));
  var vega = S * pdf * Math.sqrt(T) / 100; // per 1% IV
  var thetaTerm = -(S * pdf * sigma) / (2 * Math.sqrt(T));
  var theta;
  if (type === 'call') {
    theta = (thetaTerm - r * K * Math.exp(-r * T) * normCdf(d2)) / 365;
  } else {
    theta = (thetaTerm + r * K * Math.exp(-r * T) * normCdf(-d2)) / 365;
  }
  return { delta: delta, gamma: gamma, vega: vega, theta: theta };
}

document.getElementById('calc').addEventListener('click', function() {
  var S = parseFloat(document.getElementById('underlying').value);
  var target = parseFloat(document.getElementById('target').value);
  var stop = parseFloat(document.getElementById('stop').value);
  var K = parseFloat(document.getElementById('strike').value);
  var T = parseFloat(document.getElementById('days').value) / 365;
  var sigma = parseFloat(document.getElementById('iv').value) / 100;
  var r = parseFloat(document.getElementById('rate').value) / 100;
  var type = document.getElementById('otype').value;
  var deltaOverride = parseFloat(document.getElementById('delta').value);
  var gammaOverride = parseFloat(document.getElementById('gamma').value);
  var thetaOverride = parseFloat(document.getElementById('theta').value);
  var vegaOverride = parseFloat(document.getElementById('vega').value);

  var current = blackScholes(S, K, T, r, sigma, type);
  var greeks = blackScholesGreeks(S, K, T, r, sigma, type);
  if (!isNaN(deltaOverride)) greeks.delta = deltaOverride;
  if (!isNaN(gammaOverride)) greeks.gamma = gammaOverride;
  if (!isNaN(thetaOverride)) greeks.theta = thetaOverride;
  if (!isNaN(vegaOverride)) greeks.vega = vegaOverride;

  var approxTarget = current + greeks.delta * (target - S);
  var approxStop = current + greeks.delta * (stop - S);

  var targetVal = blackScholes(target, K, T, r, sigma, type);
  var stopVal = blackScholes(stop, K, T, r, sigma, type);

  document.getElementById('result').textContent =
    'Current option price: ' + current.toFixed(2) + '\n' +
    'Delta: ' + greeks.delta.toFixed(4) + ' Gamma: ' + greeks.gamma.toFixed(4) + '\n' +
    'Theta/day: ' + greeks.theta.toFixed(4) + ' Vega: ' + greeks.vega.toFixed(4) + '\n' +
    'Approx at target (delta): ' + approxTarget.toFixed(2) + '\n' +
    'Approx at stop (delta): ' + approxStop.toFixed(2) + '\n' +
    'Price at target (BS): ' + targetVal.toFixed(2) + '\n' +
    'Price at stop (BS): ' + stopVal.toFixed(2);
});
</script>

**Financial disclaimer:** This material is for educational purposes only and is not financial advice.

