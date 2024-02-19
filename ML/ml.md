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