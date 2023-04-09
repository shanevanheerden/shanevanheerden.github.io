---
layout: distill
title: Decision Tree Learning
description: Classic Machine Learning Algorithms Series
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
  - name: 2. Classification and regression trees
  - name: 3. Random forests
  - name: 4. The C4.5 algorithm
  - name: 5. Gradient boosted decision trees
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

*This blog post was adapted from Section 4.5 in my [PhD dissertation](https://sunore.co.za/wp-content/uploads/2021/03/vanheerden_phd_2020.pdf).*

{% include figure.html path="assets/img/blog6.1.png" class="img-fluid rounded z-depth-1" zoomable=true %}

This is the second post in a series of blog posts focusing on the some of the classic Machine Learning algorithms! In this post, we will talk about a much-loved class of machine learning models, namely Decision Trees. Let's get started!

## 1. Introduction

*Decision tree learning* is one of the most widely used supervised learning approaches --- a fact that is primarily attributed to their capability of performing both classification and regression tasks, their relatively simple mathematical formulation and their ability to represent their underlying decision making process visually and explicitly \cite{Gupta2017}. A decision tree can, in essence, be explained in terms of two entities, namely decision *nodes* and *leaves*. Decision nodes describe the rule by which data are partitioned into smaller sub-parts, while leaves denote the final decisions or outcomes \cite{Kulkarni2017}. There exists a wide variety of tree-based learning algorithms in the literature, the most widely used being *classification and regression trees* (CART), *random forests*, the *C4.5* algorithm and gradient boosted decision trees.

***

## 2. Classification and regression trees

CART is a popular non-parametric algorithm for producing either classification or regression trees, depending on the defined target variable \cite{Marsland2009}. The explanatory variables $\mathcal{X}=\{\bm{x}^{(1)},\ldots,\bm{x}^{(m)}\}$ can be either numerical or categorical \cite{Hand2001}. The stages in a CART analysis are two-fold and employ a binary recursive partitioning method. The first stage is the tree growth stage where so-called *parent* nodes are recursively partitioned into two more homogeneous *child( nodes. The second stage is concerned with limiting the overall tree growth by computing the optimal tree size. These two steps are further elucidated in the remainder of this sub-section in the context of a classification tree since only this paradigm is applied later on in this dissertation.

The process of producing a classification tree begins at an initial node, called the *root node*, in which the entire training set is contained. Tree growth is achieved by partitioning each node in such a way as to maximise a defined splitting criterion. If the $j$\textsuperscript{th} attribute is defined as the parent node $d_p$, then there exists an optimal splitting value $x_j^*$ for this node. Once this value has been determined, an *if-then* splitting procedure may follow whereby all observations with a $j$\textsuperscript{th} attribute value of $x_j^*$ or greater are partitioned into a right-hand child node $t_r$, as illustrated in Figure~\ref{4.fig.node}, while all observations with a $j$\textsuperscript{th} attribute value less than $x_j^*$ are partitioned into a left-hand child node $t_l$ \cite{Rashidi2014}. The question, however, remains as to how to determine this "optimal" splitting value $x_j^*$ for the $j$\textsuperscript{th} attribute.

{% include figure.html path="assets/img/blog6.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}

According to Lewis \cite{Lewis2000}, the primary goal of splitting a parent node into two child constituents is to achieve the largest improvement in predictive accuracy by maximising the *homogeneity* or *purity* of the two subsequent child nodes. The *impurity* of a node $t$ is calculated by means of an *impurity function* $i(t)$ \cite{StatSoft2016}, which is defined in terms of the impurity of the parent node, denoted by $i(t_p)$, and the combined impurity of the associated child nodes, denoted by $i(t_c)$. Regardless of the child node split, the impurity of a parent node remains constant. The change in impurity, given a specific child split, may therefore be expressed as

\begin{equation}
\Delta i(t)=i(t_p)-E[i(t_c)],\label{4.eqn.tree1}
\end{equation}

where $E[i(t_c)]$ is the expected impurity of the two child nodes, which can also be expressed in terms of the probability of being split into the left- and right-hand nodes, denoted by $p(t_l)i(t_l)$ and $p(t_r)i(t_r)$, respectively. Consequently, in the case of a classification tree, all possible attribute values for all $n$ attributes in $\mathcal{X}$ must be considered as possible "best split," resulting in the maximum value of (\ref{4.eqn.tree1}), expressed mathematically at a single parent node, as

\begin{equation}
\underset{\substack{x_j<x_j^*\\j\in\{1,\ldots,m\}}}{\arg\max}\hspace{1.5mm}i(t_p)-p(t_l)i(t_l)-p(t_r)i(t_r).\label{4.eqn.tree2}
\end{equation}

Although this process of maximising the change in impurity described in (\ref{4.eqn.tree2}) is common to both classification and regression trees, the choice of impurity function $i(t)$ is defined in a different manner \cite{Moisen2008}. Many different impurity functions have been proposed in the literature. In the case of classification trees, however, the most widely used is the *Gini splitting rule* and the *Twoing splitting rule* \cite{Timofeev2004}.

A tree that has been grown to the point where it fits the training data almost perfectly typically results in an overly complex model that performs poorly in respect of the validation data, as illustrated in Figure~\ref{4.fig.treesize}. A tree that is too small, on the other hand, is not able to discover the underlying relationships present in the data. Consequently, the ideal tree size is one that achieves a suitable trade-off between overfitting and underfitting the training data. A CART is, therefore, grown to a near optimal size by employing one of two methods: The growth of the tree can be terminated according to (1) a *stopping criterion* or (2) employing a *pruning* procedure.

{% include figure.html path="assets/img/blog6.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}

As the name implies, the use of the stopping criterion approach forces the recursive tree growing procedure to be terminated once a specified criterion is met. Rashidi *et al.* \cite{Rashidi2014} described two such criteria. The first of these is the *maximum tree depth* criterion which terminates tree growth after a pre-specified number of branching levels have been created. The second is the *minimum node size to split* criterion which terminates tree growth at a specific node if the size of the node (*i.e.* the number of observations contained in the node) is less than a pre-specified value.

Although the stopping criterion provides a simplified method for limiting the growth of a tree, the more popular method is that of pruning \cite{Timofeev2004}. According to this procedure, a tree is first grown to its maximum size and then pruned backwards to a more suitable size \cite{Moisen2008}. There exists a wide variety of methods for determining the optimal size to which a tree should be pruned backwards, including *reduced-error pruning*, *pessimistic pruning*, *rule post-pruning*, and *cost complexity pruning* \cite{James2013, Rokach2008}. Performance comparisons of the various pruning methods have been conducted in multiple studies, with the majority finding that no single pruning method is superior under all circumstances \cite{Esposito1997, Mingers1989, Quinlan1987} --- a finding that echoes the *No Free Lunch* theorem\footnote{The No Free Lunch theorem states that "any two optimization algorithms are equivalent when their performance is averaged across all possible problems" \cite{Wolpert2005}.}.

Although CART is a powerful predictive model in its original form, there are three attractive methods for enhancing this standard learning model \cite{Moisen2008}. The first of these relies on a procedure called *bagging* which produces an aggregation of multiple bootstrap samples (hence, bagging is also referred to as *bootstrap aggregating*), as described in \S\ref{4.sec.bootstrap}. Although the notion of bagging may be applied to any learning model in an attempt to improve its stability and accuracy \cite{James2013}, this procedure is described here exclusively in the context of the CART learning model so as to demonstrate its working. Given a training set $\mathcal{S}$, a total of $b$ separate data subsets $\mathcal{S}_1,\ldots,\mathcal{S}_b$ are randomly drawn from $\mathcal{S}$ and used to construct $b$ CART models. The predictive capability of CART model $i$ is then validated in respect of the data not used to construct the model (*i.e.* the set $\mathcal{S}\mbox{\textbackslash}\mathcal{S}_i$). In the case of a classification task, the final categorisation is given by the majority vote of the $b$ CART models (for this reason, $b$ should be chosen as odd in the case of a binary classification problem to avoid ties) while, in the case of a regression task, the results of the independent CART predictions are averaged \cite{James2013}. The predictions achieved by this bagging approach are reported to exhibit less variance than that of a single CART model \cite{Hastie2009}.

The second CART enhancement method is that of *boosting*. Much like bagging, the notion of boosting may be applied to any learning model \cite{James2013}, but, for demonstration purposes, is described here exclusively in the context of the CART learning model. Boosting involves the construction of $b$ CART models *sequentially* from an adaptive training data subset, where each new tree utilises the information discovered by its predecessors. This stands in contrast to bagging, which constructs multiple CART models *independently* from bootstrapped data samples. Initially, all observations are assigned an equal weighting and are randomly sampled according to this weighting to be included in the training sample set $\mathcal{S}$. During the construction and evaluation of each new CART model, the frequently misclassified observations (known as *problematic observations*) are given more weight, resulting in their more frequent inclusion in the new sample $\mathcal{S}^{\prime}$ for training future CART models. This effectively allows future models to correct the mistakes of their predecessors, thus improving the models' overall prediction ability \cite{Moisen2008}. It should be noted, however, that boosting may suffer from overfitting if the value of $b$ is chosen too large \cite{James2013}.

The third and, by far, most popular CART enhancement is called *random forests* which is discussed, in its own right, in the next section.

***

## 3. Random forests



***

## 4. The C4.5 algorithm



***

## 5. Gradient boosted decision trees



***

## 6. References

1. 
