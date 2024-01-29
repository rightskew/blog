# Measuring volatility

Volatility is technically defined as the standard deviation of the logarithmic returns.
It's not directly observed, one needs to estimate it by doing some sort of sampling. We'll show a classic, a fast and an alternative way to estimate it.

## The classic

The variance estimation is:

$$\sigma^2 = \frac{1}{N}\sum_{i=1}^{N}(X_{i}-\bar{X})^2 \approx  \frac{1}{N}\sum_{i=1}^{N}X_{i}^2 $$

This is a biased estimator, as we are using one degree of freedom to estimate the mean (which is often assumed to be 0). The unbiased population variance estimate is

$$\sigma^2 =  \frac{1}{N-1}\sum_{i=1}^{N}X_{i}^2 $$

And taking the square root leads to


$$\sigma = \sqrt{ \frac{1}{N-1}\sum_{i=1}^{N}X_{i}^2}$$

which is again a biased (systematically low) estimation! It turns out that it's not possible to have an estimator that is unbiased both for the standard deviation and for the variance (Jensen):

$$ E(s)=E(\sqrt{s^2})<\sqrt{E(s^2)}=\sqrt{\sigma^2}=\sigma $$

For large N this is not a problem, but for small N we need to divide by a factor $b(N) \approx \sqrt{1-\frac{3}{2N}}$ to get the correct population standard deviation (for more see Volatility Trading by Sinclair) and get:

$$\sigma = \frac{1}{b(n)}\sqrt{ \frac{1}{N-1}\sum_{i=1}^{N}X_{i}^2}$$

This estimator converge quite slowly to the correct value and a confidence level is $var(\sigma)\approx \frac{\sigma^2}{2N}$.

## The fast
Squared returns are difficult to compute for a trader, but he might know what the mean absolute return is:

$$ E(\lvert R\rvert) = \frac{1}{N-1}\sum_{i=1}^{N}\vert X_{i}\rvert  $$

Again, for Jensen, this is a lower bound for the actual volatility. If we assume that the returns are normally distributed then we can get a decent estimate as:
$$E(\lvert R\rvert) = \sqrt{\frac{2}{\pi}}\sigma  \approx 0.8 \sigma$$

Therefore, if we know the average daily move and want to know how much is the annual standard deviation, we can get it by multiplying the daily std by 16 $(\sqrt{252})$:

$$\sigma_{annual} \approx \frac{16 \cdot \text{average daily move}}{0.8 \cdot S} = 20 \frac{\text{average daily move}}{S} $$

## (One) alternative

The close to close estimation is not using the candles fully, as it only looks at the close. The Parkinson estimator uses the highs and lows instead:

$$\sigma = \sqrt{\frac{1}{4N\log(2)} \sum_{i=1}^N\log(\frac{h_i}{l_i})^2} $$

This estimator converges faster (5 times) than the close to close, but they both share the same weakness: we are only looking at a partial realization of the time series. They will be both biased low as the large movements ( or the highest high and the lowest low) will be likely outside our sampling window. We can only increase our sampling window by a finite amount, as volatility is always changing and there is a very steep diminishing return in the amount of information we get. Including a black swan event in our sampling window can change our estimation by quite a bit, but we can't afford to sample over the past 10 years, as that will only give us an average value of vol, and not how much it's now.

# Relativity

This last observation leads to another important fact: volatility is relative. "Instantanous" volatility exists only in theory, in practice it depends on the sampling window, the sampling frequency and also on how we can actually trade it!

We touched on the importance of the sampling window, but what about the sampling frequency?
Is it better to sample each minute, each hour or each day? Clearly, if we sample more often we can get a better estimate of the "instant" volatility, but we get also more noise. It would be better to use all three and compare them: if the high frequency (say hours) volatility is higher than the low frequency (say days) volatility it probably means that the underlying is mean reverting (every few hours). If the low frequency vol is higher than the high frequency vol, then there is some trend.

Furthermore, we are not trading volatility in the vacuum. To capitalize on cheap (or rich) volatility we want to buy (or sell) options and [delta hedge](../delta_hedging/delta_hedging.md). Now, there are two problems: 
- the perceived volatility is correlated with the hedging frequency
- there is no "fixed price", the underlying has a bid/ask spread

To exploit the mean reversion/trending fenomenom, when long gamma one could delta hedge less if the underlying is trending (to get a "relative" higher vol), or more often if it's mean reverting (and the opposite when short gamma). If we don't have a strong opinion on the trend, we can say that we hedge when our delta position exceed a certain value (see chapter 4 of Sinclair).

However, the underlying that we use to delta hedge doesn't have a fixed price, it has a bid/ask spread. When we delta hedge a long gamma position we can "sit" on the bid/ask spread (so we have an outstanding limit order down the order book), and when get filled it meas that the price moved against us (and so when we got filled we hedged). We didn't pay the spread (good) but if we assume that the price was (bid+ask)/2 and we got filled at bid, it means that the price moved by just spread/2, so the volatility was low (bad, we are long gamma). Likewise, if we are short gamma we can't sit on the bid/ask spread, and we need to chase the spot. If the underlying moves by spread/2, we can only get realistically filled at old ask + spread/2 by crossing the spread (bad) and so the price for us moved by spread, so the volatility was higher (bad, we are short gamma).
The "perceived" volatility is:

$$ \sigma_{\text{long}} = \sigma \sqrt{1-\frac{\lambda}{\sigma}\sqrt{\frac{8}{\pi\Delta t}}} $$

$$ \sigma_{\text{short}} = \sigma \sqrt{1+\frac{\lambda}{\sigma}\sqrt{\frac{8}{\pi\Delta t}}} $$

where $\lambda$ is the proportional transaction cost and $\Delta t$ is the time between rebalancing.