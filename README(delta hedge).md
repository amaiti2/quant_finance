# Delta Hedging with Black–Scholes, GARCH, and CEV

This project compares the hedging error of three option models on AAPL, AMZN, and META: Black–Scholes (BS), a GARCH(1,1)-based model, and a constant elasticity of variance (CEV) model. A self-financing stock-and-cash portfolio is rebalanced daily, and its terminal value is compared with the option payoff. The analysis uses mean absolute error (MAE), root mean squared error (RMSE), and the distribution of hedge errors to compare the models.

## Hedging setup

For a trade date, let \(S_0\) be the initial stock price, \(m\) the moneyness, and \(H\) the maturity in trading days. The strike is \(K=mS_0\), and the initial time to maturity in years is \(\tau_0=H/252\). The portfolio holds \(\Delta_t\) shares and cash \(B_t\):

\[
\Pi_t=\Delta_tS_t+B_t, \qquad B_0=V_0-\Delta_0S_0.
\]

At daily rebalance \(j\), the cash account accrues at rate \(r\) and pays for changes in the stock position:

\[
B_j=B_{j-1}e^{r/252}-(\Delta_j-\Delta_{j-1})S_j.
\]

At maturity, the **hedge error** is the terminal portfolio value minus the option payoff, \(e=\Pi_T-\mathrm{Payoff}(S_T)\). For \(N\) observations:

\[
\mathrm{MAE}=\frac{1}{N}\sum_{i=1}^{N}|e_i|,
\qquad
\mathrm{RMSE}=\sqrt{\frac{1}{N}\sum_{i=1}^{N}e_i^2}.
\]

## Models

| Model | Assumption in the presentation | Mathematical specification |
|:--|:--|:--|
| BS | Constant volatility | \(dS_t=(r-q)S_t\,dt+\sigma S_t\,dW_t\); call delta \(\Delta_{\mathrm{BS}}=e^{-q\tau}N(d_1)\) |
| GARCH(1,1) | Conditional volatility changes over time | \(R_t=\mu+\varepsilon_t\), \(\varepsilon_t=\sigma_tz_t\), \(\sigma_t^2=\omega+\alpha\varepsilon_{t-1}^2+\beta\sigma_{t-1}^2\) |
| CEV | Volatility depends on the stock level | \(dS_t=(r-q)S_t\,dt+\delta S_t^\beta\,dW_t\); return volatility \(\delta S_t^{\beta-1}\) |

Here \(q\) is the dividend yield, \(N\) is the standard normal CDF, and \(W_t\) is Brownian motion. The slides state the BS delta but do not spell out how GARCH and CEV deltas were computed.

## Results

Each model–ticker row in the accompanying summary reports **1,487 observations**. Values below are hedge-error statistics; lower MAE and RMSE are better.

| Ticker | Model | MAE | RMSE | Mean error | Error std. dev. |
|:--|:--|--:|--:|--:|--:|
| AAPL | BS | 1.290700 | 2.066591 | -1.023956 | 1.795684 |
| AAPL | GARCH | **1.231579** | **1.682385** | 0.203152 | 1.670636 |
| AAPL | CEV | 2.192815 | 3.297051 | -2.126167 | 2.520760 |
| AMZN | BS | 1.861535 | 2.739732 | -1.709561 | 2.141638 |
| AMZN | GARCH | **1.636123** | **2.191411** | 0.546492 | 2.122889 |
| AMZN | CEV | 2.726122 | 4.132901 | -2.600026 | 3.213671 |
| META | BS | **5.296459** | 8.615297 | -5.237058 | 6.843103 |
| META | GARCH | 5.324906 | **8.355723** | 2.248248 | 8.050284 |
| META | CEV | 7.803202 | 11.917306 | -7.760020 | 9.047614 |

GARCH has the lowest RMSE for all three tickers and the lowest MAE for AAPL and AMZN. BS has a marginally lower MAE on META. CEV has the largest MAE and RMSE in every comparison. The slides also show error distributions, boxplots, and cumulative hedge-error plots; their commentary describes larger negative tails for CEV and more stable cumulative errors for GARCH.

## Scope

These are the reported results of a historical hedging comparison, not a live-trading evaluation. The slides do not give a full data specification, parameter calibration procedure, transaction-cost model, or reproducible implementation. The evaluation design should be documented before using these results to claim out-of-sample performance. A cumulative **hedge error** should not be read as realized strategy profit.

**Sources:** *slides (quant)(1).pdf* (mathematical setup, model formulas, charts, and rounded results); *summary_pdf(20260924-071908).pdf* (full-precision statistics and observation counts). Ayan Maiti, March 2026.
