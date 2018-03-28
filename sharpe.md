<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# What shoudl I expect from a trading strategy based on a weak classifier?

## Context

Let's assume that for each small period of time (it could be 5 minutes, or 1 day), we have access to a prediction given by a weak classifier for the sign of the return of an asset on the next period. Therefore at time $t$, we forecast the sign of the return between $t$ and $t + \delta t$ and invest a value $V$. Typically, the accuracy of the prediction would be in the range [0.5, 0.6]. What can we expect in terms of commulative return, volatility and Sharpe ratio ?

## Model
First, we will assume that the price of the asset follows a geometric Brownian motion:
{% raw %} 
$$\frac{\mathrm{d}S_t}{S_t} = \mu \mathrm{d}t + \sigma \mathrm{d}B_t $$ 
{% endraw %}

By integratio between $t$ and $t + \delta t$, we can show using Ito's formula the following property:
{% raw %} 
$$\frac{\delta S_t}{S_t} \sim \mathcal{N}(\mu  - \sigma^2/2)\deltat; + \sigma^2 \delta t) $$ 
{% endraw %}
