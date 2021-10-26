---
layout: post
title:  "Forecasting volatility"
subtitle: "Elementary statistics meets Neural Networks: inferring a parametric probability distribution."
date: 2021-10-23
categories: machine learning
---

# Introduction

A few months ago, Optiver launched a nice Kaggle [competition](https://www.kaggle.com/c/optiver-realized-volatility-prediction). Quite briefly, the goal is to predict the realized volatility during the next ten minutes for several stocks. To this end, you are given certain information about the order and trade book of each stock for the ten minutes window right before the target period. In this post, I will describe an approach that is different from the ones addressed in the [discussion section](https://www.kaggle.com/c/optiver-realized-volatility-prediction/discussion) (which by the way, warmly recommend to check). Although I wasn't able to obtain a great score at local cross validation, I think it had potential.

# A minimal background

Let's say we observe the price $$S_t$$ of an action for times $$t = 0, 1, \dots, n$$. The realized volatility $$RV$$ during this period is defined as the square root of the realized variance of the logarithmic returns, that is  

$$
	RV = \sqrt{\sum_{t=1}^{n} r_t^{2} },
$$

where $$r_t = \log\left( \frac{S_t}{S_{t-1}} \right) = \log(S_t) - \log(S_{t-1})$$. Usually the realized volatility is normalized by the length of the considered period (e.g., divided by $$\sqrt{n}$$), but let's adopt the above definition for consistency with the challenge. The use of logarithmic returns instead of the stock price or the *raw* returns $$\frac{S_t}{S_{t-1}}$$ offers several algebraic/technical advantages, but I would like to stress only the following one: if we are to believe in the [Black-Scholes model](https://en.wikipedia.org/wiki/Black-Scholes_model) the prices are lognormally distributed and hence, the logarithmic returns follow a normal distribution. Thus, under this model the realized volatility admits a straightforward interpretation as a natural estimator of the standard deviation for the underlying normal distribution (classically denoted with $$\sigma$$).

As an immediate consequence, forecasting the realized volatility reduces to estimating a parameter (the variance) of a normal distribution from a number of samples, which is a classical problem in statistical inference. One of the most intuitive and simplest methods for achieving this is the maximum likelihood estimation method.

# Maximum likelihood estimation

Imagine you tossed a coin $$n$$ times and recorded the outcomes $$x_1, \dots, x_n$$ where $$x_i = 1$$ if the $$i$$-th side was heads and $$0$$ otherwise. You suspect that the coin is unfair (that is, heads comes out with a certain probability $$p \neq \frac{1}{2}$$) and want to infer $$p$$. In such a situation, where you have samples of a parametric probability distribution (in this case, $${\mathcal B}(p)$$), the maximum likelihood estimation dictates that you should choose the parameter that maximizes the probability of the outcomes. Intuitive, isn't it?

To formalize this idea, let's define the likelihood function $$\mathcal{L}$$ at a parameter $$p$$ as the probability of having seen the events $$x_1, \cdots, x_n$$. In our example, it amounts to  

$$
	\mathcal{L}(p) = P(x_1, \cdots, x_n | p) = \prod_{i=1}^{n} p^{x_i}(1-p)^{x_i}.
$$

To simplify the computations, a standard trick is finding the maxima of the logarithm of the likelihood, which doesn't change the result since $$\log$$ is an increasing map but transforms the product into a sum. Deriving with respect to $$p$$,  

$$
	\frac{\partial \log(\mathcal{L}(p))}{\partial p} = \frac{\partial}{\partial p} \sum_{i=1}^{n} x_i \log(p) + (1-x_i)\log(1-p) = \sum_{i=1}^{n} \frac{x_i}{p} - \frac{1-x_i}{1-p}.
$$

From there, it is not difficult to check that the likelihood attains a maximum at $$p = \frac{1}{n} \sum_{i=1}^{n} x_i$$. The average frequency of heads was probably your first hunch, but hey, here are some pretty solid maths backing it up!

Returning to the volatility forecast, by accepting the Black-Scholes model we would be in a similar situation. For each stock, we have several realizations of its volatility in 10 minutes periods and the distribution of its returns is normal. This time, it doesn't make sense to consider probabilities at a single point, so the likelihood function is modified to use the conditional *density* instead. However, while the results aren't terrible, the accuracy we get through this technique is far from being satisfactory. How come? A quick glance at the data reveals the reason: the distribution of the stocks' realized volatility looks nowhere similar to a Dirac delta, as we would expect under the Black-Scholes model (since the variance of the returns should be essentially constant).

[comment]: # ![Realized volatility distribution](/img/realized_volatility_example.png){:height="286px" width="730px"}

<img class="img-fluid" src="/img/realized_volatility_example.png" alt="Realized volatility distribution">

<span class="caption">Empirical distribution of the realized volatility for a typical stock (the plot was generated using searborn's utility `kdeplot`.</span>

## Digression: benefits of fitting a probability distribution

It is probably a good moment to address the question as to why we'd be interested in learning a probability distribution from the data instead of treating the realized volatility forecast as a regression problem (i.e. just trying to predict a number). In my opinion, the main motivations in this case are noise reduction and hedging against uncertainty.

As for the first one, it is well known that financial signals tend to be noisy. Although there are many different techniques to deal with this difficulty, I like the probability model approach because it attempts to handle the noise *by design*. Typically, the available data will be more than enough to robustly fit the few parameters of an underlying probabilistic model (and by this I mean a parametric probability distribution which the data at least approximately follow) even under the presence of noise. Moreover, you will automatically have at your disposal not just a number but a complete scenario with the probability of each event to help making optimal decisions. If I had to invest real money based on predictions, I would feel much more comfortable using a reasonably accurate probabilistic model that allows to quantify the risk than a black-box model that just outputs the next return, even at the cost of some precision loss.

However, all this has the rather heavy assumption that the data under analysis follows a parametric probability distribution. Is it the case in our problem? Well, I just started checking all known continuous probability distributions until I stumbled upon the Inverse Gamma, which looked promising. I gave it a shot using the `scipy.stats` library and...

<img class="img-fluid" src="/img/inv_gamma_fit.png" alt="Fitting the realized volatility with an Inverse Gamma distribution">

<span class="caption">Fitting of the realized volatility with an Inverse Gamma (the actual empirical distribution is in blue, the fit is in orange).</span>

# Maximum likelihood estimation revisited

