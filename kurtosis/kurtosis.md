## [Home](../README.md)

# What is the kurtosis?


The kurtosis is technically defined as the fourth normalized moment of the distribution. 

$$ \text{Kurtosis}:=E((\frac{x-\mu}{\sigma})^4)$$

or by sampling
$$ \mu_4 \approx \frac{1}{N}\sum_{i=1}^{N}\frac{(x_i-\bar{x})^4}{\sigma^4}$$

It is 3 for a normal distribution, distributions with higher values are called leptokurtic (and platikurtic the reverse). It rappresents how "fat" are the tails, and also how much "dispersed" are the returns squared. For a normalized distribution Z:

$$ \mu_4 = E(Z^4) = Var(Z^2)+ (E(Z^2))^2$$
$$ =Var(Z^2) + Var^2(Z) = Var(Z^2) +1 $$

Now, there are two questions:
* why is the the volatility curve smiling?
* how can we profit from it?

## Why is the volatility curve smiling?
There are three main reasons why OTM options are expensive:
* volatility is not constant
* volatility is correlated with spot
* returns have fat tails

If returns were all sampled from the same constant distribution, the sum would still have no kurtosis. However, volatility is always changing. The volatility of volatility makes some points concentrate around the mean, and some very far into the tails. If we sample from $\mathcal{N}(0, \sigma^2)$ where $\sigma$ is not fixed, but sampled from a log normal with standard deviation $\bar{\sigma}$, as we increase $\bar{ \sigma} $ we also increase the kurtosis:

![kurt1](kurt1.jpg)

Again, this is another of the ideas behind the Henston model:

$$dX_t = X_t(mdt+\sigma_tdW)$$

$$d(\sigma_t^2)=\Omega\sigma(\sigma_t^2-\sigma_0^2)dt+\gamma\sigma_tdW' $$

Increasing $\gamma$ will increase the kurtosis.

The second reason is double: first, if both returns and volatility are random, then a big returns most likely comes from high volatility, thus the tails have the thickness of the higher volatility (and the inverse for small returns). Second, returns are skewed, and the skew will implicitly introduce some kurtosis. In fact, skew bounds kurtosis from below:
$$\text{Kurtosis} > \text{Skew}^2 +1$$

The third reason is the following: returns are not gaussians. It happens to see $20\sigma$ movements (which would never happen if they were truly gaussian). The tails in particular behave more power law like $f(x) \approx \alpha c^{-x}$. The CLT theorem would say that if we convolute this distribution with itself enough times then we would get something that resembles a gaussian. The problem is that only the central region will be a gaussian, the tails will continue to act as power law! (althought the "central" region will grow with $N^{\frac{3}{4}}\sigma$).
A nice story that explains why returns are should not be modelled as gaussian is the following:
*A farmer uses a rivulet of water behind his house to water the plants. He want to estimate the distribution of the water that comes day each day. Being a good statistician, he uses the Gaussian distribution to model it. After 100 days of measuraments, the mean is around 400 liters per day, with a standard deviation of 50 liters. He goes into the market, and sells 600 strike calls, being quite sure that they will never be exercised. One day, another farmer goes near the start of the river, and removes a big tree that was blocking the stream. The day after, the poor farmer registers 4000 liters of water, his plants are all dead and he just lost all of his savings on the market.*


## How can we profit from it?

Like for the skew, we should buy kurtosis when it's cheap and sell when it's rich. Therefore, we should buy if we think that vol of vol is underestimated or there is a black swan event around the corner, and sell if we think that vol will be quite stable in the future. A classic way to gain kurtosis exposure is with a short straddle and 2 long strangle.

![kurt2](Kurt2.jpg)

Here we want either very small movements (to profit from the short straddle) or very large (to profit from the long strangle). Therefore we profit from volatility dropping or increasing faster than the market expects.