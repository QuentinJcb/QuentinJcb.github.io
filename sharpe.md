<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# What shoudl I expect from a trading strategy based on a weak classifier?

## Context

Let's assume that for each small period of time (it could be 5 minutes, or 1 day), we have access to a prediction given by a weak classifier for the sign of the return of an asset on the next period. Therefore at time $$t$$, we forecast the sign of the return between $$t$$ and $$t + \delta t$$ and invest a value $$V$$. Typically, the accuracy of the prediction would be in the range [0.5, 0.6]. What can we expect in terms of commulative return, volatility and Sharpe ratio ?

## Model
First, we will assume that the price of the asset follows a geometric Brownian motion:
{% raw %} 
$$\frac{\mathrm{d}S_t}{S_t} = \mu \mathrm{d}t + \sigma \mathrm{d}B_t $$ 
{% endraw %}

By integration between $t$ and $t + \delta t$, we can show using Ito's formula the following property:
{% raw %} 
$$\frac{\delta S_t}{S_t} \approx \log(\frac{S_{t + \delta t}}{S_t}) \sim \mathcal{N}(\mu  - \sigma^2/2)\delta t;  \sigma^2 \delta t) $$ 
{% endraw %}

To model the prediction, we assume that at each time step, we predict the correct sign of the return with probability $$p$$. So, with probability $$p$$, the return on our portfolio will be the absolute value of the asset return, and with probability $$1 - p$$, it will be the opposite of the absolute value of the asset return. We can therefore write:
{% raw %} 
$$r_{portfolio} = q_t \mid r_{asset} \mid $$ 
{% endraw %}
where $$q$$ is a random variable taking the value $$+1$$ with probability $$p$$ and $$-1$$ with probability $$1-p$$.

The log-return between $$t=0$$ and $$T$$ will be:
{% raw %} 
$$\log(\frac{S_{T}}{S_0}) = \sum \limits_{t} q_t \mid r_{asset}\ mid $$
{% endraw %}

Finally, we assumet that $$q_t$$ and $$r_t$$ are independent. 

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
Using the property $$\mathrm{Var}(XY) = \mathrm{Var}(X)\mathbb{E}(Y) + \mathrm{Var}(Y)\mathbb{E}(X) + \mathrm{Var}(X)\mathrm{Var}(Y)$$, it follows that:
{% raw %} 
$$\mathrm{Var}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = T\left(4p(1-p)\mathbb{E}(\mid r_{asset}\mid) + (2p-1) \mathrm{Var}(\mid r_{asset}\mid) + 4p(1-p)\mathrm{Var}(\mid r_{asset}\mid)  \right)$$
{% endraw %}

## What is the distribution of $$\mid r_{asset}\mid $$ ?
We have seen before that $$r_t$$ is normally distributed. $$\mid r_{asset}\mid $$ will therefore have a folded normal distribution. The key point is that we can easily compute the mean and variance of this distribution, knowing those of $$r_t$$. The results are the following:
{% raw %} 
$$\mathbb{E}(\mid r_{asset}\mid) = \sigma \sqrt{\delta t} \sqrt{\frac{2}{\pi}}\exp\left(- \frac{\delta t(\mu  - \sigma^2/2)^2}{2\sigma^2 }\right) + (\mu  - \sigma^2/2)\delta t\left(1 - 2\Phi\left(- \sqrt{\delta t}\frac{\mu  - \sigma^2/2}{\sigma}\right)\right)$$
{% endraw %}

The formula giving the variance of $$\mid r_{asset}\mid $$ is much easier:
{% raw %} 
$$\mathrm{Var}(\mid r_{asset}\mid) = \delta t^2(\mu  - \sigma^2/2)^2 + \sigma^2\delta t - \mathbb{E}(\mid r_{asset}\mid )^2$$
{% endraw %}

