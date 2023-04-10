---
layout: distill
title: Decision Tree Learning
description: Classic Machine Learning Algorithms Series
date: 2018-11-1
tags: MLAlgorithms

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
  - name: 2. Classification and regression trees
  - name: 3. Random forests
  - name: 4. The C4.5 algorithm
  - name: 5. Gradient boosted decision trees

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

*Decision tree learning* is one of the most widely used supervised learning approaches --- a fact that is primarily attributed to their capability of performing both classification and regression tasks, their relatively simple mathematical formulation and their ability to represent their underlying decision making process visually and explicitly<d-cite key="Gupta2017"></d-cite>. A decision tree can, in essence, be explained in terms of two entities, namely decision *nodes* and *leaves*. Decision nodes describe the rule by which data are partitioned into smaller sub-parts, while leaves denote the final decisions or outcomes<d-cite key="Kulkarni2017"></d-cite>. There exists a wide variety of tree-based learning algorithms in the literature, the most widely used being *classification and regression trees* (CART), *random forests*, the *C4.5* algorithm and *gradient boosted decision trees*.

***

## 2. Classification and regression trees

CART is a popular non-parametric algorithm for producing either classification or regression trees, depending on the defined target variable<d-cite key="Marsland2009"></d-cite>. The explanatory variables $\mathcal{X}=\{\bm{x}^{(1)},\ldots,\bm{x}^{(m)}\}$ can be either numerical or categorical<d-cite key="Hand2001"></d-cite>. The stages in a CART analysis are two-fold and employ a binary recursive partitioning method. The first stage is the tree growth stage where so-called *parent* nodes are recursively partitioned into two more homogeneous *child* nodes. The second stage is concerned with limiting the overall tree growth by computing the optimal tree size. These two steps are further elucidated in the remainder of this sub-section in the context of a classification tree since only this paradigm is applied later on in this dissertation.

The process of producing a classification tree begins at an initial node, called the *root node*, in which the entire training set is contained. Tree growth is achieved by partitioning each node in such a way as to maximise a defined splitting criterion. If the $j$<sup>th</sup> attribute is defined as the parent node $d_p$, then there exists an optimal splitting value $x_j^{\*}$ for this node. Once this value has been determined, an *if-then* splitting procedure may follow whereby all observations with a $j$<sup>th</sup> attribute value of $x_j^{\*}$ or greater are partitioned into a right-hand child node $t_r$, as illustrated in Figure 1, while all observations with a $j$<sup>th</sup> attribute value less than $x_j^{\*}$ are partitioned into a left-hand child node $t_l$<d-cite key="Rashidi2014"></d-cite>. The question, however, remains as to how to determine this "optimal" splitting value $x_j^{\*}$ for the $j$<sup>th</sup> attribute.

{% include figure.html path="assets/img/blog6.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 1:</em> Splitting a parent node into corresponding child nodes in a CART<d-cite key="Timofeev2004"></d-cite>.
</div>

According to Lewis <d-cite key="Lewis2000"></d-cite>, the primary goal of splitting a parent node into two child constituents is to achieve the largest improvement in predictive accuracy by maximising the *homogeneity* or *purity* of the two subsequent child nodes. The *impurity* of a node $t$ is calculated by means of an *impurity function* $i(t)$<d-cite key="StatSoft2016"></d-cite>, which is defined in terms of the impurity of the parent node, denoted by $i(t_p)$, and the combined impurity of the associated child nodes, denoted by $i(t_c)$. Regardless of the child node split, the impurity of a parent node remains constant. The change in impurity, given a specific child split, may therefore be expressed as

\begin{equation}
\Delta i(t)=i(t_p)-E[i(t_c)],\label{4.eqn.tree1}
\end{equation}

where $E[i(t_c)]$ is the expected impurity of the two child nodes, which can also be expressed in terms of the probability of being split into the left- and right-hand nodes, denoted by $p(t_l)i(t_l)$ and $p(t_r)i(t_r)$, respectively. Consequently, in the case of a classification tree, all possible attribute values for all $n$ attributes in $\mathcal{X}$ must be considered as possible "best split," resulting in the maximum value of (\ref{4.eqn.tree1}), expressed mathematically at a single parent node, as

\begin{equation}
\underset{\substack{x_j<x_j^{\*},j\in\{1,\ldots,m\}}}{\arg\max}\hspace{1.5mm}i(t_p)-p(t_l)i(t_l)-p(t_r)i(t_r).\label{4.eqn.tree2}
\end{equation}

Although this process of maximising the change in impurity described in (\ref{4.eqn.tree2}) is common to both classification and regression trees, the choice of impurity function $i(t)$ is defined in a different manner<d-cite key="Moisen2008"></d-cite>. Many different impurity functions have been proposed in the literature. In the case of classification trees, however, the most widely used is the *Gini splitting rule* and the *Twoing splitting rule*d-cite key="Timofeev2004"></d-cite>.

A tree that has been grown to the point where it fits the training data almost perfectly typically results in an overly complex model that performs poorly in respect of the validation data, as illustrated in Figure 2. A tree that is too small, on the other hand, is not able to discover the underlying relationships present in the data. Consequently, the ideal tree size is one that achieves a suitable trade-off between overfitting and underfitting the training data. A CART is, therefore, grown to a near optimal size by employing one of two methods: The growth of the tree can be terminated according to (1) a *stopping criterion* or (2) employing a *pruning* procedure.

{% include figure.html path="assets/img/blog6.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 2:</em> The change in misclassification rate according to a tree’s size<d-cite key="Steynberg2016"></d-cite>.
</div>

As the name implies, the use of the stopping criterion approach forces the recursive tree growing procedure to be terminated once a specified criterion is met. Rashidi *et al.*<d-cite key="Rashidi2014"></d-cite> described two such criteria. The first of these is the *maximum tree depth* criterion which terminates tree growth after a pre-specified number of branching levels have been created. The second is the *minimum node size to split* criterion which terminates tree growth at a specific node if the size of the node (*i.e.* the number of observations contained in the node) is less than a pre-specified value.

Although the stopping criterion provides a simplified method for limiting the growth of a tree, the more popular method is that of pruning<d-cite key="Timofeev2004"></d-cite>. According to this procedure, a tree is first grown to its maximum size and then pruned backwards to a more suitable size<d-cite key="Moisen2008"></d-cite>. There exists a wide variety of methods for determining the optimal size to which a tree should be pruned backwards, including *reduced-error pruning*, *pessimistic pruning*, *rule post-pruning*, and *cost complexity pruning*<d-cite key="James2013, Rokach2008"></d-cite>. Performance comparisons of the various pruning methods have been conducted in multiple studies, with the majority finding that no single pruning method is superior under all circumstances<d-cite key="Esposito1997, Mingers1989, Quinlan1987"></d-cite> --- a finding that echoes the *No Free Lunch* theorem<d-footnote>The No Free Lunch theorem states that "any two optimization algorithms are equivalent when their performance is averaged across all possible problems"<d-cite key="Wolpert2005"></d-cite>.</d-footnote>

Although CART is a powerful predictive model in its original form, there are three attractive methods for enhancing this standard learning model<d-cite key="Moisen2008"></d-cite>. The first of these relies on a procedure called *bagging* which produces an aggregation of multiple bootstrap samples (hence, bagging is also referred to as *bootstrap aggregating*). Although the notion of bagging may be applied to any learning model in an attempt to improve its stability and accuracy<d-cite key="James2013"></d-cite>, this procedure is described here exclusively in the context of the CART learning model so as to demonstrate its working. Given a training set $\mathcal{S}$, a total of $b$ separate data subsets $\mathcal{S}_1,\ldots,\mathcal{S}_b$ are randomly drawn from $\mathcal{S}$ and used to construct $b$ CART models. The predictive capability of CART model $i$ is then validated in respect of the data not used to construct the model (*i.e.* the set $\mathcal{S}\mbox{\textbackslash}\mathcal{S}_i$). In the case of a classification task, the final categorisation is given by the majority vote of the $b$ CART models (for this reason, $b$ should be chosen as odd in the case of a binary classification problem to avoid ties) while, in the case of a regression task, the results of the independent CART predictions are averaged<d-cite key="James2013"></d-cite>. The predictions achieved by this bagging approach are reported to exhibit less variance than that of a single CART model<d-cite key="Hastie2009"></d-cite>.

The second CART enhancement method is that of *boosting*. Much like bagging, the notion of boosting may be applied to any learning model<d-cite key="James2013"></d-cite>, but, for demonstration purposes, is described here exclusively in the context of the CART learning model. Boosting involves the construction of $b$ CART models *sequentially* from an adaptive training data subset, where each new tree utilises the information discovered by its predecessors. This stands in contrast to bagging, which constructs multiple CART models *independently* from bootstrapped data samples. Initially, all observations are assigned an equal weighting and are randomly sampled according to this weighting to be included in the training sample set $\mathcal{S}$. During the construction and evaluation of each new CART model, the frequently misclassified observations (known as *problematic observations*) are given more weight, resulting in their more frequent inclusion in the new sample $\mathcal{S}^{\prime}$ for training future CART models. This effectively allows future models to correct the mistakes of their predecessors, thus improving the models' overall prediction ability<d-cite key="Moisen2008"></d-cite>. It should be noted, however, that boosting may suffer from overfitting if the value of $b$ is chosen too large<d-cite key="James2013"></d-cite>.

The third and, by far, most popular CART enhancement is called *random forests* which is discussed, in its own right, in the next section.

***

## 3. Random forests

Introduced by Breiman<d-cite key="Breiman2001"></d-cite> and often considered to be a panacea of all data science problems<d-cite key="AnalyticsVidhya2016"></d-cite>, random forests is a versatile and simplistic learning model that has proven effective in a wide range of classification and regression tasks<d-cite key="Cutler2011"></d-cite>. Other advantages of the method of random forests include its ability to handle categorical and numerical attributes without any need for scaling, its inclination to perform implicit feature selection, and its parallel algorithmic structure which allows for relatively fast training times<d-cite key="Deeb2015"></d-cite>.

Similar to bagging, this method involves the construction of $b$ decision trees produced by CART and combines them together to produce a more accurate, stable prediction. In addition to the randomness induced by employing bootstrap sampling, random forests provide further randomness by imposing the following change to the standard CART model: Starting at the root node, instead of considering each of the $m$ attributes as possible candidates according to which to split the node, only a random subset of $m^{\prime}<m$ (typically chosen as $m^{\prime}\approx\sqrt{m}$) attributes are considered. This process of only considering a random attribute subset to split a node is continued until the tree reaches the largest possible size. The process is repeated a total of $b$ times, and the final prediction is typically a majority vote (in the case of classification) or an average (in the case of regression) of all the predictions made by the $b$ trees constructed<d-cite key="Moisen2008"></d-cite>.

The reason why this approach works so well can be described as follows: By only allowing the algorithm to consider a subset of the available attributes at a node split, the algorithm is charged to explain the target variables by not only utilising the "best" attributes present in the data. In other words, if there is an attribute which is an inherently very strong predictor of the target variables, this attribute will not be available to the algorithm $100(m-m^{\prime})/m\%$ of the time (on average), thereby forcing the algorithm to explore the use of other attributes which are moderately strong predictors [11]. In the case of bagging, the collection of trees are, to a large extent, similar to one another since they all typically rely on the same strong predictor when splitting the root node. Hence, the members of this collection of trees are highly correlated and an average of highly correlated results typically does not produce a large reduction in variance. For this reason, the method of random forests is typically described as a technique for *decorrelating* the trees produced by CART<d-cite key="James2013"></d-cite>.

***

## 4. The C4.5 algorithm

The C4.5 algorithm was developed by Quinlan<d-cite key="Quinlan1993"></d-cite> and is the successor of his earlier ID3 algorithm<d-cite key="Quinlan1986"></d-cite>. Similar to CART, this algorithm begins with the training set defined at a single root node of a tree which is subsequently partitioned into child nodes based on a specific splitting criterion. The algorithm is, however, only applicable in the case of classification tasks. In the case of the C4.5 algorithm, the *information gain ratio* is used as the default spitting criterion. Just like in CART, if a node is split based on a quantitative attribute, a threshold may be determined as the point at which the training data set is partitioned into two subsequent child nodes. In the case of a qualitative attribute, however, the C4.5 algorithm creates as many child nodes as there are associated categories. For this reason, the C4.5 algorithm is described as a *multi-way split* (or *non-binary split*) tree growing algorithm<d-cite key="Kim2001"></d-cite>. The tree growing procedure of the C4.5 algorithm also follows a greedy approach by attempting to find the best local node split which maximises the information gain ratio without employing any form of backtracking<d-cite key="Galathiya2012, Ruggieri2002"></d-cite>.

The split criterion employed by the C4.5 algorithm is based on Shannon's well-known concept of entropy from information theory<d-cite key="Shannon1949"></d-cite>. Entropy is a measure of the disorder in a data subset. Denote the set of $C_j$ categories for attribute $j$ by $\mathcal{C}_j=\{c_1^{(j)},\ldots,c_{C_j}^{(j)}\}$, and the frequency of observations in a particular subset of data $\mathcal{S}\subset\mathcal{X}$ with a target variable of $q$ by freq$(q,\mathcal{S})$. Then the entropy of a category $c_k^{(j)}$ of attribute $j$ is given by

\begin{equation}
\mbox{Entropy}(\mathcal{S})=-\sum_{q=1}^{Q}\frac{\mbox{freq}(q,\mathcal{S})}{|\mathcal{S}|}\log_b\left(\frac{\mbox{freq}(q,\mathcal{S})}{|\mathcal{S}|}\right),\label{4.eqn.entropy}
\end{equation}

where $Q$ is the total number of target attribute categories. If a large proportion of observations in a data subset $\mathcal{S}$ have the value of $q$ (*i.e.* $\mbox{freq}(q,\mathcal{S})/|\mathcal{S}|\approx 1$), then the entropy in (\ref{4.eqn.entropy}) will be approximately zero (implying a high level of order). Conversely, if there is an equal representation of all attribute categories in the data subset $\mathcal{S}$ (*i.e.* $\mbox{freq}(q,\mathcal{S})/|\mathcal{S}|\approx 1/C_j$), then the entropy in (\ref{4.eqn.entropy}) will be close to one (implying a high level of disorder).

With this notion in mind, consider the case in which a data subset $\mathcal{S}$, residing in a parent node $t_p$, is partitioned based on the categories of attribute $j$ producing $C_j$ child nodes $t_1,\ldots,t_{C_j}$. The *information gain* achieved by this node split can be defined as the total reduction of entropy in the data, expressed mathematically as

\begin{equation}
\mbox{Gain}(t_p,j)=\mbox{Entropy}(t_P)-\sum_{i=1}^{C_j}\frac{|t_i|}{|t_p|}\mbox{Entropy}(t_i).\label{4.eqn.gain}
\end{equation}

This criterion was first proposed for splitting nodes in the ID3 algorithm<d-cite key="Quinlan1986"></d-cite>. A notable drawback associated with this splitting criterion is that it is biased towards attributes with many categories \cite{Thakur2010}. This led Quinlan<d-cite key="Quinlan1993"></d-cite> subsequently to build on this notion in order to create the information gain ratio, which is a more robust criterion than information gain and generally produces better classification results. This formulation utilises (\ref{4.eqn.entropy}) as a basis to define a so-called *split entropy* in terms of the categories of an attribute $j$ (as opposed to the target attribute categories), expressed mathematically as

\begin{equation}
\mbox{SplitEntropy}(t_p,j)=-\sum_{i=1}^{C_j}\frac{|t_i|}{|t_p|}\log_b\left(\frac{|t_i|}{|t_p|}\right).\label{4.eqn.splitentropy}
\end{equation}

If a large proportion of observations in a data subset $\mathcal{S}$ exhibit the value $c_k^{(j)}$ for attribute $j$ ({\em i.e.}\ $|t_i|/|t_p|\approx 1$), then the entropy in (\ref{4.eqn.entropy}) will be approximately zero, while, conversely, if there is an equal representation of all attribute categories in the data subset $\mathcal{S}$ ({\em i.e.}\ $|t_i|/|t_p|\approx 1/C_j$), then the entropy in (\ref{4.eqn.entropy}) will be close to one. Combining (\ref{4.eqn.gain}) and (\ref{4.eqn.splitentropy}), it is possible to define the information gain ratio as the proportion of information generated by partitioning a parent node $t_p$ according to attribute $j$, expressed mathematically as

\begin{equation}
\mbox{GainRatio}(t_p,j)=\frac{\mbox{Gain}(t_p,j)}{\mbox{SplitEntropy}(t_p,j)}.\label{4.eqn.gainratio}
\end{equation}

Unlike its predecessor, this formulation is more robust since it effectively penalises the choice of splitting attributes with many categories. A drawback of this approach, however, is that (\ref{4.eqn.gainratio}) tends to favour unbalanced splits in which one node partition is much larger than the rest<d-cite key="Harris2002"></d-cite>.

***

## 5. Gradient boosted decision trees


