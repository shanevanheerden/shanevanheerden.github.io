---
layout: distill
title: Generalised Linear Models
description: ML Algorithms
date: 2019-10-23
tags: MLAlgorithms

authors:
  - name: Shane van Heerden
    affiliations:
      name: SUnORE, Stellenbosch University

bibliography: 2018-12-22-distill.bib

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
  - name: 6. References

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

*This blog post was adapted from my [PhD dissertation](https://sunore.co.za/wp-content/uploads/2021/03/vanheerden_phd_2020.pdf).*

{% include figure.html path="assets/img/blog2.1.png" class="img-fluid rounded z-depth-1" zoomable=true %}

This is the first post in a series of blog posts focusing on the some of the core Machine Learning algorithms! In this post, we will talk about 

## 1. Introduction

In the world of statistics, we often come across various models that aim to explain a particular phenomenon. However, what if we could find a way to unify these models under a single theoretical framework? That's precisely what Nelder and Wedderburn did in their groundbreaking 1972 paper [183]. They showed that many of the linear statistical models shared common properties and could be estimated using a common method. This led to the development of the Generalized Linear Models (GLMs), which allowed us to view different models as a single class, rather than a disjointed set of topics. In this article, we'll see how some well-known models like Ordinary Least Squares Regression and Logistic Regression can be considered as special cases of GLMs. But before that, let's start with a brief overview of the General Linear Models, which serve as the foundation for GLMs.

***

## 2. General linear models

In a general linear model, the fundamental assumption is that the set of target variables $\mathcal{Y}=\{y^{(1)},\ldots,y^{(m)}\}$, now denoted by the vector $\bm{y}$, and the set of explanatory variables $\mathcal{X}=\{\bm{x}^{(i)},\ldots,\bm{x}^{(m)}\}$, now denoted by the *design matrix* $\bm{X}$, are related linearly by an equation of the form

\begin{equation}
\mathbold{y}=\mathbold{X}\mathbold{\beta}+\mathbold{\epsilon},\label{4.eqn.asmp}
\end{equation}

where $\mathbold{\beta}$ is a column vector of unknown parameters, while $\bm{\epsilon}$ is a column vector containing independently and identically distributed error terms that capture any unmodelled effects or random noise in the data. It is assumed that the expected values $\bm{\mu}=E(\bm{y}\mid\bm{X};\bm{\beta})$ of the target variables have an identity relationship with the linear predictor $\bm{X}\bm{\beta}$ in the sense that the hypothesis of the general linear model is given by $h(\bm{\tilde{x}})=E(y\mid \bm{x};\bm{\beta})=\bm{x}\bm{\beta}$. The strong assumption made by a general linear model is that the error terms are assumed to be sampled from a Gaussian (or normal) distribution with zero mean and a constant variance $\sigma^2$, written as $\epsilon^{(i)}\sim\mathcal{N}(0,\sigma^2)$. The density of $\epsilon^{(i)}$ is, therefore, given by
\begin{equation*}
p(\epsilon^{(i)})=\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(\epsilon^{(i)})^2}{2\sigma^2}\right),
\end{equation*}
which implies that
\begin{equation*}
p(y^{(i)}\mid\bm{x}^{(i)};\bm{\beta})=\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\bm{x}^{(i)}\bm{\beta})^2}{2\sigma^2}\right).
\end{equation*}
The distribution of the observed target variables $\bm{y}$ are consequently characterised by $\bm{y}\mid\bm{X};\bm{\beta}\sim\mathcal{N}(\bm{\beta},\sigma^2)$. Since each observation $(\bm{x}^{(i)}, y^{(i)})$ is assumed to be independent of all the other observations, the joint density or {\em likelihood} $L(\bm{\beta})=L(\bm{\beta};\bm{X},\bm{y})=p(\bm{y}\mid\bm{X};\bm{\beta})$ of the data is given by the product of the individual probabilities
\begin{align*}
L(\bm{\beta})&=\prod_{i=1}^{m}p(y^{(i)}\mid\bm{x};\bm{\beta})\\
&=\prod_{i=1}^{m}\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\bm{x}^{(i)}\bm{\beta})^2}{2\sigma^2}\right).
\end{align*}
One would like to obtain an estimate of $\bm{\beta}$ which maximises the value of $L(\bm{\beta})$, called the {\em maximum likelihood estimation} (MLE). Instead of maximising $L(\bm{\beta})$, one may also maximise any strictly increasing function of the likelihood function. As a result, it is often more convenient to maximise a logarithmic form of the likelihood function (due to its relatively simple differentiability), appropriately called the {\em log likelihood} $\ell(\bm{\beta})$, where
\begin{align}
\ell(\bm{\beta})&=\log L(\bm{\beta})\\
&=\log\prod_{i=1}^{m}\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\bm{x}^{(i)}\bm{\beta})^2}{2\sigma^2}\right)\nonumber\\
&=\sum_{i=1}^{m}\log\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(y^{(i)}-\bm{x}^{(i)}\bm{\beta})^2}{2\sigma^2}\right)\nonumber\\
&=m\log\frac{1}{\sqrt{2\pi}\sigma}-\frac{1}{2\sigma^2}\sum_{i=1}^{m}(y^{(i)}-\bm{x}^{(i)}\bm{\beta})^2.
\end{align}
From these results, one may conclude that the final choice of $\beta$-values does not depend on the variance $\sigma^2$ of the Gaussian distribution (this fact will prove useful later). Hence, for a general linear model, maximising $\ell(\bm{\beta})$ is equivalent to minimising the cost function
\begin{equation}
\sum_{i=1}^{m}(y^{(i)}-\bm{x}^{(i)}\bm{\beta})^2.\label{4.eqn.ols}
\end{equation}
The expression (\ref{4.eqn.ols}) may be recognised as that occurring in the well-known OLSR method for estimating the unknown parameters in a linear regression model. The optimal parameter values $\bm{\beta}^*$ are, therefore, those that minimise the sum of the squared errors between the actual and predicted target variable, for all observations in the training set. For most statistical models, these parameter values are typically computed using a technique called {\em gradient decent}, the details of which are described by Ng \cite{Ng2012}. In the case of OLSR, however, these $\bm{\beta}$-values may be computed analytically by solving the well-known normal equations \cite{Hastie2009}, to yield
\begin{equation*}
\bm{\beta}^*=(\bm{X}^T\bm{X})^{-1}\bm{X}^T\bm{y}.\label{4.eqn.norm}
\end{equation*}

***

## 6. References

1. 