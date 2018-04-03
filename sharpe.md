<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Return sign predictions slightly better than coin-flipping: what Sharpe ratio can I expect?

## Context

Let's assume that for each small period of time (it could be 5 minutes, or 1 day), we have access to a prediction given by a weak classifier for the sign of the return of an asset on the next period. Therefore at time $$t$$, we forecast the sign of the return between $$t$$ and $$t + \delta t$$ and invest a value $$V$$. Typically, the accuracy of the prediction, that is the percentage of winning trades, would be in the range [0.5, 0.6]. What can we expect in terms of commulative return, volatility and Sharpe ratio for this strategy?

We assume a risk-free rate of zero.

## Model
First, we will assume that the price of the asset follows a geometric Brownian motion:
{% raw %} 
$$\frac{\mathrm{d}S_t}{S_t} = \mu \mathrm{d}t + \sigma \mathrm{d}B_t $$ 
{% endraw %}

By integration between $$t$$ and $$t + \delta t$$, we can show using Ito's formula the following property:
{% raw %} 
$$\frac{\delta S_t}{S_t} \approx \log(\frac{S_{t + \delta t}}{S_t}) \sim \mathcal{N}((\mu  - \sigma^2/2)\delta t;  \sigma^2 \delta t) $$ 
{% endraw %}

To model the prediction, we assume that at each time step, we predict the correct sign of the return with probability $$p$$. So, with probability $$p$$, the return on our portfolio will be the absolute value of the asset return, and with probability $$1 - p$$, it will be the opposite of the absolute value of the asset return. We can therefore write:
{% raw %} 
$$r_{portfolio} = q_t \mid r_{asset} \mid $$ 
{% endraw %}
where $$q$$ is a random variable taking the value $$+1$$ with probability $$p$$ and $$-1$$ with probability $$1-p$$.

The log-return between $$t=0$$ and $$T$$ will be:
{% raw %} 
$$\log\left(\frac{S_{T}}{S_0}\right) = \sum \limits_{t} q_t \mid r_{asset}\mid $$
{% endraw %}

Finally, we assume that $$q_t$$ and $$r_t$$ are independent. This is a strong assumption: some strategies are known to be skewed, meaning that small trading losses are common, but occasional big gains happen. In particular, see Jean-Philippe Bouchaud, Marc Potters. Trend followers lose more often than they gain. 2005[^fn1]

## Expected value and variance
The expression $$\log(\frac{S_{T}}{S_0})$$ has the nice property of being expressed as a sum of iid random variables. It will make the calculation of its expected value and variance easier.
{% raw %} 
$$\mathbb{E}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = \sum \limits_{t} \mathbb{E}(q_t) \mathbb{E}(\mid r_{asset}\mid)$$
{% endraw %}
thus 
{% raw %} 
$$\mathbb{E}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = T (2p-1) \mathbb{E}(\mid r_{asset}\mid)$$
{% endraw %}
For the variance, we have:
{% raw %} 
$$\mathrm{Var}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = \sum \limits_{t} \mathrm{Var}\left(q_t \mid r_{asset}\mid \right)$$
{% endraw %}
Using the property $$\mathrm{Var}(XY) = \mathrm{Var}(X)\mathbb{E}(Y)^2 + \mathrm{Var}(Y)\mathbb{E}(X)^2 + \mathrm{Var}(X)\mathrm{Var}(Y)$$, it follows that:
{% raw %} 
$$\mathrm{Var}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = T\left(4p(1-p)\mathbb{E}(\mid r_{asset}\mid)^2 + (2p-1)^2 \mathrm{Var}(\mid r_{asset}\mid) + 4p(1-p)\mathrm{Var}(\mid r_{asset}\mid)  \right)$$
{% endraw %}

## What is the distribution of $$\mid r_{asset}\mid $$ ?
We have seen before that $$r_t$$ is normally distributed. $$\mid r_{asset}\mid $$ will therefore have a folded normal distribution. The key point is that we can easily compute the mean and variance of this distribution, knowing those of $$r_t$$. The results are the following:
{% raw %} 
$$\mathbb{E}(\mid r_{asset}\mid) = \sigma \sqrt{\delta t} \sqrt{\frac{2}{\pi}}\exp\left(- \frac{\delta t(\mu  - \sigma^2/2)^2}{2\sigma^2 }\right) + (\mu  - \sigma^2/2)\delta t\left(1 - 2\Phi\left(- \sqrt{\delta t}\frac{\mu  - \sigma^2/2}{\sigma}\right)\right)$$
{% endraw %}

The variance of $$\mid r_{asset}\mid $$ is given by:
{% raw %} 
$$\mathrm{Var}(\mid r_{asset}\mid) = \delta t^2(\mu  - \sigma^2/2)^2 + \sigma^2\delta t - \mathbb{E}(\mid r_{asset}\mid )^2$$
{% endraw %}

The next step is to assess the sensitivity of the mean and the variance of the portfolio with respect to $$p$$. 

## Some orders of magnitude
Let's introduce the variable $$\epsilon$$ defined as $$p = 1/2 + \epsilon$$. $$\epsilon$$ quantifies the excess of accuracy from a coin-flipping strategy. We then have:
{% raw %} 
$$\mathbb{E}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) =2T\epsilon\mathbb{E}(\mid r_{asset}\mid)$$
{% endraw %}
and 
{% raw %} 
$$\mathrm{Var}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = T\left((1 - 4\epsilon^2)\mathbb{E}(\mid r_{asset}\mid)^2 + 4\epsilon^2 \mathrm{Var}(\mid r_{asset}\mid) + (1-4\epsilon^2)\mathrm{Var}(\mid r_{asset}\mid) \right)$$
{% endraw %}
so that 
{% raw %} 
$$\mathrm{Var}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = T\left( \delta t^2(\mu - \sigma^2/2)^2 + \sigma^2\delta t- 4\epsilon^2\mathbb{E}(\mid r_{asset}\mid)^2 \right)$$
{% endraw %}
We can therefore approximate the standard deviation of the portfolio returns by this expression:
{% raw %} 
$$\sigma_{portfolio} \approx T^{1/2}\sqrt{\delta t^2(\mu - \sigma^2/2)^2 + \sigma^2\delta t} \approx T^{1/2}\sigma\sqrt{\delta t}$$
{% endraw %}

$$\mathbb{E}(\mid r_{asset}\mid)$$ can be approximated by its first term:
{% raw %} 
$$\mathbb{E}(\mid r_{asset}\mid) \approx \sigma\sqrt{\delta t}\sqrt{\frac{2}{\pi}} $$
{% endraw %}

Finally, the Sharpe ratio of this strategy is 
{% raw %} 
$$ SR_{portfolio} \approx 1.6\epsilon T^{1/2}$$
{% endraw %}

For daily predictions, over one year, we find $$SR_{portfolio} \approx 25\epsilon $$: an increase of 1% in our accuracy will lead to a SR increase of 25 pp! A percentage of winning trades of 54% will lead to a Sharpe ratio of 1.

## References
[^fn1]: [Jean-Philippe Bouchaud, Marc Potters. Trend followers lose more often than they gain. 2005.](https://arxiv.org/pdf/physics/0508104.pdf)

