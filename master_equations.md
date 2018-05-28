<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Master equations: from physics to finance

In physics, master equations describe the temporal evolution of a system by specifying some relations between the probability of the system being in a specific state and its derivatives. These equations can be found almost everywhere, from chemistry to quantum mechanics. More suprisingly, some strong analogies exist between physical and financial systems, the core of the intersection being probably the Brownian motion. In this context, master equations emerge and can be of great help when modeling financial mechanisms. The purpose of this article is to show how very basic financial models inspired from physics can exhibit non-trivial properties.

## The random walk

Let us assume that the price of a security can only take discrete values evenly spaced on a grid (such an assumption is not very restrictive as quoted prices can indeed only take specific values which are multiples of the tick size). Let us also assume that prices go up and down with equal probabilities. Under these assumptions, if we observe a price $$x$$ at time $$t + \Delta t$$, two cases must be considered: either at time $$t$$ the price was $$x - \Delta x$$, either it was $$x + \Delta x$$. We can therefore write the following master equation describing the probability of observing a price $$x$$ at time $$t + \Delta t$$:


{% raw %} $$P(x, t + \Delta t) = \frac{P(x + \Delta x, t) + P(x - \Delta x, t)}{2} $$ {% endraw %}

By substracting $$P(x, t)$$, we have


{% raw %} $$P(x, t + \Delta t) - P(x, t) = \frac{P(x + \Delta x, t) -2P(x, t) + P(x - \Delta x, t)}{2} $$ {% endraw %}

For small $$\Delta x$$ and $$\Delta t$$, the first term can be approximated by $$\frac{\partial P(x, t)}{\partial t} \Delta t $$ and the second one by $$\frac{\partial^2P(x, t)}{\partial x^2}\Delta x^2 $$ leading to the **following Heat equation**:


{% raw %} $$\frac{\partial P(x, t)}{\partial t}= \frac{\Delta x^2}{2\Delta t} \frac{\partial^2P(x, t)}{\partial x^2}$$ {% endraw %}


