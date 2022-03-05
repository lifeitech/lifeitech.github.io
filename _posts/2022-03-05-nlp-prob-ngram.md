---
title: Natural Language Processing, Probabilities and the n-gram Model
date: 2022-03-05 16:20:00 +0800
categories: [Data Science]
tags: [nlp, n-gram]
image:
  src: /assets/imgs/nlp.jpg
  alt: "Natural Language Processing, Probabilities and the n-gram Model"
  width: 800
  height: 500
math: true
---

> This is the first of a series of posts on Natural Language Processing (NLP). We give an introduction to the subject, mention two model evaluation methods, see how to assign probabilities to words and sentences, and learn the most basic model ------ the n-gram model in NLP.

## Introduction

Natural Language Processing (NLP) concerns the automatic processing of language data. There are two broad classes of tasks: one is *classification*, like spam email filtering, or sentiment classification. One should extract useful features from the data to make the decision. The other kind of task is *generation*. In speech recognition, a model should generate texts from speech inputs; in translation, a model should generate correct translation of a corresponding input, and so on.

Languages are formed from the need to communicate information among creatures, from human minds, from thousands of years of social development and transitions. It is challenging for a computer program to understand and manipulate data that are driven by these forces behind.

We want a model to acquire useful statistics from training data, yet mere statistics is not enough. A core ability of a good language model is the ability to *generalize*, in other words, to have the *intelligence* over the task at hands. 

In probabilistic language modeling, the goal is to

<div style="font-size:20pt;text-align:center;border:3px solid red;margin:5pt;">
assign probabilities to sentences.  
</div>

A related goal is to predict the next word give the current words. The hope is to obtain statistics about a language by training a model using text data. The probabilities that the model outputs should reflect the statistics about the language it learned. After we obtain quantitative assessments about the language, we can use the model to do all sorts of tasks, like

1. Machine translation, e.g. $\mathbb{P}(\textit{high}\text{ winds tonight}) > \mathbb{P}(\textit{large}\text{ winds tonight})$.
2. Spell correction, e.g. $\mathbb{P}(\text{about 15 }\textit{minutes}\text{ from}) > \mathbb{P}(\text{about 15 }\textit{minuets}\text{ from})$.
3. Speech recognition, e.g. $\mathbb{P}(\text{I saw a van}) > \mathbb{P}(\text{eyes awe of an})$.

For evaluating models, the only way to see if a model improves the task at hand is through **extrinsic evaluation**, where you put two models in a real task, like spelling correction or speech recognition, and measure the performance of the two models. But it is time-consuming. Instead, one often uses **intrinsic evaluation**, namely after training two models, calculate probability outputs on some test set, and see which gives higher numbers.

The common metric is the **perplexity**. For a test set $W=w_1\cdots w_N$ (i.e. the whole test set data, including all sentences), it computes

$$
PP(w) = \sqrt[N]{\frac{1}{\mathbb{P}(w_1\cdots w_N)}}.
$$

The lower the perplexity, the better. Minimizing perplexity is the same as maximizing probability.


## Probabilities and the n-gram model

The n-grams model is the simplest model to achieve the goal set at the beginning: **count the number of occurrences of each word (or word sequence)**. A two-word sequence $w_{i-1}w_i$ is called a bigram, like "I love". A three-word sequence is called a trigram, like "I love you".

Specifically, suppose we want to compute $\mathbb{P}(w_{1:n})$, the probability of a word sequence. The chain rule of probability tells us to multiply together the conditional probabilities:

$$
\mathbb{P}(w_{1:n}) = \mathbb{P}(w_1)\mathbb{P}(w_2\mid w_1)\mathbb{P}(w_3\mid w_{1:2})\cdots\mathbb{P}(w_n\mid w_{1:n-1}).
$$

At this point, one makes the Markov assumption that the conditional probability of a word $w$ only depends on the previous 2 or 3 words. In **bigram** model, the probability only depends on the previous one word, i.e. $\mathbb{P}(w_n\mid w_{1:n-1})=\mathbb{P}(w_n\mid w_{n-1})$. **Trigram** model looks two words in the past, and so on. Using bigram model for example, the probability above simplifies to

$$
\mathbb{P}(w_{1:n}) = \prod_{i=1}^n\mathbb{P}(w_i\mid w_{i-1}).
$$

Now, at this point, the obvious way to estimate $\mathbb{P}(w_i\mid w_{i-1})$ is to count $w_{i-1}w_i$ among all bigrams starting with $w_{i-1}$, and calculate the proportion:

$$
\mathbb{P}(w_i\mid w_{i-1}) = \frac{\#w_{i-1}w_i}{\sum_{w}\#w_{i-1}w},
$$

which is also the MLE estimate.

An implementation detail: we shall add `<s>` and `</s>` to the beginning and end of each sentence in the training data, like "`<s>` I like you the way you are `</s>`", to give the bigram context of the first word, and also make the model a true distribution on all sentences.

This is basically the n-gram model. Just count.

### Discussions

n-gram models, while being simple, have obvious drawbacks.

1. Words in a sentence can have *long-distance dependencies*, for example I can insert clauses into a sentence, to separate the subject and the object. Just like natural images are not likely to be color gradients, a paragraph is unlikely to develop in a linear, continuous and predictable way. In other words, languages, like many other high dimensional data, are highly *nonlinear*. The Markov assumption is the very drawback to this. 

2. Since the model is a primitive statistics of the training data, it falls short of predicting anything that are not in the training data. Training data can only be sparse in data space, but any good AI model need the ability to generalize. One common remedy to this is to do smoothing to the probabilities. 

    (a) Laplace (or add-one) smoothing -- add one to each count:

    $$
    \mathbb{P}^*(w_n\mid w_{n-1}) = \frac{\#w_{i-1}w_i+1}{\sum_{w}(\#w_{i-1}w+1)} = \frac{\#w_{i-1}w_i+1}{\sum_{w}\#w_{i-1}w + V}.
    $$

    Thus, if the $w_{i-1}$ never appears before, then we give any bigram $w_{i-1}w$ a probability of $1/V$.


    (b) Add-$k$ smoothing, where $k\in[0,1]$:

    $$
    \mathbb{P}^*(w_n\mid w_{n-1}) = \frac{\#w_{i-1}w_i+k}{\sum_{w}\#w_{i-1}w + kV}.
    $$

    (c) Backoff: use trigram if the evidence is sufficient, other wise use bigram, otherwise use unigram. In other words, we "back off" if we have zero evidence for a higher-order n-gram.

    (d) Interpolation: use weighted average of trigram, bigram and unigram estimates, for example

    $$
    \mathbb{P}^*(w_n\mid w_{n-2}w_{n-1}) = \lambda_1\mathbb{P}(w_n\mid w_{n-2}w_{n-1}) + \lambda_2\mathbb{P}(w_n\mid w_{n-1}) + \lambda_3\mathbb{P}(w_n)
    $$

    where $\lambda_1+\lambda_2+\lambda_3=1$.

