# causal-analytics

This repository is inspired by thoughts in here:

1. [Causal inference in statistics: An overview](https://ftp.cs.ucla.edu/pub/stat_ser/r350.pdf)
1. [An Introduction to Causal Inference - PMC](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2836213/)
1. [The Book of Why: The New Science of Cause and Effect – Pearl and Mackenzie](http://bayes.cs.ucla.edu/WHY/why-ch1.pdf)

And concrete implementation of [doWHy](https://github.com/py-why/dowhy)

The goal is to make problem debugging in any domain simpler than what it is today.

## Introduction

When we explain a complex concept in white board, we draw some circles and arrows joining them. That in effect is a DAG (direct acyclic graph) - assuming there are no closed cycles. That is the quintessential way to express domain knowledge. We can take a DAG and:

1. store it as a __Knowledge Graph__ in a Graph DB with some kind of data
1. convert it to a __Bayesian Network__ by adding the Conditional Probability Distribution (CPD) to it
1. convert it to a __Causal model__ (Bayesian Network or a DAG with a little more rigor if needed) with some structural equation

Each of the above options take domain knowledge and helps answer some questions about it.

The [introductory notebook](introduction.ipynb) ties these together. 

### Naming Convention followed in Causal literature

- v variables - are the `treatments`. They can be binary or continuous. In the case of continuous abs(beta) defines thier magnitude;

- y - is the `outcome` variable. The generating equation is, y = normal(0, stddev_outcome_noise) + t @ beta [where @ is a numpy matrix multiplication allowing for beta be a vector]

- W variables - Common Causes or `Confounders`. Commonly cause both the treatment and the outcome and are iid. if continuous, they are Norm(mu = Unif(-1,1), sigma = 1)

- Z variables - Instrument variables. Each one affects all treatments. i.e. if there is one instrument and two treatments then z0->v0, z0->v1

- X variables - effect modifiers or `Covariates`. If continuous, they are Norm(mu = Unif(-1,1), sigma = 1) [x -> y]

- FD variables - Front door variables, v0->FD0->y. These are called `Mediators`

### Causal Nomenclature describing effects of variables

These names are understood:
- common causes
- instrument variables
- effect modifier
- front door variables
- treatment
- outcome

## Backdoor
### Backdoor Criterion
A a set of variables Z satisfies the backdoor criterion relative to (X, Y) if:

1. no node in Z is a descendant of X,
1. Z blocks every path between X and Y that contains `an arrow into X`

It is implied that:
- X -> Y is the causal path.
- Therefore all backdoor paths go through Z.
- And our overall goal is to make sure that we get the `correct` impact (causal) of X on Y. So we must ensure nothing effects this. In a RCT, we can control for these. Since we are working off Observed data, we must do the due mathematical diligence and identify those and then correct/adjust for those.

### Backdoor adjustment
If a set of variables Z `satisfies the back-door criterion` relative to (X, Y ), then the __`causal effect of X on Y is identifiable and is given by the formula`__

$P(Y=y|do(X=x)) =\sum_{z} P(Y=y|X=x, Z=z)P(Z=z)$

See - the right hand-side can be estimated from the data since it has only conditional probabilities.



## Frontdoor
### Frontdoor Criterion
A set of variables Z satisfies the front-door criterion relative to an ordered pair of variables (X, Y ) in a DAG G if:
1. Z intercepts all directed paths from X to Y ;
1. There is `no unblocked` back-door path from X to Z; and
1. All back-door paths from Z to Y are blocked by X.


### Frontdoor Adjustment

If a set of variables Z satisfies the front-door criterion relative to (X, Y ) and if P(x, z) > 0, then the __`causal effect of X on Y is identifiable and is given by the formula`__

$P(Y=y|do(X=x)) =\sum_{z} P(Z=z|X=x) \sum_{x'} P(Y=y|X=x', Z=z)P(X=x')$


## Backdoor and Front door
When we cannot block a backdoor path because U (not using Z) is not observable, we can still have a front door path and therefore measure causal effect. A wonderful but lenghty explanation available [here](http://bayes.cs.ucla.edu/BOOK-2K/ch3-3.pdf). Backdoor path generally rests around confounders. If confounders are not observed, then the backdoor path has an active trail between X and Y. We must find other ways to counter that. Therefore we come in front door using the mediator M. And now we try to find:
- causal effect between X and M and block/adjust the backdoor between X and M
- causal effect between M and Y and block/adjust the backdor between M and Y
A intuitive explanation is given [here](https://stats.stackexchange.com/questions/350267/a-layman-understanding-of-the-difference-between-back-door-and-front-door-adjust).


## Identification of Causal Effects

For both back- and front-door settings, the goal was to convert


$P(y|do(x))$

into an expression with no do(.) notation. Or in other words an expression that can be derived from the observational distribution P(X,Y,Z) without compromising the calcuated value of the causal effect. That is what the right hand side of both the equations state! 

