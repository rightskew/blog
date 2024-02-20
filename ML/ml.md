## [Home](../README.md)

# Good ML 

Here I list some good ML practices, maily found in the De Prado book.
- Using candles
- Feature selection
- Siloing 
- Fractional differences
- Weak learners
- Adverse selection
- Backtest

## Using candles

We start with the first step of every machine learing pipeline: data analysis. The most common way to store data is with HLOC time candles, for each x time we group the highs, lows, open and closing price of that candle. This good because it's simple, and also gives a bound for "how slow" the algorithm can be (if we trade 5 min candles, our algorithm can take up to 5 minutes to run). The downside is that we use a non linear scale: is a weekend the same as a weekday? Is the morning opening the same as a tuesday afternoon? After a earning, time will briefly accelerate, and slow down after a while. Velocity of the market is proportional to the flow of information. How can we adjust for that?

- volume candles: group candles by volume traded. Pro: also very simple, volume is a decent proxy for information. Con: doesn't take in account the price (trading a 1 euro stock is not the same as trading SP).
- price candles: group candles by euros/dollars traded. Pro: solves the volume problem. Con: doesn't take in account the distribution (ex. SP crashing has more information than SP going back and forth)
- entropy candles: mark up movements as 1 and down movements as -1, the group by candles with the same amount of information content (with the idea that 1,1,1,1 has more information than 1,-1,1,-1 and the same as -1,-1,-1,-1). Pro: trend/asymmetry is better proxy of information Cons: more complex and doesn't take into account volume and price
- price entropy candles: weight the information per bit by return*volume. It's also possible to bin them. Pro: maybe the best way to capture information flow. Cons: very complex


## Feature selection

When selecting features, we should select the relevant features before doing any multi (linear) regression. This means testing first each feature separately, and using LASSO afterwards (Ridge is probably better because collinearity is hard to get rid). Also, when training we should use a trailing window with fixed size. In this way, the training set does not grow as time goes on but the problem is that we are reusing features (training sets intersect). We should therefore: use EWMA and penalize feature intersection. If we can weight the importance of a feature, we should weight it less if its being reused, and more if it influences the pnl. If we can weight the points, we should weight more the points that influence more the pnl (ex liquidity).

## Siloing:

A general good practice is siloing: to divide the machine learning pipeline in small, testable units. I'll make 2 examples (that are in the De Prado book):

1) feature test: instead of testing a set of features in a multi linear regression, test each one singularly.

2) meta labeling: instead of optimizing for trade selection + trade size at the same time divide the process in 2 steps:

 - First, train a high recall method (or not use ml at all) to find all the points in time where you wanted to trade. Example: you are trading a mean reverting asset, you mark a 1 when the price is too high (crosses the upper band) and as -1 when the price is too low (crosses the lower band). Low and high should be proportional to the standard deviation, computed using a trailing EWMA.

 - Second, for each selected trade, apply the sigmoid function to the PNL of that trade. Then train a logistic regression on the selected trades (using some features) to match the pnl distribution using the cross entropy loss. A 1 should loosely say that we always want to do that trade, 0 never. With the probability distribution we can also size it using Kelly. 


## Fractional differences

 I never used this method, but it looks interesting. When we want to model a time series, often we need it to be stationary (like if want to use an ARMA). To get a stationary time series we usually take the returns so $R_t = S_t - S_{t-1}$, or, using the backshift operator, $BS_t = S_{t-1} \rightarrow R_t = (1-B)S_t$. If this is not stationary, we can take the returns of the returns  $(1-B)^d S_t$, but as we continue down this path we lose more and more memory/autocorrelation. But what if taking the returns was not necessary, and we could just take $d<1$ and still make it stationary? 

 To take $(1-B)^d S_t$ with $d<1$ we Taylor expand it using B as a variable and get $\sum_{k=0}^\infty (d, k) (-B)^k$. In practice we don't have an infinite time series so we take just the first n terms and get we the weight w recursively $w_k = -w_{k-1}\frac{d-k+1}{k}$. Apparently d = 0.2 is enough for many time series, and removes less memory from the process.

## Weak learners

Another good idea is to use the "linearity of edge": the sum of weak signals is a strong signal. 

- bagging: we train in parallel many weak learners, on random subsets of the training set using random feature(s). We reduce both variance and bias. It's very fast and doesn't overfit much. But not super precise.
- boosting: we train sequentially many weak learners, reducing the residuals of the preciding learner. We reduce variance greatly, but it's more prone to overfit. It's less fast (but still fast with LighGBM/XGBoost).

We should use them both: my intuition is that we should boost manually and bag automatically. Ex: we take a time series, instead of predicting it directly, we predict the residuals of the time series - (some historical mean + seasonality + trend), and to do that we use bagging on a set of well tested features. 

## Adverse selection

Adverse selection risk is the reason why market makers exist. The spread is the risk premium associated with the information flow of the takers. In a sense, it's like option selling: you take a risk and you get payed for it.
Using this, if we can estimate the magnitude of the (toxic) information flow, we can compute the optimal spread. One proxy for the IF is the taker volume: VIP$=\frac{\alpha_t \mu}{\alpha_t\mu+2\epsilon}$ where $\mu$ is the rate of arrival of informed traders, $\epsilon$ is the rate of arrival of uninformed traders and $\alpha$ is the probability of an informational event. It is also the volume imbalance $E(\lvert 2V^B_t-1\rvert)$ so if we have a candle with fixed volume V, VPIN=1 if all orders are buy, and 1 if all orders are sell. In practice, this also grows with the increase of variance (because the informed trader can "hide" better), and with a decrease of uncertainty in the asset (so he can bet more and be more sure about the outcome). Another estimate for VPIN (I guess in HF scenario) is VPIN $\approx \frac{\sum_{i=1}^t\lvert V_t^B - V_t^S\rvert}{\sum_{i=1}^t V_t^B + V_t^S}$

Another way to quantify adverse selection is to fit a ML model, and when the prediction stops being good, then it means new information arrived. In practice we can fit a logistic regression using a matrix of feature X with all the important metric (VPIN, asymmetry ecc) over a vector Y of pnl; so for each trade, given its risk characteristic we predict wheter it will lose or make money, meta labeling style. Then we use this classifier to classify incoming trades. If the classifier has a low cross entropy loss, then we are doing good, and the flow is not carring much information. Otherwise, our method of estimating the spread is not doing a good, the IF is stronger than predicted, we must back up and make the spread bigger.

## Backtest

De prado talks a lot about backtesting. The salient points are the following:

- always backtest last. Backtest should not be part of the model, it's just a sanity check of the parameters. If the backtest fails once, note it and start from scratch

- when perfoming a grid search, instead of trying out every possible parameter (which could take a lot of time), just sample a set of parameters from a set of fixed distributions (they don't need to be uniform)

- instead of training up to x_t and test on $x_{t+1}$, also test on $x_{t+2}$ until $x_{T}$. If performance drops too soon, maybe we are using a wrong time scale. Also we can test on the reverse dataset or do a combinatorial split, with the idea that the performance shound't drop too much. If it drops, then we were probably overfitting 