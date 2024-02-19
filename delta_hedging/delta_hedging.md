## [Home](../README.md)

# Vega and Gamma 

How do vega and gamma play a role when delta hedging?

To capture volatility mispricing and to avoid exposure on the direction of the underlying we should keep a delta neutral position. Let's start with a simple call with delta $\Delta $, our hedged position is

$$ C - \Delta S_t $$

If $S_t \rightarrow S_{t+1} $ then

$$\text{PL} \approx C(S_{t+1}) - C(S_{t}) - \Delta(S_{t+1} - S_{T})$$

If we taylor expand:

$$ C(S_{t+1}) = C(S_t) + \Delta (S_{t+1} - S_{t})$$
$$ + \frac{1}{2}(S_{t+1}-S_{t})^2\Gamma$$

$$\rightarrow \text{PL} \approx \frac{1}{2} (S_{t+1}-S_{t})^2\Gamma $$

Also on average 

$$ (S_{t+1}-S_{t})^2 \approx \sigma^2 S^2$$

$$\rightarrow \text{PL} \approx \frac{1}{2} \sigma^2 S^2\Gamma $$

If implied volatility is mispriced than we make:

$$\text{PL} \approx \frac{1}{2} S^2\Gamma(\sigma^2 - \sigma_{\text{implied}}^2) $$

Also, since $vega = \frac{\partial C}{\partial \sigma_{iv}}$, we get that 

$$\text{PL} = \text{vega}(\sigma - \sigma_{\text{implied}})$$ 

$$\rightarrow\text{vega}(\sigma - \sigma_{\text{implied}}) \approx \frac{1}{2} S^2\Gamma(\sigma^2 - \sigma_{\text{implied}}^2)$$ 

$$\rightarrow\text{vega}\approx \frac{1}{2} S^2\Gamma(\sigma + \sigma_{\text{implied}})$$ 

$$\rightarrow\text{vega}\approx  S^2\Gamma\sigma$$ 

This is the instant value, since an option has time to maturity t we should multiply the RHS by t.

$$\text{vega}\approx  S^2\Gamma\sigma t$$ 

In general, if we earn x because IV went up and we are long vega, then we would earn the same x with delta hedging thanks to gamma if realized vol = IV. 