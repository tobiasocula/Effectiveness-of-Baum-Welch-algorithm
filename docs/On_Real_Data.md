## Using Baum-Welch on financial time series returns

I wanted to see whether the same principle of the Baum-Welch algorithm could work in a real situation, not just on simulated data. For this, I downloaded some equity returns from ETFs from various markets.

The idea is the same as with the weather model, but now the latent states represent specific market regimes instead of weather conditions. I have chosen to let the regimes represent volatility ranges specifically, since I found that this is standard in finance. The first regime corresponds with low volatility, the second with medium volatility and the third with a high-volatility market environment. The emissions are, naturally, equity returns.

Because we don't have access to the "real" volatility regimes, we cluster the returns by volatility measure, using k-means. According to this clustering we compute the means and covariances of the returns within each basket.

After running the main algorithm we obtain the parameters $(A,B,\pi)$.

We then may construct an estimated regime sequence based on the obtained estimations. If we denote $\alpha_t(k)$ as the probability of being in regime $k$ at time $t$, we can calculate recursively

$\alpha_t(k)=B_{t,k}\sum_{i=1}^M\alpha_{t-1}(i)A_{i,k}$

and initialize $\alpha_1(k)=\pi_kB_{0,k}$.

### Predicting future returns

I have also tried letting the model predict a sequence of returns, by using the natural formula

$p_t=\alpha_{t}^T\cdot\mu\in\mathbb{R}^N$ with $N$ the number of assets and $\mu\in\mathbb{R}^{M\times N}$ being the mean returns per regime

but this did not work. The predicted returns are very flat and smooth when compared to the ragged returns coming from the testing data itself. My guess is that the model provides a natural estimation of the mean returns and the regime probability distribution, but that it fails to detect short-term patterns coming from returns themselves. The model is then probably more fit for volatility regime detection.

### Conclusion

This implementation provided a realistic regime transition probability distribution. While clearly not fit for predicting a sequence of future returns, it can definitely provide a layer of more sophisticated risk control on top of some other investment model.