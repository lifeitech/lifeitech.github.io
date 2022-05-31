---
title: Natural Language Processing, Probabilities and the n-gram Model
date: 2022-03-05 16:20:00 +0800
categories: [Data Science]
tags: [nlp, n-gram]
image:
  src: /assets/imgs/nlp-head.jpg
  alt: "Natural Language Processing, Probabilities and the n-gram Model"
  width: 800
  height: 500
math: true
---

> This is the first one of a series of posts on Natural Language Processing (NLP). We give an introduction to the subject, mention two model evaluation methods, see how to assign probabilities to words and sentences, and learn the most basic model ------ the n-gram model in NLP.

## Introduction

The field of Natural Language Processing (NLP) is concerned with automatic processing of language data. It arose with the widespread usage of the Internet. There are many scenarios for which NLP techniques can be useful. An email service provider may wish to  have a program to filter spam emails. Some data companies regularly scrape the web to extract information from millions of text entries. Marketing companies extract sentiments from online comments and reviews. Softwares can be developed to transcribe speech into texts, and translate one language to another. We can even ask machines to generate articles and reports for us.

If you think about it, it is very hard to transfer our knowledge about human languages to a computer program.

Languages are formed from the incentive to communicate information among creatures. They evolve from thousands of years of social development and transitions. A piece of text is not a mere permutation of words. Rather, it is a reflection of the 3D world, a condensed representation of much higher dimensional complexities. How could a model really understand the word "sea" when it never have the chance to directly see the ocean like humans do? Much more complicated concepts like "think", "abstract", "economy" would be more challenging to learn.  It is thus very difficult to train a computer program for it to understand and then manipulate texts that depend on external matters that are beyond language itself.

Just imagine how you would learn a foreign language solely based on a large amount of texts in that language. You are not given any vocabulary table, but you must learn the meaning of every word, the grammar, and every expression only based on statistics of texts. This sounds intimidating right? Now imagine doing the same task, this time being an infant and not understanding much of the world. You have to learn every concept like "politics" and "tyranny" on your own......I'm not sure if this is feasible at all. This thought experiment shows that, training a model to "understand" human languages is never an obvious and easy task.

But to solve the problem, we don't have to try hard to jump to the ultimate and perfect solution at one leap, if there is one. We can always start from the easiest approach, and think about how we can use a better way to improve over it.

## Language models

A language model is a model that **assigns probabilities to words and sentences**.

Models are often trained to predict the next word give current contexts, i.e. output a probability distribution over vocabularies conditioned on previous texts. The output probabilities should reflect word statistics in the training data. Probabilities can be very useful for many applications. For example, for grammar correction, $\mathbb{P}(\text{This }\textit{means}\text{ in particular}) > \mathbb{P}(\text{This }\textit{mean}\text{  in particular})$.

For evaluating models, the only way to see if a model improves the task at hand is through **extrinsic evaluation**, where you put two models in a real task, like spelling correction or speech recognition, and measure the performance of the two models. But it is time-consuming. Instead, one often uses **intrinsic evaluation**, namely after training two models, calculate probability outputs on some test set, and see which one gives higher numbers.

The common metric is the **perplexity**. For a test set $W=w_1\cdots w_N$ (i.e. the whole test set data, including all sentences), it computes

$$
PP(w) = \sqrt[N]{\frac{1}{\mathbb{P}(w_1\cdots w_N)}}.
$$

The lower the perplexity, the better. Minimizing perplexity is the same as maximizing probability.


## The n-gram model

The n-grams model is the simplest model to achieve the goal of assigning probabilities to words: **count the number of occurrences of each word (or word sequence)**. A two-word sequence $w_{i-1}w_i$ is called a bigram, like "I love". A three-word sequence is called a trigram, like "I love you".

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

This is basically the n-gram model. Just counting.

### Drawbacks and remedies

n-gram models, while being simple, have obvious drawbacks.

1. Words in a sentence can have *long-distance dependencies*, for example I can insert clauses into a sentence, to separate the subject and the object. Just like natural images are not likely to be color gradients, a paragraph is unlikely to develop in a linear, continuous and predictable way. In other words, languages, like many other high dimensional data, are highly *nonlinear*. The Markov assumption is the very drawback to this. 

2. Since the model is a primitive statistics of the training data, it falls short of predicting anything that are not in the training data. Training data can only be sparse in data space, but any good AI model should have the ability to generalize. One common remedy to this is to do smoothing to the probabilities. 

    (a) Laplace (or add-one) smoothing -- add one to each count:

    $$
    \mathbb{P}^*(w_n\mid w_{n-1}) = \frac{\#w_{i-1}w_i+1}{\sum_{w}(\#w_{i-1}w+1)} = \frac{\#w_{i-1}w_i+1}{\sum_{w}\#w_{i-1}w + V}.
    $$

    Thus, if the $w_{i-1}$ never appears before, then we give any bigram $w_{i-1}w$ a probability of $1/V$.


    (b) Add-$k$ smoothing, where $k\in[0,1]$:

    $$
    \mathbb{P}^*(w_n\mid w_{n-1}) = \frac{\#w_{i-1}w_i+k}{\sum_{w}\#w_{i-1}w + kV}.
    $$

    (c) Backoff: use trigram if the evidence is sufficient, otherwise use bigram, otherwise use unigram. In other words, we "back off" if we have zero evidence for a higher-order n-gram.

    (d) Interpolation: use weighted average of trigram, bigram and unigram estimates, for example

    $$
    \mathbb{P}^*(w_n\mid w_{n-2}w_{n-1}) = \lambda_1\mathbb{P}(w_n\mid w_{n-2}w_{n-1}) + \lambda_2\mathbb{P}(w_n\mid w_{n-1}) + \lambda_3\mathbb{P}(w_n)
    $$

    where $\lambda_1+\lambda_2+\lambda_3=1$.


## Summary

NLP is about teaching machines to extract information from texts, to classify texts, and to generate texts. The core ability that we want a model to acquire is the ability to generalize. We want a model to have enough intelligence and flexibility to handle all kinds of variations in downstream tasks that may not be present in the training data. Language models output probabilities over words. n-gram models are the simplest kind of language models, which give a primitive statistical summary of the training data. Although insufficient, they serve as a benchmark for more advanced models.

<hr>
Cite as:

```bibtex
@article{lifei2022ngram,
  title   = "{{ page.title }}",
  author  = "Li, Fei",
  journal = "www.lifei.tech",
  year    = "2022",
  url     = "{{ page.url | absolute_url }}"
}
```