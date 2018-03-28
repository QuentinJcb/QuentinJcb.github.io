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
$$\mathbb{E}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = \sum \limits_{t} \mathbb{E}(q_t) \mathbb{E}(\mid r_{asset}\ mid)$$
{% endraw %}
thus 
{% raw %} 
$$\mathbb{E}\left(\sum \limits_{t} q_t \mid r_{asset} \mid \right) = T \mathbb{E}(q_t) \mathbb{E}(\mid r_{asset}\ mid)$$
{% endraw %}




