---
title: Sequence Labeling with HMM and CRF
date: 2022-03-26 13:18:00 +0800
categories: [Data Science]
tags: [nlp, hmm, crf]
image:
  src: /assets/imgs/seq-labeling.png
  alt: "Sequence Labeling with HMM and CRF"
  width: 800
  height: 500
math: true
---

> Sequence labeling, HMM and CRF.

Sequence labeling is the task in which we assign each word $x_i$ in an input word sequence a label $y_i$, so that the output sequence $Y$ has the same length as the input sequence $X$. There are two specific senarios:

- Parts of speech tagging. Parts of speech refer to the grammatical property of a word: noun, verb, pronoun, preposition, adverb, conjunction, participle, and article.
- Named entity recognition (NER). In NER we want to extract structured information from unstructured text, like person name, location, time, price and so on. The NER problem can be converted to a sequence labeling problem, by giving each word a label. Anything outside of interest gets a label like "O".

We introduce two models, the Hiden Markov Model (HMM) and the Conditional Random Field (CRF). Both are probabilistic models that make predictions by selecting tags/labels with largest probabilities. As we can see, these two models are just old wine in new bottles. The HMM corresponds to generative modeling/Baye's rule, and the CRF corresponds to discriminative modeling/logistic regression. The only difference is that, since inputs and outputs come in sequence, rather than treating each input and output in isolation, previous input/output can be taken into account when making predictions about the current output.

## Hidden Markov Model (HMM)
HMM is generative. Given an input sequence $X=x_1\ldots x_n$, the prediction about their labels $Y=y_1\ldots y_n$ is

$$
\begin{split}
Y^* &= \mathop{\mathrm{argmax}}_{Y}\mathbb{P}(Y\mid X)\\[1em]
&= \mathop{\mathrm{argmax}}_{Y}\frac{\mathbb{P}(X\mid Y)\mathbb{P}(Y)}{\mathbb{P}(X)}\\[1em]
&= \mathop{\mathrm{argmax}}_{Y}\mathbb{P}(X\mid Y)\mathbb{P}(Y)\\
&= \mathop{\mathrm{argmax}}_{Y}\prod_{i=1}^n\mathbb{P}(x_i\mid y_i)\mathbb{P}(y_i\mid y_{i-1}).
\end{split}
$$

There are two assumptions:

- Markov assumption: the probability of being in state $y_i$ only depends on the last state: $\mathbb{P}(y_i\mid y_{1},\ldots,y_{i-1})=\mathbb{P}(y_i\mid y_{i-1})$, so that $\mathbb{P}(y_1\ldots y_n) = \prod_{i=1}^n\mathbb{P}(y_i\mid y_{i-1})$

- Output independence: the probability of observing $x_i$ only depends on the hidden state $y_i$, but not on previous words or hidden states.

By the way, the name "Hiden Markov Model" comes from the rhetoric that labels are like a hidden Markov chain with certain states and transition probabilities among them. These states "emit" the word sequence we see.

MLE estimations of the probabilities in the above equation are frequencies in the training corpus:

$$
\mathbb{P}(x_i\mid y_i) = \frac{\#(x_i,y_i)}{\# y_i}.
$$

$$
\mathbb{P}(y_i\mid y_{i-1}) = \frac{\#(y_i, y_{i-1})}{\# y_{i-1}}.
$$

### The Viterbi algorithm

Now, to make inference in HMM, we have to select the best label $$y_s^*$$ among a set of states $$\{y_1,\ldots,y_S\}$$ at each time step $i=1,\ldots,n$. This requires some work. We acheive this, by calculating and saving the maximum value for each state $$y_s$$ in $$\{y_1,\ldots,y_S\}$$ at each step $i$, denoted by $$v(i, y_s)$$, as well as the pointer to the state at step $i-1$ that leads to this maximum value. At the final step, we select the label or state $$y_n^*$$ with maximal value for $$v(n, y_s)$$. Then we follow its pointer to backtrack from this label/state to the previous label/state $$y_{n-1}^*$$, until we reach the beginning, to get the optimal output sequence $$y_1^*,\ldots,y_n^*$$. Yes, here we are using a dynamic programming algorithm to solve a *graph maximization problem*. It is called the *Viterbi algorithm*.

In the course of the algorithm, we populate a table with value $v(i, y_s)$ and a pointer. The Bellman equation is

$$
v(i, y_s) = \max_{y\in\{y_1,\ldots,y_S\}} v(i-1, y)\cdot \mathbb{P}(x_i\mid y_s)\cdot \mathbb{P}(y_s\mid y).
$$

See Figure 1 for an illustration of the algorithm. As it's clear from the description above, its running time is $$\vert S\vert^2\cdot n$$. For a sequence $$Y=y_1\ldots y_n$$ with length $$n$$ where each $$y_i$$ can be any element in $$\{y_1,\ldots,y_S\}$$, there are $$\vert S\vert^n$$ such paths in total. Without the Viterbi algorithm, finding the argmax would require an exponential amount of computation. With savings of intermediate results in memory, we are able to reduce a large amount of computation.

![Hiden Markov Model (HMM) - Illustration of the Viterbi Algorithm](/assets/imgs/viterbi.png){: width=400}
_Figure 1: Illustration of the Viterbi Algorithm for solving Hiden Markov Model (HMM). The model is a generative model. To select the most probable output sequence $Y$, we select $Y$ that makes text sequence $X$ most probable._

<!-- To select the most probable output sequence Y, we select Y that makes text sequence X most probable. -->

## Conditional Random Field (CRF)
CRF is discriminative. It attempts to directly model $\mathbb{P}(Y\mid X)$ with multi-class logistic regression. The only difference is that features take into account not only datapoints $(x_i,y_i)$ in isolation but also $y_{i-1}$, the previous label. Let $\mathcal{Y}$ denote the set of all possible output sequences $Y$ with length $n$, which has an exponential size $|S|^n$. The model is

$$
\mathbb{P}(Y\mid X) = \dfrac{\exp\left(\sum\limits_{k=1}^Kw_kF_k(Y,X)\right)}{ \sum\limits_{Y'\in\mathcal{Y}}\exp\left(\sum\limits_{k=1}^Kw_kF_k(Y',X)\right)}. 
$$

Or, write the denominator in the last equation as $Z(X)$,

$$
\mathbb{P}(Y\mid X) = \frac{1}{Z(X)}\exp\left(\sum_{k=1}^Kw_kF_k(Y,X)\right).
$$

The $K$ features $F_1(Y,X),\ldots,F_K(Y,X)$ are called global features. In linear chain CRF, each global feature is a summation of $n$ local features, where each $f_k(\cdot,\,i), i=1,\ldots,n$ can be an indicator function:

$$
F_k(Y,X) = \sum_{i=1}^nf_k(y_{i-1}, y_{i}, X, i).
$$

Training is done by MLE, thanks to the fact that $n$ is not too large and the normalizing constant $Z(X)$ can be computed in reasonable time. For inference,

$$
\begin{split}
Y^* &= \mathop{\mathrm{argmax}}_{Y}\mathbb{P}(Y\mid X)\\
&= \mathop{\mathrm{argmax}}_{Y}\sum_{k=1}^Kw_kF_k(Y,X)\\
&= \mathop{\mathrm{argmax}}_{Y}\sum_{k=1}^Kw_k\sum_{i=1}^nf_k(y_{i-1}, y_{i}, X, i)\\
&= \mathop{\mathrm{argmax}}_{Y}\sum_{i=1}^n\sum_{k=1}^Kw_kf_k(y_{i-1}, y_{i}, X, i),\\
\end{split}
$$

and again we use the Viterbi algorithm for this sequence maximization problem.