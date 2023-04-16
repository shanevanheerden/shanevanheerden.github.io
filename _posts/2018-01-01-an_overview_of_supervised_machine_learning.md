---
layout: distill
title: An Overview of Supervised Machine Learning
description: Classic Machine Learning Algorithms Series
date: 2018-01-01
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
  - name: 2. Machine learning paradigms
  - name: 3. The supervised learning procedure
  - name: 4. Data taxonomy
  - name: 5. Data cleaning
  - name: 6. Data partitioning methods
  - name: 7. Performance measures

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

*This blog post was adapted from Sections 4.1, 4.2 and 4.3 in my [PhD dissertation](https://sunore.co.za/wp-content/uploads/2021/03/vanheerden_phd_2020.pdf).*

{% include figure.html path="assets/img/blog/blog7.1.png" class="img-fluid rounded z-depth-1" zoomable=true %}

This is the first post in a series of blog posts focusing on the some of the classic Machine Learning algorithms! In this post, we will give a brief overview of the exciting field of Machine Learning. Let's get started!

## 1. Introduction

The term "machine learning" was first coined by Arthur Samuel in 1954 upon the publication of his self-learning program which could successfully play the game of Checkers \cite{Samuel1959}. Although not literally stated in his paper, the definition of ML as "*the field of study that gives computers the ability to learn without being explicitly programmed*," is often attributed to Samuel. Formally stated, ML involves constructing computer-based algorithms that are able to learn generalised rules from data to make predictions or determinations about a real-world phenomenon without relying on rule-based programming \cite{Copeland2016, Pyle2015}. Nowadays, ML algorithms (or learning models) are employed in a wide variety of useful applications such as recommender systems, fraud detection, weather forecasting, medical diagnostics, robot navigation, and image classification \cite{Marr2016}, to name but a few.

## Machine learning paradigms

The majority of ML algorithms reside, for the most part, in one of four broad learning paradigms which may be described as follows:
- **Supervised learning.** In a *supervised learning* paradigm, an algorithm is presented with a set of *labelled* data comprising input examples together with *known* corresponding output values deemed of particular interest. The task of the algorithm is to approximate the underlying function which maps these input examples to their corresponding output values \cite{Mohri2012}. In the case where output values are categorical, the problem is called a *classification* problem while in the case of continuous output values, it is called a *regression* problem.
- **Unsupervised learning.** In an *unsupervised learning* paradigm, an algorithm is presented with a set of *unlabelled* data for which there are *no* known corresponding output values. The task of the algorithm is to identify some underlying structure (or pattern) between the various input values \cite{Marsland2009}. This is typically achieved through *dimensionality reduction* techniques (which aim to project the data onto a lower dimensional space), *density estimation* methods (which attempt to construct a functional estimate of the observed data), and/or though the application of various *clustering* approaches (whereby similar input examples are grouped together). This paradigm is the primary task of *exploratory data analysis* (EDA) which attempts to first make sense of the acquired data so that meaningful questions can be framed and posed \cite{Blitz2018}.
- **Semi-supervised learning.** In a *semi-supervised learning* paradigm, an algorithm is typically presented with both labelled an unlabelled data. In this paradigm, the algorithm utilises both supervised and unsupervised learning techniques to accomplish *inductive* or *transductive* learning tasks. In the case of the former, unlabelled data are used in conjunction with a small amount to labelled data to improve the accuracy of a supervised learning task \cite{DeepAI2017}. In the case of the latter, the aim is to infer the correct labels for each of the unlabelled examples as a way of avoiding the costly and time-consuming task of manual labelling.
- **Reinforcement learning.** Lastly, in a *reinforcement learning* paradigm, an algorithm, commonly referred to as an *agent*, is exposed to a dynamic *environment* which is typically some abstraction of the real world. The task of the agent is to determine, through a trial-and-error process, the correct set of *actions* (collectively called a *policy*) which maximises (or minimises) some long-term cumulative *reward* (or punishment) \cite{Marsland2009}. Reinforcement learning differs from supervised learning in that, instead of correcting sub-optimal actions, the focus is on striking a balance between the *exploration* of uncharted territory and the *exploitation* of current knowledge \cite{Kaelbling1996}.

{% include figure.html path="assets/img/blog/blog7.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}

## The supervised learning procedure

The primary goal of any supervised learning process is to choose an appropriate ML algorithm that can accurately model an underlying relationship between the variables in a data set. The standard process by which this is achieved is illustrated graphically in Figure~\ref{4.fig.supervised}, where the process begins by developing a well-formulated research question. To answer this research question by means of supervised learning, a researcher must first transform one or more sets of raw data (deemed relevant to the current investigation) by means of a host of data preprocessing techniques into a set of prepared data. The process of ensuring that the data are appropriately prepared for the application of an ML algorithm is often iterative in nature, usually requiring the data to be reshaped in such a way that one may draw the best scientific conclusions from them. Once a set of appropriately prepared data has been established, the researcher may apply a selection of supervised ML algorithms to the data in an attempt at predicting a specific feature that is able to provide insight into the original research question. Again, this is an iterative process, whereby the performance of each candidate model is trained and evaluated in respect of the prepared data until an appropriate model has been found. Thereafter, the researcher may draw insight from this model by predicting the outcome of new scenarios presented to the model.

{% include figure.html path="assets/img/blog/blog7.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}

As a way of establishing a set of formal notation and naming conventions for use in the remainder of this chapter, the "input" variables, hereafter referred to as *explanatory variables*, are denoted by the set $\mathcal{X}=\{\bm{x}^{(1)},\ldots,\bm{x}^{(m)}\}$ of $m$ vectors, where the $i$\textsuperscript{th} vector of explanatory variables $\bm{x}^{(i)}=\langle x_1^{(i)}\cdots x_n^{(i)}\rangle$ has values associated with $n$ *explanatory attributes*\footnote{The term *attribute* is used (as opposed to the more common ML term *feature*), in this case, to highlight the analogous meaning of the term to that previously presented in the GIS literature review of \S\ref{3.sub.concept}.} of interest. All explanatory variables correspond to a set of "output" variables, hereafter referred to as the *target variables*, which are denoted by $\mathcal{Y}=\{y^{(1)},\ldots,y^{(m)}\}$, where only one *target attribute* defines this set. A pair of the form $(\bm{x}^{(i)}, y^{(i)})$, therefore, denotes the $i$<sup>th</sup> *observation* recorded for a specific event, where a vector $\bm{x}^{(i)}$ of explanatory variables is mapped to a single target variable $y^{(i)}$. A data set $\{(\bm{x}^{(1)}, y^{(1)}),\ldots,(\bm{x}^{(m)}, y^{(m)})\}$ of $m$ independent observations of a specific event is called a *sample set*. Given such a sample set, the goal of a supervised learning algorithm, hereafter referred to as a *learning model*, is to learn a function $h:\mathcal{X}\mapsto\mathcal{Y}$ such that, for an unseen vector $\bm{x}$, $h(\bm{x})=y$ is a "good" predictor of the actual target variable $y$. The function $h(\bm{x})$ is called the *hypothesis* of the learning model and may predict either a categorical or a continuous target variable. Furthermore, if the fundamental working of a learning model is based on a specific assumption of the distribution of observations, it is called a *parametric* model, while learning models that do not make any explicit assumptions about the distribution of observations are termed *non-parametric* models.

Since the primary input to all ML algorithms is data, one must be aware of the many consideration associated with the appropriate understanding, management and manipulation of data in the context of ML. More specifically, one should be able to: (1) Identify the many formats in which attributes in a data set may be expressed, (2) understand the origin of erroneous and missing values in a data set and how they should be corrected appropriately, and (3) be aware of the various data partitioning methods used during the performance evaluation of a learning model.

## Data taxonomy

According to Baltzan and Phillips \cite{Baltzan2012}, *information* may be seen as *raw data* that have been transformed, through various processes and procedures, into meaningful and understandable content which can ultimately be used to support decision making. Raw data, on the other hand, may be described as data that have not undergone any form of preprocessing \cite{Rouse2012}, and are typically classified according to the inherent structure by which the data were captured, called the *data format*.

The format of data can, in its most primordial form, be classified into two broad categories: Data can either be inherently *structured* or *unstructured*. Structured data, for the most part, exhibit a high degree of organisation so that they can be interpreted easily by a computer, while unstructured data do not follow any conventional data structure \cite{BrightPlanet2012}. Jiang *et al.* \cite{Jiang1999} presented a more descriptive classification which groups data formats into to four broad categories, namely: (1) *textual data*, (2) *temporal data*, (3) *transactional data* and (4) *relational data*. The first of these, textual data, are, in essence, a large collection of characters that convey meaning by forming words which, in turn, form sentences. These may, for example, manifest themselves in the form of documents, texts or social media posts --- all of which are highly unstructured. Temporal data, on the other hand, are commonly structured and represent information that changes over time (typically referred to as *time-series* data). Stock market graphs and the daily air temperature are examples of inherently temporal data. Transactional data describe one or more objects related to a specific event and, therefore, also contain a time dimension. An example of data in this format is a list of purchased products captured in historical retail store transactions. Finally, and probably the most widely-used data format, is that of a relational data. In this format, data are typically organised in the form of a *tabular* data structure, where each row denotes a unique *record* and each column represents an *attribute* which conforms to a specific *data type*.

The data type of an attribute describes the inherent values a data item can assume and may, in its most basic form, be grouped into one of two categories, namely as a *qualitative* (categorical) attribute or a *quantitative* attribute. The latter describes an attribute which is inherently captured on a numerical scale, such as the velocity or mass of a vehicle \cite{Curry2009, James2013}. Although all quantitative attributes are numeric, all numeric attributes are not necessarily quantitative. A person's identification number may, for example, be numeric, but not necessarily quantitative. Qualitative attributes, on the other hand, classify all data items into a set of distinct categories which describe the inherent characteristics of the object under consideration. An optometrist record may, for example, describe a client's eye colour as being either blue, green or brown. Both qualitative and quantitative attributes may be further classified according to the hierarchical categories shown in Figure~\ref{4.fig.datatype}.

{% include figure.html path="assets/img/blog/blog7.4.png" class="img-fluid rounded z-depth-1" zoomable=true %}

Qualitative attributes may further be categorised into attributes that are either *nominal* or *ordinal* in nature \cite{Hastie2009}, where the former contains two or more categories that are all assumed to be of equal importance (*i.e.* there exists no meaningful sequence in which to order the categories of an attribute). One cannot, for example, say that a brown eye colour is more important than a blue eye colour. Nominal attributes can be further decomposed into attributes that are binary in nature, referred to as *dichotomous*, or contain more than two categories, in which case they are referred to as *multichotomous*. In contrast to nominal attributes, ordinal attributes contain categories that *are* intrinsically ranked \cite{Bramer2007}. In a survey, for example, one may be required to rate a customer experience as either: "Bad," "Satisfactory" or "Good." It should be noted, however, that, although ordinal attributes express an inherent ordering of the associated categories, the differences between attribute categories is not necessarily equal \cite{Hastie2009}.

Quantitative attributes may further be categorised into attributes that are either *discrete* or *continuous* in nature. Discrete attributes can assume a single value from a finite set of values, such as the number rolled on a six-face die, for example. All qualitative attributes are inherently discrete since they are countable and can assume only a certain set of values. Continuous attributes, on the other hand, can assume one of an uncountably infinite set of values (which exist along a continuum), such as the temperature in a room, for example. Continuous attributes can further be categorised into attributes which assume values on an *interval* scale or a *ratio* scale. The latter assumes that there exists a so-called *true zero* which reflects the absence of the specific measured feature. An object's weight is, for example, a ratio attribute since a value of zero indicates that the object has no weight. Interval attributes, on the other hand, do not assume this property. Temperature measured in degrees Fahrenheit ($^{\circ}F$) or Celsius ($^{\circ}C$) is, for example, an interval attribute since zero $^{\circ}F$ or $^{\circ}C$ does not imply no temperature. If, however, temperature is measured in Kelvin, then it may be expressed as a ratio estimate, since zero Kelvin indicates the absence of temperature.

## Data cleaning

Probably the most important and, at the same time, mundane tasks associated with the data preparation process is that of *data cleaning* \cite{Press2016}. The purpose of data cleaning is to remove any errors in and/or missing values from a data set so as to ensure that the data being used to formulate a learning model's hypothesis is of a high quality, thereby instilling confidence in the results obtained. This typically involves three types of activities, namely: (1) correcting erroneous values, (2) *imputing* (filling in) missing data entries and (3) detecting outliers \cite{Nisbet2009}. Examples of these occurrences are illustrated in Table~\ref{7.tbl.errors}, where the first column denotes the data attribute the recorder attempted to capture, the second column describes the true data that should have been entered by the data recorder, and the last column contains the actual data recorded for an observation. As may be seen, the values for the attributes `Annual income` and `Ethnicity` have been left blank, the attributes `Name`, `Date of birth` and `Place of birth` are incomplete, and the attribute `Sex`, although in the correct format, does not reflect the true value.

\begin{table}[!htb]
	\centering
	\caption[Examples of erroneous and missing data values]{Examples of erroneous and missing data values \cite{Davis2010}.}\label{7.tbl.errors}
	\rowcolors{2}{gray!25}{white}
	\begin{tabular}{|l|l|l|}
		\hline
		\rowcolor{gray!75}
		\multicolumn{1}{|c|}{\textbf{Attribute}}      & \multicolumn{1}{c|}{\textbf{True data}}              & \multicolumn{1}{c|}{\textbf{Actual data}} \\ \hline
		Name           & Maria Margaret Smith    & Maria Smith                      \\ \hline
		Date of birth  & 2/19/1981               & 1981                             \\ \hline
		Sex            & F                       & M                                \\ \hline
		Ethnicity      & Hispanic and Caucasian  &                                  \\ \hline
		Place of Birth & Nogales, Somora, Mexico & Nogales                          \\ \hline
		Annual income  & \$50,000                &                                  \\ \hline
	\end{tabular}
\end{table}

According to Hellerstein \cite{Hellerstein2008}, there exists four main origins of erroneous values in a data set. The first of these is described as *data entry errors* which are primarily attributed to the typographic and misinterpretation errors humans make when attempting to capture the data. The second origin is described as *measurement errors* which occur when measuring a physical property is not conducted according to a proper procedure. The third origin is described as *distillation errors* which occur during the preprocessing and summarising phase before subsequently being added to a system database. The final origin is described as *data integration errors* which occur when multiple data sets are integrated into a single database.

Since the majority of learning models require a complete data set in order to function properly, it is necessary to address any missing data in a data set. Obviously, the best approach would be to manually fill in all incomplete entries to contain the correct information. This is, however, not always practical, especially if there is a significant proportion of missing values in a considerably large data set. The second approach, which may seem more reasonable, would be to delete any observations with missing values entirely, leaving only the observations that contain values for all of the defined attributes. This may, however, result in a substantial loss in the overall set of observations, to the point where there are too few data to conduct a meaningful investigation. Another approach is to identify those attributes in the data set which possess a large proportion of missing values. If the proportion of missing values of an attribute exceed a specific threshold, then the attribute may be disregarded altogether. The final approach would be to employ a specific imputing technique where missing values are filled according to some specified rule. Many imputing methods have been proposed in the literature, namely: *Mean imputation* (replace missing values with the average/most frequent value), *hot deck imputation* (replace missing values with randomly chosen observations of similar values), *cold deck imputation* (replace missing values with systematically chosen observations of similar values), *random imputation* (replace missing values with random values) and *model-based imputation* (construct a learning model to predict missing values based on values of complete observations) \cite{Davis2010, GraceMartin2008}.

The final activity in the data cleaning process is concerned with the detection of any outliers that may be present in a data set, where an outlier is described as an observation that does not conform to the general pattern observed in a data set \cite{Moore2009}. Aguinis *et al.* \cite{Aguinis2013} described a set of best-practice recommendations concerned with *defining*, *identifying*, and *handling* outliers. There exists a wide variety of statistical techniques for the purposes of outlier definition and identification, which are typically described as *univariate* (identifying extreme values of a single attribute), *multivariate* (identifying unusual value combinations for two or more attributes), *parametric* (based on underlying distribution assumptions) and *non-parametric* (not based on any distribution assumptions) methods \cite{Williams2002}. The appropriate handling of outliers is, however, problem-specific \cite{Nisbet2009}. If the aim of a study is to identify a general pattern in the data, then outliers are typically removed. If, on the other hand, the goal of a study is to detect unusual observations (*e.g.* a fraudulent bank transactions), then outliers provide essential information.

## Data partitioning methods

The initial sample set is typically partitioned into three separate data sets, namely a *training set*, a *validation set* and a *testing set* \cite{Brownlee2017}, as shown in Figure~\ref{4.fig.sampledata}. The training set is used to find optimal parameter settings (or weights) for a learning model so that it fits the set of training examples. The validation set (sometimes called the *hold-out set*) is used to provide an unbiased evaluation of a learning model's fit in respect of the training set while also tuning the hyper-parameters of the model. For this reason, the combined training and validation sets are often referred to as the *learning set*. The evaluation of a model, however, becomes more biased as its predictive ability in respect of the validation set is incorporated into the learning model's configuration \cite{Shah2017}. It is, therefore, necessary to evaluate a learning model's performance in respect of an independent test set so as to provide a completely unbiased evaluation of a final model fit in respect of the training set. In order to enhance the performance of learning models, various *data re-sampling* methods may be employed to construct the training and validation sets \cite{James2013}, two of the most popular being the method of *cross-validation* and the *bootstrap re-sampling* method.

{% include figure.html path="assets/img/blog/blog7.5.png" class="img-fluid rounded z-depth-1" zoomable=true %}

### Cross-validation

There are two main types of cross-validation methods, namely: $k$-*fold* cross-validation and *leave-one-out* cross-validation. In the case of $k$-fold cross-validation, the set of $m$ observations is partitioned into $k$ equal (or as close to equal as possible) parts (or folds), as illustrated graphically in Figure~\ref{4.fig.kfold}. A total of $k-1$ folds of the data are then used for training a learning model, while the remaining fold is used for model validation. This process is repeated a total of $k$ times, each time using a different fold for validation purposes. The final performance of a learning model is then estimated as the average of the misclassification errors seen during the $k$ repetitions.

The leave-one-out method of cross-validation is a special case of the $k$-fold cross-validation that occurs when $k=m$ (*i.e.* $k$ is equal to the number of observations). As its name suggests, all observations, except one (*i.e.* $m-1$ observations), are used to train the learning model according to this method, and the remaining observation is used for model validation. This procedure is thus repeated a total of $m$ times, where the final performance of a learning model is estimated as the average of the $m$ misclassification errors.

### Bootstrap re-sampling

The second type of data sampling method is that of *bootstrap re-sampling* (or just *bootstrapping*), where the aim is to create a *bootstrap sample* of observations for training and validating learning models --- the method by which it achieves these subsets is, however, quite different. Bootstrapping involves the repeated selection of observations from the learning set *with* replacement \cite{Tonidandel2009} to construct a new set of any cardinality, denoted by $L$ (since observations can be reused). Given a specified validation-training proportion split $p$, validation and training sets of cardinalities $pL$ and $(1-p)L$, respectively, may be defined a total of $b$ times, as illustrated in Figure~\ref{4.fig.bootstrapping}. The final performance of a learning model is then estimated as the average of the misclassification errors achieved over the $b$ validating sets. This method, therefore, stands in contrast to $k$-fold cross-validation which is limited by the size of the learning set and the number of folds.

{% include figure.html path="assets/img/blog/blog7.6.png" class="img-fluid rounded z-depth-1" zoomable=true %}

## Performance measures

As previously described in \S\ref{4.sub.supervised}, the primary goal of any supervised learning procedure is choosing an appropriate model. One must, therefore, be aware of various notions that may assist in selecting, configuring and evaluation a model for a specific learning task. More specifically, one should be able to: (1) Evaluate the performance of a learning model according to an appropriate performance measure, (2) identify whether a learning algorithm is generalising the data, and (3) appreciate the trade-off associated with employing a complex learning model rather than one that is easier to understand. These topics are discussed in this section.

Since the evaluation of a learning model is essential in any ML investigation, one must be able to choose a performance measure that is most appropriate when presented with either a classification or regression task. In the case of the latter, the most popular performance measures are the well-known *mean square error*, *mean absolute error* and *R-squared error* \cite{Scikit2013}. In the case of a classification task, however, the performance evaluation of a learning model is more involved. The performance of a classifier is typically first described in the form of a confusion matrix, as illustrated in Table~\ref{4.tbl.confusion} (for a binary classification task), where each row of the matrix represents the *actual* category of the observations while each column represents the observation category *predicted* by the learning model.

\begin{table}[!htb]
	\centering
	\caption[A confusion matrix]{A confusion matrix showing the possible outcomes of a learning model's predictions.}\label{4.tbl.confusion}
	\begin{tabular}{cc|c|c|}
		\hhline{~~--}
		& & \multicolumn{2}{c|}{\cellcolor{gray!75}Predicted category} \\ \hhline{~~--}
		& & \cellcolor{gray!50}Positive & \cellcolor{gray!50}Negative \\ \hline
		\multicolumn{1}{|c|}{\cellcolor{gray!75}} & \cellcolor{gray!50}Positive & True positive (TP) & \cellcolor{gray!25}False negative (FN) \\ \hhline{|*1{>{\arrayrulecolor{gray!75}}-}>{\arrayrulecolor{black}}|*3{-}|}
		\multicolumn{1}{|c|}{\multirow{-2}{*}{\cellcolor{gray!75}Actual category}} & \cellcolor{gray!50}Negative & \cellcolor{gray!25}False positive (FP) & True negative (TN) \\ \hline
	\end{tabular}
\end{table}

Perhaps the best-known performance measure is *classification accuracy* (CA), which describes the total proportion of correct predictions made by a learning model, expressed mathematically as
\begin{equation}
\mbox{CA}=\frac{\mbox{TP}+\mbox{TN}}{\mbox{TP}+\mbox{FP}+\mbox{FN}+\mbox{TN}}.
\end{equation}
Although CA seems to be an intuitive measure by which to assess a classifier, it should never be used when there is a large category imbalance in a data set. Consider, for example, a case in which $950$ positive observations and $50$ negative observations are contained in a data set. Then a learning model can easily achieve a CA of $95\%$ simply by always predicting the majority category ({\em i.e.}\ the positive category).









