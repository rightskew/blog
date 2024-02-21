### What is the price of an ATF call if $\sigma$ tends to infinity?

Let's assume S=F=100.
The put can at most be worth the strike S, and if volatility tends to infinity then the ATF put is worth S. Using put call parity: C-P=F-S we get that C=F (note, the put and the call completely divide into the F and S factors).
Now, how much is worth the 0 call strike? The answer is again F. This is true for 2 reasons:
1) the 0 strike put is worth 0, so by put call parity the call is again worth F
2) the 0 strike call will always be exercised, therefore it's value is exactly the underlying

This is true no matter the volatility.
But then, how much is the 0-100 call spread worth? The answer is 0 (both calls are worth the same). This looks like a paradox, as the 0-100 call spread is worth 100 if the underlying exceeds 100, and a number between 0 and 100 if the underlying goes under 100.
The reason is the following: as volatility goes to infinity, the mean of the log-normal distribution also goes to infinity. For the mean of the log-normal to be fixed at 100 (the forward value), we need its median to be closer and closer to 0. We have that the probability of the underlying being 0 is 1-$\epsilon$ and the probability of being >0 is $\epsilon$. The payoff of the call spread is therefore less than (100-$\epsilon$)*0 + $\epsilon$*100. As $\sigma \rightarrow \infty, \epsilon \rightarrow 0$ , so having a limited upside makes the payoff 0.
Therefore, every call option has a value of F and every put a value of S.


### Should we buy A or B?

Le't assume A=B=100. Let's also assume they follow a lognormal distribution: their value is:

$$ 
A = E(e^{\mathcal{N}(\mu_A, 5^2)}) 
$$
$$ 
B = E(e^{\mathcal{N}(\mu_B, 25^2)})
$$

So we get that the mean of the exponent is:

$$
100 = e^{\mu_A + 0.05/2} \rightarrow \mu_A \approx 4.58
$$
$$
100 = e^{\mu_A + 0.25/2} \rightarrow \mu_B \approx 4.48
$$

If we increase the volatility of A to 25% we then get

$$
A_{\text{leveraged}} = e^{4.58 + 0.25/2} = 110
$$

So A is worth more than B!


### And what if we increase the volatility by going to the casino?

If the owner goes to the casino the value of A is now:

$$
A = \frac{1}{2}101 + \frac{1}{2}99 = 100
$$

If he keeps betting 1/100 of the value of A we get a binomial series, which still sums up to 100.

The difference is that in the first we sample the logarithmic returns, in the second we sample the geometric returns. For small numbers (low vol) there is no difference, but as we increase vol we increase the divergence between the 2.