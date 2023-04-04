---
title: The REINFORCE Algorithm
date: 2022-04-30 16:30:00 +0800
categories: [Data Science]
tags: [reinforce]
image:
  src: /assets/imgs/reinforce.jpg
  alt: "The REINFORCE Algorithm"
  width: 800
  height: 500
math: true
---

> In this short post, we give an introduction to the REINFORCE algorithm.

The REINFORCE algorithm {% cite williams1992simple %} is a method used to evaluate parameter gradients of an expectation that does not depend on the parameter in a differentiable way. It comes from reinforcement learning. In such a setting, an agent is in an environment represented by a set of states, and each of his action leads to a reward and the next state. His objective is to choose action sequence that maximize the rewards. He has a parameterized policy function that samples action given a state, and our goal is to learn the parameters of the model. The set of actions available may be discrete, so in this setting we need to backpropagate through a discrete sampling process.

In short, it is about moving the gradient operation inside the expectation operation.

Let $$\mathcal{X}=\{0,1\}^d$$ be a discrete space of dimension $d$, which contains some object of interest. Let $f$ be a real valued function defined on $\mathcal{X}$, and let $p_\alpha$ be a discrete probability density on $\mathcal{X}$ with parameter $\alpha$. We would like to compute 

$$
\nabla_\alpha \mathbb{E}_{x\sim p_\alpha}f(x),
$$

the gradient of the expectation of $f(x)$ under density $p_\alpha$ with respect to parameter $\alpha$. The expectation is our objective, and we need the gradient in order to update parameter $\alpha$. The difficulty is that, the sampling process $x\sim p_\alpha$ is discrete, so it is not possible to express the sample $x$ as a differentiable function of the parameter $\alpha$. Such a function is a step function, with zero derivatives almost everywhere and undefined derivatives at the boundaries. Taking gradient with respect to $\alpha$ would not give us much useful information.

Let's see how we can use some trick to solve this. Using the gradient rule for the log function, we can write

<div style="overflow-x:auto;">
$$
\begin{equation}\label{eq:reinforce-intro}
\begin{split}
\nabla_\alpha \mathbb{E}_{x\sim p_\alpha}f(x) &= \nabla_\alpha \int f(x)p_\alpha(x)dx\\
&= \int f(x)\nabla_\alpha p_\alpha(x)dx\\
&= \int f(x)\frac{\nabla_\alpha p_\alpha(x)}{p_\alpha(x)}p_\alpha(x)dx\\
&= \int\left\{f(x)\nabla_\alpha\log p_\alpha(x)\right\}p_\alpha(x)dx\\[0.2cm]
&= \mathbb{E}_{x\sim p_\alpha}[f(x)\nabla_\alpha\log p_\alpha(x)],
\end{split}
\end{equation}
$$
</div>

where the integrals are Lebesgue integrals, with respect to the discrete measure on $\mathcal{X}$. The exchange of gradient and integration operation is justified by [Leibniz integral rule](https://en.wikipedia.org/wiki/Leibniz_integral_rule). The last term in \eqref{eq:reinforce-intro} can be approximated by sample mean, which is an unbiased Monte Carlo estimator of the gradient. We see that we are now able to circumvent the need to differentiate discontinuous functions. Thus, given samples $$\{x^{(i)}\}_{i=1}^N$$, computing $$\nabla_\alpha\mathbb{E}_{x\sim p_\alpha}f(x)$$ means computing


$$
\begin{equation}\label{eq:reinforce-intro-samples}
\frac{1}{N}\sum_{i=1}^Nf\left(x^{(i)}\right)\nabla_\alpha\log p_\alpha\left(x^{(i)}\right).
\end{equation}
$$


We can now sample from the distribution $p_\alpha$, treat the samples as fixed, and then evaluate \eqref{eq:reinforce-intro-samples} on the samples. 

However, being *unbiased* usually means the new formula \eqref{eq:reinforce-intro-samples} can have high *variances*. To reduce variance, in practice one subtracts a *baseline* function $b(\alpha)$ to $f(x)$ that does not depend on each sample. This does not alter the gradient since

<div style="overflow-x:auto;">
$$
\begin{split}
\mathbb{E}_{x\sim p_\alpha}[(f(x)-b(\alpha))\nabla_\alpha\log p_\alpha(x)] &= \mathbb{E}_{x\sim p_\alpha}[f(x)\nabla_\alpha\log p_\alpha(x)] - \mathbb{E}_{x\sim p_\alpha}[b(\alpha)\nabla_\alpha\log p_\alpha(x)]\\[1em]
&= \mathbb{E}_{x\sim p_\alpha}[f(x)\nabla_\alpha\log p_\alpha(x)] - 0\\[1em]
&= \mathbb{E}_{x\sim p_\alpha}[f(x)\nabla_\alpha\log p_\alpha(x)],
\end{split}
$$
</div>

where we used the fact that 

<div style="overflow-x:auto;">
$$
\mathbb{E}_{x\sim p_\alpha}[\nabla_\alpha\log p_\alpha(x)]=0.
$$
</div>

To see this,

<div style="overflow-x:auto;">
$$
\begin{split}
\mathbb{E}_{x\sim p_\alpha}[\nabla_\alpha\log p_\alpha(x)] &= \int\nabla_\alpha\log p_\alpha(x)dx\\
&= \int\frac{\nabla_\alpha p_\alpha(x)}{p_\alpha(x)}p_\alpha(x)dx\\
&= \int\nabla_\alpha p_\alpha(x)dx\\
&= \nabla_\alpha\int p_\alpha(x)dx\\[1em]
&= \nabla_\alpha 1\\[1em]
&= 0.
\end{split}
$$
</div>

<hr>
Cite as:

```bibtex
@article{lifei2022reinforce,
  title   = "{{ page.title }}",
  author  = "Li, Fei",
  journal = "{{ site.url }}",
  year    = "2022",
  url     = "{{ page.url | absolute_url }}"
}
```

## Reference

{% bibliography --cited %}