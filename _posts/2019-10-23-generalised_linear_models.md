---
layout: distill
title: Generalised Linear Models
description: Classic Machine Learning Algorithms Series
date: 2019-10-23
tags: ClassicAlgorithms

authors:
  - name: Shane van Heerden
    affiliations:
      name: SUnORE, Stellenbosch University

bibliography: bibliography.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: 1. Introduction
  - name: 2. General linear models
  - name: 3. The exponential family
  - name: 4. Constructing a generalised linear model
  - name: 5. Logistic regression

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---

*This blog post was adapted from Section 4.4 in my [PhD dissertation](https://sunore.co.za/wp-content/uploads/2021/03/vanheerden_phd_2020.pdf).*

{% include figure.html path="assets/img/blog/blog5.1.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

This is the second post in a series of blog posts focusing on the some of the classic Machine Learning algorithms! In [my previous post](https://shanevanheerden.github.io/blog/2018/an_overview_of_supervised_machine_learning/), I gave a brief introduction of Supervised Learning together with many considerations that you need to bear in mind when framing a problem as a supervised learning problem. In this post, we will begin exploring the classic suite of statistical learning models, namely *Generalized Linear Models* (GLMs). Let's get started!

## 1. Introduction

The idea of generalising various statistical models into a single, overarching theoretical framework was first conceived in a 1972 paper by Nelder and Wedderburn<d-cite key="Nelder1972"></d-cite>. In that paper, the authors showed that many of the classical linear statistical models share a number of properties which may be exploited collectively, and that they admit a common method for computing parameter estimates. These commonalities facilitate the study of so-called GLMs as a single class of models, as opposed to considering the various models as an unrelated collection of special topics. In this section, it is shown that the well-known OLSR and logistic regression parametric models are, in fact, special cases of the family of GLMs. In their most basic form, GLMs are an extension of the concepts proposed by its predecessor, *general linear models*<d-footnote>The term <em>generalised linear model</em> and its abbreviation GLM are often confused with the term <em>general linear model</em>. The co-originator, John Nelder, has expressed regret over choosing this terminology<d-cite key="Senn2003"></d-cite>.</d-footnote>, which will serve as a starting point for the impending discussion.

***

## 2. General linear models

In a general linear model, the fundamental assumption is that the set of target variables $\mathcal{Y}=\{y^{(1)},\ldots,y^{(m)}\}$, now denoted by the vector $\mathbf{y}$, and the set of explanatory variables $\mathcal{X}=\{\mathbf{x}^{(i)},\ldots,\mathbf{x}^{(m)}\}$, now denoted by the *design matrix* $\mathbf{X}$, are related linearly by an equation of the form

\begin{equation}
\mathbf{y}=X\mathbf{\beta}+\mathbf{\epsilon},\label{4.eqn.asmp}
\end{equation}

where $\mathbf{\beta}$ is a column vector of unknown parameters, while $\mathbf{\epsilon}$ is a column vector containing independently and identically distributed error terms that capture any unmodelled effects or random noise in the data. It is assumed that the expected values $\mathbf{\mu}=E(\mathbf{y}\mid\mathbf{X};\mathbf{\beta})$ of the target variables have an identity relationship with the linear predictor $\mathbf{X}\mathbf{\beta}$ in the sense that the hypothesis of the general linear model is given by $h(\mathbf{\tilde{x}})=E(y\mid \mathbf{x};\mathbf{\beta})=\mathbf{x}\mathbf{\beta}$. The strong assumption made by a general linear model is that the error terms are assumed to be sampled from a Gaussian (or normal) distribution with zero mean and a constant variance $\sigma^2$, written as $\epsilon^{(i)}\sim\mathcal{N}(0,\sigma^2)$. The density of $\epsilon^{(i)}$ is, therefore, given by

\begin{equation}
p(\epsilon^{(i)})=\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(\epsilon^{(i)})^2}{2\sigma^2}\right),
\end{equation}

which implies that

\begin{equation}
p(y^{(i)}\mid\mathbf{x}^{(i)};\mathbf{\beta})=\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\mathbf{x}^{(i)}\mathbf{\beta})^2}{2\sigma^2}\right).
\end{equation}

The distribution of the observed target variables $\mathbf{y}$ are consequently characterised by $\mathbf{y}\mid\mathbf{X};\mathbf{\beta}\sim\mathcal{N}(\mathbf{\beta},\sigma^2)$. Since each observation $(\mathbf{x}^{(i)}, y^{(i)})$ is assumed to be independent of all the other observations, the joint density or *likelihood* $L(\mathbf{\beta})=L(\mathbf{\beta};\mathbf{X},\mathbf{y})=p(\mathbf{y}\mid\mathbf{X};\mathbf{\beta})$ of the data is given by the product of the individual probabilities

$$
\mkern-103pt L(\mathbf{\beta})=\prod_{i=1}^{m}p(y^{(i)}\mid\mathbf{x};\mathbf{\beta})
$$

\begin{equation}
=\prod_{i=1}^{m}\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\mathbf{x}^{(i)}\mathbf{\beta})^2}{2\sigma^2}\right).
\end{equation}

One would like to obtain an estimate of $\mathbf{\beta}$ which maximises the value of $L(\mathbf{\beta})$, called the *maximum likelihood estimation* (MLE). Instead of maximising $L(\mathbf{\beta})$, one may also maximise any strictly increasing function of the likelihood function. As a result, it is often more convenient to maximise a logarithmic form of the likelihood function (due to its relatively simple differentiability), appropriately called the *log likelihood* $\ell(\mathbf{\beta})$, where

$$
\mkern-107pt\ell(\mathbf{\beta})=\log L(\mathbf{\beta})
$$

$$
=\log\prod_{i=1}^{m}\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\mathbf{x}^{(i)}\mathbf{\beta})^2}{2\sigma^2}\right)
$$

$$
\hspace{2pt}=\sum_{i=1}^{m}\log\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\mathbf{x}^{(i)}\mathbf{\beta})^2}{2\sigma^2}\right)
$$

\begin{equation}
\hspace{3pt}=m\log\frac{1}{\sqrt{2\pi}\sigma}-\frac{1}{2\sigma^2}\sum_{i=1}^{m}(y^{(i)}-\mathbf{x}^{(i)}\mathbf{\beta})^2.
\end{equation}

From these results, one may conclude that the final choice of $\beta$-values does not depend on the variance $\sigma^2$ of the Gaussian distribution (this fact will prove useful later). Hence, for a general linear model, maximising $\ell(\mathbf{\beta})$ is equivalent to minimising the cost function

\begin{equation}
\sum_{i=1}^{m}(y^{(i)}-\mathbf{x}^{(i)}\mathbf{\beta})^2.\label{4.eqn.ols}
\end{equation}

The expression (\ref{4.eqn.ols}) may be recognised as that occurring in the well-known OLSR method for estimating the unknown parameters in a linear regression model. The optimal parameter values $\mathbf{\beta}^{\*}$ are, therefore, those that minimise the sum of the squared errors between the actual and predicted target variable, for all observations in the training set. For most statistical models, these parameter values are typically computed using a technique called *gradient decent*, the details of which are described by Ng<d-cite key="Ng2012"></d-cite>. In the case of OLSR, however, these $\mathbf{\beta}$-values may be computed analytically by solving the well-known normal equations<d-cite key="Hastie2009"></d-cite>, to yield

\begin{equation}
\mathbf{\beta}{^\*}=(\mathbf{X}^T\mathbf{X})^{-1}\mathbf{X}^T\mathbf{y}.\label{4.eqn.norm}
\end{equation}

***

## 3. The exponential family

The formulation of a GLM is traditionally achieved within the framework of the *exponential family* of distributions. A class of distributions is said to form part of the exponential family if their probability mass/density functions may be written in the form

\begin{equation}
p(y;\mathbf{\eta})=b(y)\exp(\mathbf{\eta}^T\mathbf{T}(y)-a(\mathbf{\eta})),\label{4.eqn.expfam}
\end{equation}

where $\mathbf{\eta}$ is the so-called *canonical parameter* (or *natural parameter*) of the distribution, $\mathbf{T}(y)$ is the sufficient statistic (for the majority of the distributions, it is often the case that $\mathbf{T}(y)=y$), $b(y)$ is the *underlying measure*, and $a(\mathbf{\eta})$ is the *log partition function*, which ensures that the distribution sums/integrates to one. Hence,

\begin{equation}
a(\mathbf{\eta})=\log\displaystyle\int b(y)\exp(\mathbf{\eta}^T\mathbf{T}(y))\hspace{0.8mm}\mathrm{d}{x}.
\end{equation}

Therefore, for a fixed choice of the functions $b(y)$, $\mathbf{T}(y)$, and $a(\mathbf{\eta})$, one may define a *family* (or set) of distributions. Furthermore, by varying the canonical parameter $\mathbf{\eta}$, one may obtain different distributions within this family. It may be shown that many well-known distributions, such as the Gaussian, Bernoulli, multinomial, Poisson, gamma, beta, exponential and inverse Gaussian form part of the exponential family of distributions<d-cite key="McCullagh1989"></d-cite>.

Using this formulation, one can show that the celebrated Gaussian (or normal) distribution is, in fact, a member of the exponential family of distributions. Recall that, during the derivation of a general linear model in Section 2, the value of $\sigma^2$ did not depend on the final choice of $\mathbf{\beta}$. Consequently, one may choose an arbitrary value for $\sigma^2$ without loss of generality. To simplify the subsequent derivation, $\sigma^2$ is set to unity (*i.e.* $\sigma^2=1$). In this way, the standard Gaussian distribution can be expanded to the form

$$
\mkern-89pt p(y;\mu)=\frac{1}{\sqrt{2\pi}}\exp\left(-\frac{1}{2}(y-\mu)^2\right)
$$

\begin{equation}
=\frac{1}{\sqrt{2\pi}}\exp\left(-\frac{1}{2}y^2\right)\cdot\exp\bigg(\mu y-\frac{1}{2}\mu^2\bigg).
\end{equation}

Notice that this form of the Gaussian probability density expression is in the exponential family form of (\ref{4.eqn.expfam}) with $\eta=\mu$, $T(y)=y$, $a(\eta)=\mu^2/2$ and $b(y)=(1/\sqrt{2\pi})\exp(-y^2/2)$.

As in the case of the Gaussian distribution, the well-known Bernoulli distribution can also be shown to reside within the set of exponential family distributions. The Bernoulli distribution specifies the distribution of a variable $y\in\{0,1\}$ with mean $\phi$ as

$$
\mkern-113pt p(y;\phi)=\phi^y(1-\phi)^{1-y}
$$

$$
\mkern-16pt=\exp(y\log\phi+(1-y)\log(1-\phi))
$$

\begin{equation}
=\exp\bigg(\log\left(\frac{\phi}{1-\phi}\right)y+\log(1-\phi)\bigg).
\end{equation}

Again notice that this form of the Bernoulli probability mass expression is in the exponential family of the form (\ref{4.eqn.expfam}) with $\eta=\log(\phi/(1-\phi))$, $T(y)=y$, $a(\eta)=-\log(1-\phi)=\log(1+e^\eta)$ and $b(y)=1$. Interestingly, if the definition of $\eta$ is inverted by solving for $\phi$, the relation $\phi=1/(1+e^{-\eta})$ is obtained. One may notice this as the familiar sigmoid function --- a fact that will prove useful during the derivation of logistic regression as a GLM.

***

## 4. Constructing a generalised linear model

Consider a classification or regression problem in which the objective is to predict some target variable $y$ as a function of the explanatory variables $\mathbf{x}$. In order to derive a GLM for this problem, McCullagh and Nelder<d-cite key="McCullagh1989"></d-cite> formally proposed the establishment of three model assumptions (or components):

1. A *random component*: Given a matrix of features $\mathbf{X}$ parameterised by $\mathbf{\beta}$, the target variables $\mathbf{y}$ are characterised by a probability distribution that belongs to the exponential family with canonical parameter $\mathbf{\eta}$, written as $\mathbf{y}\mid\mathbf{X};\mathbf{\beta}\sim$ ExpFamily($\mathbf{\eta}$).
2. A *systematic component*: The linear combination of the input features $\mathbf{X}$ produces a linear predictor $\mathbf{\eta}=\mathbf{X}\mathbf{\beta}$.
3. A *functional link*: A known monotonic differentiable *link function* $g(\cdot)$ relates the expected values $\mathbf{\mu}=E[\mathbf{T}(y)\mid\mathbf{X};\mathbf{\beta}]$ of the target variable (random component) to the linear predictor $\mathbf{\eta}=\mathbf{X}\mathbf{\beta}$ of features (systematic component), such that the hypothesis of the GLM is given by $h(\mathbf{\tilde{x}})=E[\mathbf{T}(y)\mid\mathbf{\tilde{x}};\mathbf{\beta}]=g(\mathbf{\tilde{x}}\mathbf{\beta})^{-1}$.

In the case of OLSR, Assumption 1 was merely a Gaussian distribution and Assumption 3 was and identity link function, implying that $\mathbf{\mu}=\mathbf{\eta}$. GLMs, therefore, extend the assumption in (\ref{4.eqn.asmp}) of general linear models to the relation

\begin{equation}
\mathbf{y}=g(\mathbf{X}\mathbf{\beta})^{-1}+\mathbf{\epsilon}.
\end{equation}

The distribution of a set of independent target variables $\mathbf{y}$ may now be characterised by any distribution in the exponential family while the homogeneity of variance is not satisfied (it rather varies as a function of the covariate mean)<d-cite key="Ng2012"></d-cite>. Furthermore, the target variable and the explanatory variables no longer assume a linear relationship but are rather related *via* a link function.

***

## 5. Logistic regression

As in the case of OLSR, one may also show that logistic regression is simply a special case of the family of GLMs. Consider the case in which the set of target variables $\mathcal{Y}=\{y^{(1)},\ldots,y^{(m)}\}$ are binary in nature (*i.e.* $y\in\{0,1\}$). In this case, it seems natural to model the conditional distribution of $\mathcal{Y}$ given the set of observations $\mathcal{X}$ as a Bernoulli distribution. Consequently, in the case of logistic regression, the hypothesis $h(\mathbf{x})$ is given by

\begin{equation}
\mkern-28pt h(\mathbf{x})=E[y\mid \mathbf{x};\mathbf{\beta}]\label{4.lr1}
\end{equation}
\begin{equation}
\mkern-41pt =\phi\label{4.lr2}
\end{equation}
\begin{equation}
\mkern-12pt =\frac{1}{1+e^{-\eta}}\label{4.lr3}
\end{equation}
\begin{equation}
=\frac{1}{1+e^{-\mathbf{\beta}^T\mathbf{x}}}.\label{4.lr4}
\end{equation}

Again, (\ref{4.lr1}) is the functional link of Assumption 3, (\ref{4.lr2}) follows from the fact that $y\mid\mathbf{x};\mathbf{b}\sim \text{Bern}(\phi)$, (\ref{4.lr3}) follows from Assumption 1 and the earlier derivation in Section 3 which showed that $\phi=1/(1+e^{-\eta})$ in the formulation of a Bernoulli distribution as an exponential family distribution. Finally, (\ref{4.lr4}) follows from Assumption 3.

Notice that $h(\mathbf{x})$ is always bounded between zero and one since $h(\mathbf{x})\rightarrow 1$ as $\mathbf{\beta}^T\mathbf{x}\rightarrow \infty$ and $h(\mathbf{x})\rightarrow 0$ as $\mathbf{\beta}^T\mathbf{x}\rightarrow -\infty$. By convention, $p(y=1\mid\mathbf{x};\mathbf{\beta})$ can be chosen as

\begin{equation}
p(y=1\mid\mathbf{x};\mathbf{\beta})=h(\mathbf{x})\label{4.eqn.lr1}
\end{equation}

which means that

\begin{equation}
p(y=0\mid\mathbf{x};\mathbf{\beta})=1-h(\mathbf{x}).\label{4.eqn.lr2}
\end{equation}

Given that $p(y=0\mid\mathbf{x};\mathbf{\beta})+p(y=1\mid\mathbf{x};\mathbf{\beta})=1$, (\ref{4.eqn.lr1}) and (\ref{4.eqn.lr2}) may be combined into the more compact Bernoulli representation

\begin{equation}
p(y\mid\mathbf{x};\mathbf{\beta})=h(\mathbf{x})^y(1-h(\mathbf{x}))^{1-y}.\label{4.eqn.lr3}
\end{equation}

Since the $m$ observations in $\mathcal{X}$ are assumed to be generated independently, the likelihood of the parameters $\mathbf{\beta}$ may, therefore, be expressed as

$$
\mkern-101pt \mathcal{L}(\mathbf{\beta})=p(\mathbf{y}\mid\mathbf{X};\mathbf{\beta})
$$

$$
\mkern-71pt =\prod_{i=1}^mp(y^{(i)}\mid\mathbf{x}^{(i)};\mathbf{\beta})
$$

\begin{equation}
=\prod_{i=1}^m\left(h(\mathbf{x}^{(i)})\right)^{y^{(i)}}\left(1-h(\mathbf{x}^{(i)})\right)^{1-y^{(i)}},\label{4.eqn.lr4}
\end{equation}

where, again, it is typically easier to maximise (\ref{4.eqn.lr4}) in terms of its log likelihood

$$\mkern-120pt \ell(\mathbf{\beta})=\log\mathcal{L}(\beta)$$

\begin{equation}
=\sum_{i=1}^my^{(i)}\log h(\mathbf{x}^{(i)}+(1-y^{(i)}\log(1-h(\mathbf{x}^{(i)}))\label{4.eqn.lr5}.
\end{equation}

As previously mentioned, optimal values of $\mathbf{\beta}^{\*}$ are typically realised by maximising (\ref{4.eqn.lr5}) using the method of gradient decent<d-cite key="Ng2012"></d-cite>.

Often, an additional *regularisation* term is added to (\ref{4.eqn.lr5}) so as to penalise (\ref{4.eqn.lr5}) for large choices of the value of $\beta$ in an attempt to avoid overfitting<d-cite key="Buhlmannk2011"></d-cite>. Although the notion of regularisation may be applied in the case of many different learning models, this concept is described exclusively in the context of the logistic regression model derivation. The two most popular regularisation types are *L1 regularisation* (often termed *lasso regression*) and *L2 regularisation* (often termed *ridge regression*). In the case of the former, the logistic regression log likelihood in (\ref{4.eqn.lr5}) can be redefined as

\begin{equation}
\ell(\mathbf{\beta})=\sum_{i=1}^my^{(i)}\log h(\mathbf{x}^{(i)})+(1-y^{(i)})\log(1-h(\mathbf{x}^{(i)}))-\lambda\sum_{j=1}^n|\beta_j|
\end{equation}

and, in the case of the latter as

\begin{equation}
\ell(\mathbf{\beta})=\sum_{i=1}^my^{(i)}\log h(\mathbf{x}^{(i)})+(1-y^{(i)})\log(1-h(\mathbf{x}^{(i)}))-\lambda\sum_{j=1}^n\beta_j^2,
\end{equation}

where $\lambda$ is a parameter used to inflate the regularisation penalty. If $\lambda=0$, no regularisation is applied while, if a large value of $\lambda$ is chosen, the $\beta$-values will approach zero.
