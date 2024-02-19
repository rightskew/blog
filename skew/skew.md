## [Home](../README.md)

# What is the skew

The skew is technically defined as the third normalized moment of the distribution. 

$$ \mu_3:=E((\frac{x-\mu}{\sigma})^3)$$

or by sampling
$$ \mu_3 \approx \frac{1}{N}\sum_{i=1}^{N}\frac{(x_i-\bar{x})^3}{\sigma^3}\approx \frac{1}{N}\sum_{i=1}^{N}\frac{\log(\frac{x_i}{x_{i-1}})}{\sigma} $$.

It is zero for a normal or a symmetric distribution, and in general has the same sign as the correlation between $\log(\frac{x_i}{x_{i-1}})$ and $\log^2(\frac{x_i}{x_{i-1}})$. Now, there are two questions:
* why is the smile skewed?
* how can we profit from it?

## Why is the smile skewed? 

There are three main reasons why the smile presents a skew
* downside protection is attractive
* volatility is correlated with spot
* the returns are not geometric

The first reason is pretty self-explanatory. Investors are long the underlying and to protect themselves against downside risk they buy out of the money put, increasing the value. 

The second reason come from the fact that (in equities at least) the returns are negatively correlated with volatility. After a big movement, we expect volatility to increase, possibly exercibating the return. This is also the idea behind the Henston model:

$$dX_t = X_t(mdt+\sigma_tdW)$$

$$d(\sigma_t^2)=\Omega\sigma(\sigma_t^2-\sigma_0^2)dt+\gamma\sigma_tdW' $$

where $dW$ and $dW'$ are correlated (and create the skew). $\Omega$ is the mean returning parameter, and create the kurtosis together with $\gamma$.

The third reason is a bit more subtle. Usually, when talking about returns, we assume them to be multiplicative factors. So, when compouding, we get the log normal distribution $e^{\mathcal{N}(\mu,\sigma^2)}$. This distribution has a skew $\approx 3\sigma$: the median needs to remain constant at $e^{\mu}$ (because the returns are symmetric), but the mean is shifted by $\frac{\sigma^2}{2}$ to the right, to account for the mode, [which is $\sigma^2$ to the left](https://www.youtube.com/watch?v=UtpLE6npxvk). This effect is also called volatily drag (Delta chapter of Dynamic Hedging by Taleb), and comes from the fact that if you gain 10% and lose 10% you are not back at 100% $(1-x)(1+x)=1-x^2$, so the most common value cannot be the median. This also tells us that the price is not a martingale, to satisfy the martingale property we need to shift by $-\frac{\sigma^2}{2}$. Now, what if the returns are not proportional to the price?
This happens often for short-scale returns. The price is now no longer correctly modelled by $e^{\mathcal{N}(\mu,\sigma^2)}$ but more like $\approx \mu+\sum_{t=1}^{N}\mathcal{N}(0,\sigma^2)$, which is not a log normal but a normal distribution. If the returns have skew 0 then the price has skew $3\sigma$, therefore if the price has skew 0 the log price has negative (shadow?) skew! See chapter 8 of Theory of financial risk and derivative pricing by Bouchaud for a better explanation.

## How can we profit from it?

Like any other thing, we can profit by buying skew when it's cheap and selling it when it's rich. To be long skew, means that we profit when the volatility becomes more tilted. A classic way to gain skew exposure is with a collar, where we bet on skew becoming more negative.

![skew1](Skew.jpg)


Let's consider a delta hedged collar: if the skew becomes more negative the price of our OTM put increases, and the OTM call decreases, making us money. In general, when we buy an OTM put and sell an OTM call, we are in debt (the put is more expensive). This means that we are paying some kind of theta as time passes (like for a long gamma position). In a long gamma position, theta is the amount we would earn if realized vol = implied vol. In a long skew position (a collar, or any vertical spread), theta is the amount we would earn if realized skew = implied skew, in practice realized $\frac{dVol}{dSpot} =$ implied$\frac{dVol}{dSpot}$. Skew is locally tracked by Vanna 

$$\text{Vanna} = \frac{\text{dDelta}}{\text{dVol}}=\frac{\text{dVega}}{\text{dSpot}}$$

but if dVol $\approx$ dSpot then 

$$\text{Vanna} \approx \frac{\text{dDelta}}{\text{dSpot}} = \text{Gamma}$$

The similarity between Vanna/Gamma is also reflected in the delta hedging process:
- if market rises $\rightarrow$ vol goes down $\rightarrow$ we make money because our short call (which is now closer) loses value $\rightarrow$ we make money, so we will need to sell high to be hedged
- if market falls $\rightarrow$ vol goes up $\rightarrow$ we make money because our long put (which is now closer) increases in value $\rightarrow$ we make money, so we will need to buy low to be hedged

We are making money if market moves and vol is negatively correlated with spot, the more correlated the better. So when should we buy skew?

Like gamma, having a long skew position is unprofitable. Let's define 4 regimes:

1) Sticky delta: volatility surface moves parallel wrt spot (spot goes up by 1, vol surface also moves 1 to the right). Here $\frac{dVol}{dSpot} > 0$. Buying skew is bad. This regimes is realistic only for long date maturities (where skew is v low). 

2) Stick strike: once vol surface is defined by strike, it remains fixed in place as spot moves. Here $\frac{dVol}{dSpot} = 0$. Buying skew is bad as well. This regime is realistic for small movement is a calm market.

3) Local vol: vol surface adapts to the market, spot moving influences the vol. Here $\frac{dVol}{dSpot} <0$. Skew should be fairly priced. This regime is the most common.

4) Jumpy market: every spot move influence greatly the vol surface. Every little down movement makes the vol skyrocket. Only regime where buying skew is good as $\frac{dVol}{dSpot} << 0$. If we are in a jumpy market and spot drops, it might make sense to buy OTM puts and sell OTM call (wrt to the new price).

Just remember that skew cannot be too negative or too positive for a no arbitrage consideration. Also, IV is weakly bounded from above by the historical highs of IV (say 2008 crash), so when IV is very high skew needs to be lower; and vice versa, low strike options are sticky, so a decrease in IV increases the skew.

## Term structure

Long dated options are less sensible to movements in spot than short dated ones. Since vol tends to mean revert in about 8 months, a 2 year IV will be quite close to an historical mean. So what can we say about the 80, 100 and 120% moneyness line?
The 80% line will have an inverted term structure, the 100% will have a flat term structure and the 120% will have a upward sloping term structure. If we end up at 80% tomorrow, it means there was a big drop, so vol should be much higher in the short term, and mostly unchanged in 2 years (or maybe just a bit higher). The same but opposite for 120%, if markets are doing good today we don't expect much more vol rn, but in the long term we do.

Assuming sticky strike, short term dated options need to have a higher skew than long dated ones, because the correlation vol/spot is higher. A rule of thumb is that this correlation (or skewness in general) decays as $\approx\frac{1}{\sqrt{t}}$


## How to measure

The easiest way to measure skew is by taking 90%Put and subtract the 100% (or 110%) call. Note that we don't divide by volatility bc to get 90% moneyness we already normalized by vol. A slightly better way would be 25 Delta put - 25 Delta call / 50 delta.

## Extra: Skew is weird



Qs:

1) Say A is worth 100 and it's volatility is $\sigma$. 
- How much is the ATM call worth for $\sigma \rightarrow \infty$? (assume no interest rates)
- How much is the 0 strike call worth?
- How much is the 0-100 call spread worth? 

2) Say A and B both are worth 100, and A has 5% vol and B has 25% vol. 
- If we can buy a leveraged A such that its vol is now 25%, which would you buy? Leveraged A or B? (assume no fees)
- If the owner of A goes to the casino and bets 1/100 of the company on a (fair) coin toss, is A worth more or less?
- What if he does it a milion times?