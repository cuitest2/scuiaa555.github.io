---
layout: post
title: "Variance Reduction of Monte Carlo"
description: "A study note on variance reduction techniques that are introduced in the book of Paul Glasserman's. This note includes control variates, antithetic variates. stratified sampling and importance sampling."
date: 2017-04-01
tags: [finance, notes]
comments: true
share: true
---

Background: By using Monte Carlo simulation to price derivatives, we always face the problem that simulate $N$ paths of stocks or other state variables or function of them $f(\cdot)$ as one-dimension $Y$ or d-dimension ($Y_1,Y_2,...,Y_d$ like a discrete path of underlying asset) to estimate $E[Y]=\mu$. The $n$ paths are denoted as $y_1,...,y_n$, where each $y_i$ is a d-dimensional realisation (replication)of random vectors.

The usual estimator is $\hat{\mu}=\bar{Y}=\frac{y_1+y_2+...+y_n}{n}$.

### 1. Control Variates

Control variate exploits information about the errors in estimates of known quantities to reduce the error in an estimate of an unknown quantity.

On each replication we calculate another output $x_i$ along with $y_i$. Suppose that the pairs $(x_i,y_i)$,$i=1,2,...,n$ are i.i.d. and that the expectation $E[X]$ of the $x_i$ is known. Then, for any fixed $b$ we can calculate

$$y_i(b)=y_i-b(x_i-E[X]),$$

and the new estimator $\hat{\mu}$ is

$$\hat{\mu}=\bar{Y}(b)=\frac{1}{n}\sum_{i=1}^n(y_i-b(x_i-E(X))).$$

The variance $\hat{\mu}$

$$Var(\hat{\mu})=\frac{Var(Y)+b^2Var(X)-2bCov(X,Y)}{n}\leq\frac{Var(Y)}{n}$$

if and only if

$$b^2Var(X)\leq 2bCov(X,Y).$$

The optimal $b^*$ is given by

$$b^*=\frac{Cov(X,Y)}{Var(X)},$$

and estimated by

$$\hat{b}=\frac{\sum_{i=1}^n(x_i-\bar{X})(y_i-\bar{Y})}{\sum_{i=1}^n(x_i-\bar{X})^2}.$$

An example is to use the underlying asset

$$\frac{1}{n}\sum_{i=1}^n(y_i-b[S_i(T)-e^{rT}S(0)]).$$

In multi-dimensional case,

$$\bar{Y}(b)=\bar{Y}-b^T(\bar{X}-E[X]).$$

Non-linear controls, for example

$$\bar{Y}\frac{E[X]}{\bar{X}}$$

adjusts $\bar{Y}$ upward if $0<\bar{X}<E[X]$, downward if $0<E[X]<\bar{X},$ and thus may be attractive if $x_i$ and $y_i$ are positively correlated. Other estimators of this type include

$$\bar{Y}\frac{\bar{X}}{E[X]} \quad and \quad \bar{Y}\exp(\bar{X}-E[X])\quad and \quad \bar{Y}^{(\bar{X}/E[X])}.$$

To analyse the large-sample behaviour is based on delta method, i.e.,

$$\sqrt{n}[h(\bar{X})-h(\mu)]\rightarrow N(0, \nabla h(\mu)\Sigma \nabla h(\mu)^T),$$

where covariance matrix of $\vec{X}$ and $Y$.


### 2. Antithetic Variates

The method of antithetic variates attempts to reduce variance by introducing negative dependence between pairs of replications.

In one-dimensional case, $F^{-1}(U)$ and $F^{-1}(1-U)$ both have distribution $F$.

In multi-dimensional case, we need the additional assumption that the random variables in the random vectors are independent, then $(F_1^{-1}(U_1),...,F_d^{-1}(U_d))$ and ($F_1^{-1}(1-U_1),...,F_d^{-1}(1-U_d))$ both have the same distribution.

Antithetic sampling produces a sequence of pairs of observations $(y_1,\tilde{y}_1),(y_2,\tilde{y}_2),...(y_n,\tilde{y}_n)$ whose features are

- the pairs $(y_1,\tilde{y}_1),(y_2,\tilde{y}_2),...(y_n,\tilde{y}_n)$ are i.i.d;
- for each $i$, $y_i$ and $\tilde{y}_i$ have the same distribution, though ordinarily they are not independent.

The antithetic variates estimator is

$$\hat{\mu}=\frac{1}{n}\sum_{i=1}^n(\frac{y_i+\tilde{y}_i}{2}).$$

We assume that the computational effort required to generate a pair $(y_i,\tilde{y}_i)$ is approximately twice the effort required to generate $y_i$. Then

$$Var(\hat{\mu})\leq Var [\frac{1}{2n}\sum_{i=1}^{2n}y_i]$$

if and only if

$$Cov(y_i,\tilde{y}_i)\leq 0.$$

To generate a Brownian path, assume that the discretise $(0,T)$ into $d$ intervals. Because of the property of independent increments of Brownian motion, random variables inside $(W_1,...,W_d)$ are independent. So the antithetic sampling can be implemented as

path 1: $W_1^1,...,W_d^1$ $\rightarrow y_1$<br> 
        $\quad \quad \  -W_1^1,...,-W_d^1$ $\rightarrow \tilde{y}_1$<br>
        ...<br>
path n: $W_1^n,...,W_d^n$ $\rightarrow y_n$<br> 
        $\quad \quad \  -W_1^n,...,-W_d^n$ $\rightarrow \tilde{y}_n$<br>
        
        
### 3. Stratified Sampling

Stratified sampling refers broadly to any sampling mechanism that constrains the fraction of observations drawn from specific subsets (or strata) of the sample space, according to

$$
\begin{align*}
E[Y]=E_X{E(Y|X)}&=\sum_{i=1}^KE(Y|X\in A_i)P(X\in A_i)\\
&=\sum_{i=1}^Kp_iE(Y|X\in A_i).
\end{align*}
$$

The stratified sampling estimator

$$\hat{\mu}=\sum_{i=1}^Kp_i\cdot\frac{1}{n_i}\sum_{j=1}^{n_i}y_{ij},$$

where $y_{ij}$ is the j-th sample from i-th strata.

There are two main issues that need to be considered

- choosing the stratification variable $X$ (could be $Y$ itself), the strata $A_1,...,A_K$, and the allocation $n_1,...,n_K$;
- generating samples from the distribution of $(X,Y)$ conditional on $X\in A_i$.

Stratifying uniforms: 

$$V_i=\frac{i-1}{n}+\frac{U_i}{n},$$

and it is used to generate $Y$ conditional on $Y\in A_i$, since 

$$P(F^{-1}(V_i)\leq x)=P(V_i\leq F(x))=P(U_i \in B_i, P(B_i)=p_i)=p_i.$$

A crude but almost universally applicable method for generating samples conditional on a stratum generates unconditional samples and keeps those that fall in the target set.

The variance 

$$Var(\hat{\mu})=\sum_{i=1}^Kp_i^2Var[\frac{1}{n_i}\sum_{j=1}^{n_i}Y_{ij}]=\sum_{i=1}^Kp_i^2\frac{\sigma_i^2}{n_i}=\frac{\sigma^2(q)}{n},$$

with

$$\sigma^2(q)=\sum_{i=1}^K\frac{p_i^2}{q_i}\sigma_i^2, \quad and \quad \sigma_i^2=Var[Y_{ij}]=Var[Y|X\in A_i]\quad and \quad q_i=n_i/n.$$


In the case of a proportional allocation of samples to strata, $q_i=p_i$, stratified sampling always decrease variance since

$$Var[Y]=\sum_{i=1}^Kp_i\sigma_i^2+\sum_{i=1}^Kp_i\mu_i^2-(\sum_{i=1}^Kp_i\mu_i)^2\geq \sum_{i=1}^Kp_i\sigma_i^2=Var(\hat{\mu})$$

by Jensen's inequality. And the optimal allocation is

$$q_i^*=\frac{p_i\sigma_i}{\sum_{l=1}^Kp_l\sigma_l}.$$

#### Terminal stratification

In the pricing of options, the most important feature of the path of an underlying asset is often its value at option expiration: much of the variability in the option's payoff can potentially be eliminated by stratifying the terminal value. Suppose that we need to stratify a discrete Brownian path $W(t_1),...W(t_m)$ and that we want to stratify the terminal value $W(t_m)$. We can accomplish this through Brownian bridge construction and show it in the following algorithm:

```
for i=1,...,K
    generate U~Unif[0,1]
    V<-(i-1+U)/K
    W(t_m)<-\sqrt(t_m)\Phi^{-1}(V)
    for j=1,...,m-1
        generate Z~N(0,1)
        W(t_j)<- Brownian bridge of W(t_m) and W(t_{j-1})
```

Usually, the terminal value of state variable is not the Brownian motion itself, instead, the terminal value of state variable say $S(T_m)$ would be determined by

$$\int_0^{t_m}\sigma(u)dW_u,$$

so we may prefer to stratify this integral instead. If $\sigma(t)$ is piecewise linear, we can simplify this to

$$W(t_m)\sigma(t_{m-1})+\sum_{i=1}^{m-1}W(t_i)[\sigma(t_{i-1})-\sigma(t_i)].$$

This is the special case of the problem of generating a multivariate normal random vector stratified along some projection. Suppose that $\xi\backsim N(\mu,\Sigma)$ in $R^d$ and that we want to generate $\xi$ with $X=v^T\xi$ stratified for some fixed vector $v\in R^d$. Since they are all normally distributed

$$(\xi|X=x)\backsim N(\frac{\Sigma v}{v^T\Sigma v}x,\Sigma-\frac{\Sigma vv^T\Sigma}{v^T\Sigma v}).$$


### 4. Importance Sampling

When we switch from the objective probability measure to the risk-neutral measure, our goal is usually to obtain a more convenient representation of an expected value. However, in importance sampling, we change measures to try to give more weight to "important" outcomes thereby increasing sampling efficiency.

Now we change our notation as considering the problem of estimating 

$$\mu=E[h(X)]=\int h(x)f(x)dx.$$

Then we can alternatively represent $\mu$ as

$$\mu=\int h(x)\frac{f(x)}{g(x)}g(x)dx.$$

This integral can be interpreted as an expectation with respect to the density $g$, we may therefore write

$$\mu=\tilde{E}[h(X)\frac{f(X)}{g(X)}].$$

The importance sampling estimator associated with $g$ is

$$\hat{\mu}=\frac{1}{n}\sum_{i=1}^nh(X_i)\frac{f(X_i)}{g(X_i)}.$$

The effective of variance reduction highly depends on the choice of $g$. The variance under new measure ranges from 0 to $+\infty$.

Consider a concrete case that the underlying asset at a discrete time grid depends on the "increment" random variables, say $X_1,...,X_m$. For example, $X_i$ are normal in a GBM model case for stocks. Then if further assume $X_i$ has the same density (which means further they have equal time interval), a path-dependent payoff can be calculated under the new measure by

$$E[h(S(t_1),S(t_2),...S(t_m))]=\tilde{E}[h(S(t_1),...S(t_m))\prod_{i=1}^m\frac{f(X_i)}{g(X_i)}].$$

Also the above relationship applies to the case of stopping time as

$$E[h(S(t_1),S(t_2),...S(t_N))1\{N<\infty\}]=\tilde{E}[h(S(t_1),...S(t_N))\prod_{i=1}^N\frac{f(X_i)}{g(X_i)}1\{N<\infty\}],$$

where $N$ is a stopping time.


#### Exponential change of measure

For a cumulative distribution function $F$ on $R$, define the *cumulant generating function* as

$$\psi(\theta)=\log \int_{-\infty}^{+\infty}e^{\theta x}dF(x).$$

For each $\theta\in \Theta$, set

$$F_{\theta}(x)=\int_{-\infty}^x e^{\theta u-\psi(\theta)}dF(u);$$

each $F_{\theta}$ is cdf and forms an exponential family of distributions. The likelihood ratio for this transformation is

$$\prod_{i=1}^m\frac{dF_0(X_i)}{dF_{\theta}(X_i)}=\exp(-\theta \sum_{i=1}^m X_i+n \psi(\theta)).$$

For example, let $f$ be the univariate standard normal density and $g$ the univariate normal density with mean $\mu$ and variance 1. Then the likelihood ratio is

$$\prod_{i=1}^m\frac{f(X_i)}{g(X_i)}=\exp(-\mu \sum_{i=1}^m X_i+ \frac{m}{2}\mu^2).$$