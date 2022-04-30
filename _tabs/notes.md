---
layout: page
title: Notes
icon: 📕
order: 1
---

I'm glad to share some of my course notes. Subjects include machine learning, algorithms, probability, statistics, stochastic process, optimal control, general topology and more.

* TOC
{:toc}

## Notes on Machine Learning

Common models in machine learning are discussed, including linear regression, logistic regression, Gaussian discriminant analysis, naive Bayes, splines, decision trees, gradient boosting and support vector machines. This notes focus on the mathematical derivation of those models. Highlights:
1. a derivation of online linear regression formula;
2. logistic regression parameter estimation;
3. derivation of the AdaBoost as a gradient boosting algorithm with exponential loss function;
4. a proof that AUC is the probability that a classifier ranks a randomly chosen
positive sample higher than a randomly chosen negative sample.

{% include pdf-block.html abbr="ml"
                          filename="machine-learning.pdf" 
                          title="Notes on Machine Learning"
                          thumbnails = "6, 8, 29"
                          bottom=false
                          %}

## Course Notes for Statistics and Probability

First there is a quick rundown of the basics of probability theory, then a significant portion is devoted to parametric inference. Topics include sufficient statistics, Fisher information, ancillarity and completeness, maximum likelihood estimation, Cramér-Rao bound, hypothesis testing. In addition, bootstrap, the EM algorithm, simulations and principal component analysis (PCA) are also explained in detail.

{% include pdf-block.html abbr="stats"
                          filename="statistics-and-probability.pdf" 
                          title="Course Notes for Statistics and Probability"
                          thumbnails = "16, 29, 52"
                          bottom=false
                          %}

## Course Notes for Algorithms

Course notes for algorithms course. Part of the course followed [Cormen's Introduction to Algorithms ](https://mitpress.mit.edu/books/introduction-algorithms-third-edition). Besides data structures (stacks, linked lists, BSTs, hash tables etc.) and sorting algorithms, the notes contain a detailed documentation for graph algorithms, including the Prim's algorithm, the Bellman-Ford Algorithm, Dijkstra's algorithm, the Floyd-Warshall algorithm and so on, as well as several dynamic programming problems. Randomized algorithms are also discussed, including the Karger's min-cut algorithm.

{% include pdf-block.html abbr="algo"
                          filename="optimization.pdf" 
                          title="Course Notes for Algorithms"
                          thumbnails = "16, 27, 46"
                          bottom=false
                          %}

## Course Notes for Optimization

This is my course notes on optimal control theory. First we discuss Banach spaces and Hilbert spaces. Full proofs are given to The [Banach fixed point theorem](https://en.wikipedia.org/wiki/Banach_fixed-point_theorem), the [Schauder's fixed point theorem](https://en.wikipedia.org/wiki/Schauder_fixed-point_theorem) and the [Riesz representation theorem](https://en.wikipedia.org/wiki/Riesz_representation_theorem). Next, in section 3, we discuss calculus of variations, including the famous Euler-Lagrange equation. Section 4 includes an introduction to optimal control problems and the Bang-bang principle. Finally the Pontraygin maximum principle and dynamic programming are discussed. Many examples are present in the notes.

{% include pdf-block.html abbr="opt"
                          filename="optimization.pdf" 
                          title="Course Notes for Optimization"
                          thumbnails = "11, 36, 39"
                          bottom=false
                          %}


## Course Notes for Stochastic Process

The course followed Erhan Çinlar's book [Probability and Stochastics](https://link.springer.com/book/10.1007/978-0-387-87859-1). Topics include measure and integration, probability theory, martingales, Poisson random measures, Lévy processes and Brownian motion.

{% include pdf-block.html abbr="stocha"
                          filename="stochastic-process.pdf" 
                          title="Course Notes for Stochastic Process"
                          thumbnails = "2, 8, 37"
                          bottom=false
                          %}

## Course Notes for Microeconometrics

Topics include causality, linear regression, t test and F test, difference in difference (DID), instrumental variables (IV) and regression discontinuity design (RDD).

{% include pdf-block.html abbr="metrics"
                          filename="microeconometrics.pdf" 
                          title="Course Notes for Microeconometrics"
                          thumbnails = "2, 16, 21"
                          bottom=false
                          %}

## Course Notes for Time Series

The first part of the note is univariate time series analysis. Topics include stationary process, autocovariance function, ARMA models, invertibility, model estimation and forecasting. The second part is multivariate time series analysis. Topics include estimation, forecasting and interpretation of VARMA models.

{% include pdf-block.html abbr="series"
                          filename="time-series.pdf" 
                          title="Course Notes for Time Series"
                          thumbnails = "9, 17, 23"
                          bottom=false
                          %}

## Notes for General Topology

This is the course note that I gave to students when I was a teaching assistant for the Mathematical Economics course at Shandong University, where part of the course covered general topology. We start from metric spaces, and then cover all essential concepts in general topology, including basis, convergence, continuity, connectedness, and compactness. We also discuss some advanced topics such as countability, separation properties and Urysohn lemma.

{% include pdf-block.html abbr="topology"
                          filename="topology.pdf" 
                          title="Notes for General Topology"
                          thumbnails = "31, 33, 43"
                          bottom=false
                          %}


## Bilinear Forms and Sesquilinear Forms

In this article, we give a detailed look at bilinear and sesquilinear forms in linear algebra. We’re interested in conditions under which a form possesses an orthonormal basis. It turns out that this is true if and only if it is (Hermitian) symmetric and positive definite. We show that the symmetry condition is due to symmetry of orthogonality. We then discuss operators and quadratic forms using our theory of bilinear forms.

{% include pdf-block.html abbr="bilinear"
                          filename="bilinear-form.pdf" 
                          title="Bilinear Forms and Sesquilinear Forms"
                          thumbnails = "8, 14, 18"
                          bottom=false
                          %}

## An Introduction to Set Theory

In this article,  we give a quick introduction to set theory. Section 1 contains a review of algebra of sets, functions, and power sets. Section 2 discusses Russell’s paradox, following which we mention the axiom of specification in ZFC set theory. Sections 3 is on countability and uncountability of sets. We prove basic properties of countable sets, and then give a discussion of the Continuum Hypothesis.

{% include pdf-block.html abbr="set"
                          filename="set-theory.pdf" 
                          title="An Introduction to Set Theory"
                          thumbnails = "2, 5, 10"
                          bottom=true
                          %}