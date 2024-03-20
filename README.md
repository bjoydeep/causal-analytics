# causal-analytics

This repository is inspired by thoughts in here:

1. [Causal inference in statistics: An overview](https://ftp.cs.ucla.edu/pub/stat_ser/r350.pdf)
1. [An Introduction to Causal Inference - PMC](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2836213/)
1. [The Book of Why: The New Science of Cause and Effect – Pearl and Mackenzie](http://bayes.cs.ucla.edu/WHY/why-ch1.pdf)

And concrete implementation of [doWHy](https://github.com/py-why/dowhy)

The goal is to make problem debugging in any domain simpler than what it is today.

## Introduction

When we explain a complex concept in white board, we draw some circles and arrows joining them. That in effect is a DAG (direct acyclic graph) - assuming there are no closed cycles. That is quintessence way to express domain knowledge. We can take a DAG and:

1. store it as a __Knowledge Graph__ in a Graph DB with some kind of data
1. convert it to a __Bayesian Network__ by adding the Conditional Probability Distribution (CPD) to it
1. convert it to a __Causal model__ (Bayesian Network or a DAG with a little more rigor if needed) with some structural equation

Each of the above options take domain knowledge and helps answer some questions about it.

The [introductory notebook](introduction.ipynb) ties these together. 

### Naming Convention followed in Causal literature

- v variables - are the treatments. They can be binary or continuous. In the case of continuous abs(beta) defines thier magnitude;

- y - is the outcome variable. The generating equation is, y = normal(0, stddev_outcome_noise) + t @ beta [where @ is a numpy matrix multiplication allowing for beta be a vector]

- W variables - Common Causes. Commonly cause both the treatment and the outcome and are iid. if continuous, they are Norm(mu = Unif(-1,1), sigma = 1)

- Z variables - Instrument variables. Each one affects all treatments. i.e. if there is one instrument and two treatments then z0->v0, z0->v1

- X variables - effect modifiers. If continuous, they are Norm(mu = Unif(-1,1), sigma = 1) [x -> y]

- FD variables - Front door variables, v0->FD0->y

### Causal Nomenclature describing effects of variables

These names are understood:
- common causes
- instrument variables
- effect modifier
- front door variables
- treatment
- outcome

But, we need to understand a little more how some of them affect the causal pathway. To desribe that, causal community groups them by :
- Confounders
- Back door
- Modifier
- Colliders 



