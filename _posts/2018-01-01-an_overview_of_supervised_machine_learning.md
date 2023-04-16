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
  - name: 8. Overfitting vs underfitting the training data
  - name: 9. Model performance vs model interpretability

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

The term "machine learning" was first coined by Arthur Samuel in 1954 upon the publication of his self-learning program which could successfully play the game of Checkers<d-cite key="Samuel1959"></d-cite>. Although not literally stated in his paper, the definition of ML as "*the field of study that gives computers the ability to learn without being explicitly programmed*," is often attributed to Samuel. Formally stated, ML involves constructing computer-based algorithms that are able to learn generalised rules from data to make predictions or determinations about a real-world phenomenon without relying on rule-based programming<d-cite key="Copeland2016, Pyle2015"></d-cite>. Nowadays, ML algorithms (or learning models) are employed in a wide variety of useful applications such as recommender systems, fraud detection, weather forecasting, medical diagnostics, robot navigation, and image classificatio<d-cite key="Marr2016"></d-cite>, to name but a few.

## Machine learning paradigms

The majority of ML algorithms reside, for the most part, in one of four broad learning paradigms which may be described as follows:
- **Supervised learning.** In a *supervised learning* paradigm, an algorithm is presented with a set of *labelled* data comprising input examples together with *known* corresponding output values deemed of particular interest. The task of the algorithm is to approximate the underlying function which maps these input examples to their corresponding output values<d-cite key="Mohri2012"></d-cite>. In the case where output values are categorical, the problem is called a *classification* problem while in the case of continuous output values, it is called a *regression* problem.
- **Unsupervised learning.** In an *unsupervised learning* paradigm, an algorithm is presented with a set of *unlabelled* data for which there are *no* known corresponding output values. The task of the algorithm is to identify some underlying structure (or pattern) between the various input values<d-cite key="Marsland2009"></d-cite>. This is typically achieved through *dimensionality reduction* techniques (which aim to project the data onto a lower dimensional space), *density estimation* methods (which attempt to construct a functional estimate of the observed data), and/or though the application of various *clustering* approaches (whereby similar input examples are grouped together). This paradigm is the primary task of *exploratory data analysis* (EDA) which attempts to first make sense of the acquired data so that meaningful questions can be framed and posed<d-cite key="Blitz2018"></d-cite>.
- **Semi-supervised learning.** In a *semi-supervised learning* paradigm, an algorithm is typically presented with both labelled an unlabelled data. In this paradigm, the algorithm utilises both supervised and unsupervised learning techniques to accomplish *inductive* or *transductive* learning tasks. In the case of the former, unlabelled data are used in conjunction with a small amount to labelled data to improve the accuracy of a supervised learning task<d-cite key="DeepAI2017"></d-cite>. In the case of the latter, the aim is to infer the correct labels for each of the unlabelled examples as a way of avoiding the costly and time-consuming task of manual labelling.
- **Reinforcement learning.** Lastly, in a *reinforcement learning* paradigm, an algorithm, commonly referred to as an *agent*, is exposed to a dynamic *environment* which is typically some abstraction of the real world. The task of the agent is to determine, through a trial-and-error process, the correct set of *actions* (collectively called a *policy*) which maximises (or minimises) some long-term cumulative *reward* (or punishment)<d-cite key="Marsland2009"></d-cite>. Reinforcement learning differs from supervised learning in that, instead of correcting sub-optimal actions, the focus is on striking a balance between the *exploration* of uncharted territory and the *exploitation* of current knowledge<d-cite key="Kaelbling1996"></d-cite>.

{% include figure.html path="assets/img/blog/blog7.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}

## The supervised learning procedure

The primary goal of any supervised learning process is to choose an appropriate ML algorithm that can accurately model an underlying relationship between the variables in a data set. The standard process by which this is achieved is illustrated graphically in Figure~\ref{4.fig.supervised}, where the process begins by developing a well-formulated research question. To answer this research question by means of supervised learning, a researcher must first transform one or more sets of raw data (deemed relevant to the current investigation) by means of a host of data preprocessing techniques into a set of prepared data. The process of ensuring that the data are appropriately prepared for the application of an ML algorithm is often iterative in nature, usually requiring the data to be reshaped in such a way that one may draw the best scientific conclusions from them. Once a set of appropriately prepared data has been established, the researcher may apply a selection of supervised ML algorithms to the data in an attempt at predicting a specific feature that is able to provide insight into the original research question. Again, this is an iterative process, whereby the performance of each candidate model is trained and evaluated in respect of the prepared data until an appropriate model has been found. Thereafter, the researcher may draw insight from this model by predicting the outcome of new scenarios presented to the model.

{% include figure.html path="assets/img/blog/blog7.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}

As a way of establishing a set of formal notation and naming conventions for use in the remainder of this chapter, the "input" variables, hereafter referred to as *explanatory variables*, are denoted by the set $\mathcal{X}=\{\mathbf{x}^{(1)},\ldots,\mathbf{x}^{(m)}\}$ of $m$ vectors, where the $i$\textsuperscript{th} vector of explanatory variables $\mathbf{x}^{(i)}=\langle x_1^{(i)}\cdots x_n^{(i)}\rangle$ has values associated with $n$ *explanatory attributes*\footnote{The term *attribute* is used (as opposed to the more common ML term *feature*), in this case, to highlight the analogous meaning of the term to that previously presented in the GIS literature review of \S\ref{3.sub.concept}.} of interest. All explanatory variables correspond to a set of "output" variables, hereafter referred to as the *target variables*, which are denoted by $\mathcal{Y}=\{y^{(1)},\ldots,y^{(m)}\}$, where only one *target attribute* defines this set. A pair of the form $(\mathbf{x}^{(i)}, y^{(i)})$, therefore, denotes the $i$<sup>th</sup> *observation* recorded for a specific event, where a vector $\mathbf{x}^{(i)}$ of explanatory variables is mapped to a single target variable $y^{(i)}$. A data set $\{(\mathbf{x}^{(1)}, y^{(1)}),\ldots,(\mathbf{x}^{(m)}, y^{(m)})\}$ of $m$ independent observations of a specific event is called a *sample set*. Given such a sample set, the goal of a supervised learning algorithm, hereafter referred to as a *learning model*, is to learn a function $h:\mathcal{X}\mapsto\mathcal{Y}$ such that, for an unseen vector $\mathbf{x}$, $h(\mathbf{x})=y$ is a "good" predictor of the actual target variable $y$. The function $h(\mathbf{x})$ is called the *hypothesis* of the learning model and may predict either a categorical or a continuous target variable. Furthermore, if the fundamental working of a learning model is based on a specific assumption of the distribution of observations, it is called a *parametric* model, while learning models that do not make any explicit assumptions about the distribution of observations are termed *non-parametric* models.

Since the primary input to all ML algorithms is data, one must be aware of the many consideration associated with the appropriate understanding, management and manipulation of data in the context of ML. More specifically, one should be able to: (1) Identify the many formats in which attributes in a data set may be expressed, (2) understand the origin of erroneous and missing values in a data set and how they should be corrected appropriately, and (3) be aware of the various data partitioning methods used during the performance evaluation of a learning model.

## Data taxonomy

According to Baltzan and Phillips<d-cite key="Baltzan2012"></d-cite>, *information* may be seen as *raw data* that have been transformed, through various processes and procedures, into meaningful and understandable content which can ultimately be used to support decision making. Raw data, on the other hand, may be described as data that have not undergone any form of preprocessing<d-cite key="Rouse2012"></d-cite>, and are typically classified according to the inherent structure by which the data were captured, called the *data format*.

The format of data can, in its most primordial form, be classified into two broad categories: Data can either be inherently *structured* or *unstructured*. Structured data, for the most part, exhibit a high degree of organisation so that they can be interpreted easily by a computer, while unstructured data do not follow any conventional data structure<d-cite key="BrightPlanet2012"></d-cite>. Jiang *et al.*<d-cite key="Jiang1999"></d-cite> presented a more descriptive classification which groups data formats into to four broad categories, namely: (1) *textual data*, (2) *temporal data*, (3) *transactional data* and (4) *relational data*. The first of these, textual data, are, in essence, a large collection of characters that convey meaning by forming words which, in turn, form sentences. These may, for example, manifest themselves in the form of documents, texts or social media posts --- all of which are highly unstructured. Temporal data, on the other hand, are commonly structured and represent information that changes over time (typically referred to as *time-series* data). Stock market graphs and the daily air temperature are examples of inherently temporal data. Transactional data describe one or more objects related to a specific event and, therefore, also contain a time dimension. An example of data in this format is a list of purchased products captured in historical retail store transactions. Finally, and probably the most widely-used data format, is that of a relational data. In this format, data are typically organised in the form of a *tabular* data structure, where each row denotes a unique *record* and each column represents an *attribute* which conforms to a specific *data type*.

The data type of an attribute describes the inherent values a data item can assume and may, in its most basic form, be grouped into one of two categories, namely as a *qualitative* (categorical) attribute or a *quantitative* attribute. The latter describes an attribute which is inherently captured on a numerical scale, such as the velocity or mass of a vehicle<d-cite key="Curry2009, James2013"></d-cite>. Although all quantitative attributes are numeric, all numeric attributes are not necessarily quantitative. A person's identification number may, for example, be numeric, but not necessarily quantitative. Qualitative attributes, on the other hand, classify all data items into a set of distinct categories which describe the inherent characteristics of the object under consideration. An optometrist record may, for example, describe a client's eye colour as being either blue, green or brown. Both qualitative and quantitative attributes may be further classified according to the hierarchical categories shown in Figure~\ref{4.fig.datatype}.

{% include figure.html path="assets/img/blog/blog7.4.png" class="img-fluid rounded z-depth-1" zoomable=true %}

Qualitative attributes may further be categorised into attributes that are either *nominal* or *ordinal* in nature<d-cite key="Hastie2009"></d-cite>, where the former contains two or more categories that are all assumed to be of equal importance (*i.e.* there exists no meaningful sequence in which to order the categories of an attribute). One cannot, for example, say that a brown eye colour is more important than a blue eye colour. Nominal attributes can be further decomposed into attributes that are binary in nature, referred to as *dichotomous*, or contain more than two categories, in which case they are referred to as *multichotomous*. In contrast to nominal attributes, ordinal attributes contain categories that *are* intrinsically ranked<d-cite key="Bramer2007"></d-cite>. In a survey, for example, one may be required to rate a customer experience as either: "Bad," "Satisfactory" or "Good." It should be noted, however, that, although ordinal attributes express an inherent ordering of the associated categories, the differences between attribute categories is not necessarily equal<d-cite key="Hastie2009"></d-cite>.

Quantitative attributes may further be categorised into attributes that are either *discrete* or *continuous* in nature. Discrete attributes can assume a single value from a finite set of values, such as the number rolled on a six-face die, for example. All qualitative attributes are inherently discrete since they are countable and can assume only a certain set of values. Continuous attributes, on the other hand, can assume one of an uncountably infinite set of values (which exist along a continuum), such as the temperature in a room, for example. Continuous attributes can further be categorised into attributes which assume values on an *interval* scale or a *ratio* scale. The latter assumes that there exists a so-called *true zero* which reflects the absence of the specific measured feature. An object's weight is, for example, a ratio attribute since a value of zero indicates that the object has no weight. Interval attributes, on the other hand, do not assume this property. Temperature measured in degrees Fahrenheit ($^{\circ}F$) or Celsius ($^{\circ}C$) is, for example, an interval attribute since zero $^{\circ}F$ or $^{\circ}C$ does not imply no temperature. If, however, temperature is measured in Kelvin, then it may be expressed as a ratio estimate, since zero Kelvin indicates the absence of temperature.

## Data cleaning

Probably the most important and, at the same time, mundane tasks associated with the data preparation process is that of *data cleaning*<d-cite key="Press2016"></d-cite>. The purpose of data cleaning is to remove any errors in and/or missing values from a data set so as to ensure that the data being used to formulate a learning model's hypothesis is of a high quality, thereby instilling confidence in the results obtained. This typically involves three types of activities, namely: (1) correcting erroneous values, (2) *imputing* (filling in) missing data entries and (3) detecting outliers<d-cite key="Nisbet2009"></d-cite>. Examples of these occurrences are illustrated in Table~\ref{7.tbl.errors}, where the first column denotes the data attribute the recorder attempted to capture, the second column describes the true data that should have been entered by the data recorder, and the last column contains the actual data recorded for an observation. As may be seen, the values for the attributes `Annual income` and `Ethnicity` have been left blank, the attributes `Name`, `Date of birth` and `Place of birth` are incomplete, and the attribute `Sex`, although in the correct format, does not reflect the true value.

\begin{table}[!htb]
	\centering
	\caption[Examples of erroneous and missing data values]{Examples of erroneous and missing data values<d-cite key="Davis2010"></d-cite>.}\label{7.tbl.errors}
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

According to Hellerstein<d-cite key="Hellerstein2008"></d-cite>, there exists four main origins of erroneous values in a data set. The first of these is described as *data entry errors* which are primarily attributed to the typographic and misinterpretation errors humans make when attempting to capture the data. The second origin is described as *measurement errors* which occur when measuring a physical property is not conducted according to a proper procedure. The third origin is described as *distillation errors* which occur during the preprocessing and summarising phase before subsequently being added to a system database. The final origin is described as *data integration errors* which occur when multiple data sets are integrated into a single database.

Since the majority of learning models require a complete data set in order to function properly, it is necessary to address any missing data in a data set. Obviously, the best approach would be to manually fill in all incomplete entries to contain the correct information. This is, however, not always practical, especially if there is a significant proportion of missing values in a considerably large data set. The second approach, which may seem more reasonable, would be to delete any observations with missing values entirely, leaving only the observations that contain values for all of the defined attributes. This may, however, result in a substantial loss in the overall set of observations, to the point where there are too few data to conduct a meaningful investigation. Another approach is to identify those attributes in the data set which possess a large proportion of missing values. If the proportion of missing values of an attribute exceed a specific threshold, then the attribute may be disregarded altogether. The final approach would be to employ a specific imputing technique where missing values are filled according to some specified rule. Many imputing methods have been proposed in the literature, namely: *Mean imputation* (replace missing values with the average/most frequent value), *hot deck imputation* (replace missing values with randomly chosen observations of similar values), *cold deck imputation* (replace missing values with systematically chosen observations of similar values), *random imputation* (replace missing values with random values) and *model-based imputation* (construct a learning model to predict missing values based on values of complete observations)<d-cite key="Davis2010, GraceMartin2008"></d-cite>.

The final activity in the data cleaning process is concerned with the detection of any outliers that may be present in a data set, where an outlier is described as an observation that does not conform to the general pattern observed in a data set<d-cite key="Moore2009"></d-cite>. Aguinis *et al.*<d-cite key="Aguinis2013"></d-cite> described a set of best-practice recommendations concerned with *defining*, *identifying*, and *handling* outliers. There exists a wide variety of statistical techniques for the purposes of outlier definition and identification, which are typically described as *univariate* (identifying extreme values of a single attribute), *multivariate* (identifying unusual value combinations for two or more attributes), *parametric* (based on underlying distribution assumptions) and *non-parametric* (not based on any distribution assumptions) methods<d-cite key="Williams2002"></d-cite>. The appropriate handling of outliers is, however, problem-specific<d-cite key="Nisbet2009"></d-cite>. If the aim of a study is to identify a general pattern in the data, then outliers are typically removed. If, on the other hand, the goal of a study is to detect unusual observations (*e.g.* a fraudulent bank transactions), then outliers provide essential information.

## Data partitioning methods

The initial sample set is typically partitioned into three separate data sets, namely a *training set*, a *validation set* and a *testing set*<d-cite key="Brownlee2017"></d-cite>, as shown in Figure~\ref{4.fig.sampledata}. The training set is used to find optimal parameter settings (or weights) for a learning model so that it fits the set of training examples. The validation set (sometimes called the *hold-out set*) is used to provide an unbiased evaluation of a learning model's fit in respect of the training set while also tuning the hyper-parameters of the model. For this reason, the combined training and validation sets are often referred to as the *learning set*. The evaluation of a model, however, becomes more biased as its predictive ability in respect of the validation set is incorporated into the learning model's configuration<d-cite key="Shah2017"></d-cite>. It is, therefore, necessary to evaluate a learning model's performance in respect of an independent test set so as to provide a completely unbiased evaluation of a final model fit in respect of the training set. In order to enhance the performance of learning models, various *data re-sampling* methods may be employed to construct the training and validation sets<d-cite key="James2013"></d-cite>, two of the most popular being the method of *cross-validation* and the *bootstrap re-sampling* method.

{% include figure.html path="assets/img/blog/blog7.5.png" class="img-fluid rounded z-depth-1" zoomable=true %}

### Cross-validation

There are two main types of cross-validation methods, namely: $k$-*fold* cross-validation and *leave-one-out* cross-validation. In the case of $k$-fold cross-validation, the set of $m$ observations is partitioned into $k$ equal (or as close to equal as possible) parts (or folds), as illustrated graphically in Figure~\ref{4.fig.kfold}. A total of $k-1$ folds of the data are then used for training a learning model, while the remaining fold is used for model validation. This process is repeated a total of $k$ times, each time using a different fold for validation purposes. The final performance of a learning model is then estimated as the average of the misclassification errors seen during the $k$ repetitions.

The leave-one-out method of cross-validation is a special case of the $k$-fold cross-validation that occurs when $k=m$ (*i.e.* $k$ is equal to the number of observations). As its name suggests, all observations, except one (*i.e.* $m-1$ observations), are used to train the learning model according to this method, and the remaining observation is used for model validation. This procedure is thus repeated a total of $m$ times, where the final performance of a learning model is estimated as the average of the $m$ misclassification errors.

### Bootstrap re-sampling

The second type of data sampling method is that of *bootstrap re-sampling* (or just *bootstrapping*), where the aim is to create a *bootstrap sample* of observations for training and validating learning models --- the method by which it achieves these subsets is, however, quite different. Bootstrapping involves the repeated selection of observations from the learning set *with* replacement<d-cite key="Tonidandel2009"></d-cite> to construct a new set of any cardinality, denoted by $L$ (since observations can be reused). Given a specified validation-training proportion split $p$, validation and training sets of cardinalities $pL$ and $(1-p)L$, respectively, may be defined a total of $b$ times, as illustrated in Figure~\ref{4.fig.bootstrapping}. The final performance of a learning model is then estimated as the average of the misclassification errors achieved over the $b$ validating sets. This method, therefore, stands in contrast to $k$-fold cross-validation which is limited by the size of the learning set and the number of folds.

{% include figure.html path="assets/img/blog/blog7.6.png" class="img-fluid rounded z-depth-1" zoomable=true %}

## Performance measures

As previously described in \S\ref{4.sub.supervised}, the primary goal of any supervised learning procedure is choosing an appropriate model. One must, therefore, be aware of various notions that may assist in selecting, configuring and evaluation a model for a specific learning task. More specifically, one should be able to: (1) Evaluate the performance of a learning model according to an appropriate performance measure, (2) identify whether a learning algorithm is generalising the data, and (3) appreciate the trade-off associated with employing a complex learning model rather than one that is easier to understand. These topics are discussed in this section.

Since the evaluation of a learning model is essential in any ML investigation, one must be able to choose a performance measure that is most appropriate when presented with either a classification or regression task. In the case of the latter, the most popular performance measures are the well-known *mean square error*, *mean absolute error* and *R-squared error*<d-cite key="Scikit2013"></d-cite>. In the case of a classification task, however, the performance evaluation of a learning model is more involved. The performance of a classifier is typically first described in the form of a confusion matrix, as illustrated in Table~\ref{4.tbl.confusion} (for a binary classification task), where each row of the matrix represents the *actual* category of the observations while each column represents the observation category *predicted* by the learning model.

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
\text{CA}=\frac{\text{TP}+\mbox{TN}}{\text{TP}+\text{FP}+\text{FN}+\text{TN}}.
\end{equation}

Although CA seems to be an intuitive measure by which to assess a classifier, it should never be used when there is a large category imbalance in a data set. Consider, for example, a case in which $950$ positive observations and $50$ negative observations are contained in a data set. Then a learning model can easily achieve a CA of $95\%$ simply by always predicting the majority category (*i.e.* the positive category).

In order to overcome this flaw, the performance of a learning model can instead be described in terms of two separate measures, namely *precision* and *recall*. Precision describes what proportion of the observations predicated as positive are actually positive observations, expressed mathematically as

\begin{equation}
\mbox{Precision}=\frac{\mbox{TP}}{\mbox{TP}+\mbox{FP}}.\label{4.eqn.precision}
\end{equation}

Recall (also known as *sensitivity*) describes what proportion of the observations that are actually positive were correctly classified as positive, expressed mathematically as

\begin{equation}
\mbox{Recall}=\frac{\mbox{TP}}{\mbox{TP}+\mbox{FN}}.\label{4.eqn.recall}
\end{equation}

Although these two performance measures can be used together to measure the performance of a single learning model, in the case when two learning models are compared, it is not always clear which learning model achieves superior performance. For this reason, an alternative performance measure, called the $\text{F}_{\beta}$-score is defined as

\begin{equation}
\text{F}_{\beta}=\left(\frac{1}{\beta\left(\frac{1}{\text{Precision}}\right)+(1-\beta)\left(\frac{1}{\text{Recall}}\right)}\right).\label{4.eqn.fbeta}
\end{equation}

This score is a weighted harmonic mean of precision and recall in which $\beta$ is a weight parameter used to specify the importance of precision over recall. The choice of $\beta$ is, to a large extent, problem-specific. A value of $\beta$ close to one essentially reduces (\ref{4.eqn.fbeta}) to (\ref{4.eqn.precision}), while a value of $\beta$ close to zero reduces (\ref{4.eqn.fbeta}) to (\ref{4.eqn.recall}). A $\beta$-value of $b=0.5$ weights both precision and recall equally and is known in statistics as the F$_1$-score.

Arguably the most popular classification performance measure is the *area under curve* (AUC) performance measure<d-cite key="Hand2001b, Huang2005, Rosset2004"></d-cite> as a result of its robustness against significant category imbalances<d-cite key="Fawcett2006"></d-cite>. As its name implies, AUC measures the area under a specific curve, called the *receiver operating characteristic* (ROC) curve. An ROC curve is a graphical plot of the predictive ability of a binary classifier. Consider, for example, a binary classification problem in which a learning model predicts a category probability (*i.e.* a value between zero and one) for a set of observations, where one denotes a strong positive category prediction and zero indicates a strong negative category prediction. The two distributions of the actual positive and actual negative observations are illustrated graphically in Figure~\ref{4.fig.auc}(a) according to the category probabilities predicted by the learning model. An ROC curve (like that shown in Figure~\ref{4.fig.auc}(b)) may, therefore, be constructed by varying the classification threshold (indicated in Figure~\ref{4.fig.auc}(a)) from a category probability of zero to one and plotting the learning model's *true positive rate* (TPR) against its *false positive rate* (FPR) for all possible classification thresholds. The TPR of a learning model may be expressed mathematically as

\begin{equation}
\text{TPR}=\frac{\text{TP}}{\text{TP}+\text{FN}}=\text{Recall},
\end{equation}

while its FPR may be expressed as

\begin{equation}
\text{FPR}=\frac{\text{FP}}{\text{FP}+\text{TN}}.
\end{equation}

{% include figure.html path="assets/img/blog/blog7.7.png" class="img-fluid rounded z-depth-1" zoomable=true %}

The area under the ROC curve (or just the AUC) indicates to what extent a model is able to distinguish between positive and negative observations. The AUC score may also be interpreted as the probability that a learning model ranks a randomly chosen positive observation higher than a random negative observation. An AUC score of one is, therefore, the most ideal scenario as it indicates that a learning model can perfectly distinguish between positive and negative categories. An AUC score of zero indicates that the learning model is, in fact, reciprocating the categories (*i.e.* positive observations are always predicted as negative and *vice versa*). In actuality, this is also an ideal scenario since merely inverting the predictions of the learning model would produce a perfect classifier. An AUC score of $0.5$ (indicated by the area under the dashed line in Figure~\ref{4.fig.auc}(b)), on the other hand, is the worst-case scenario as it indicates that a learning model has no discrimination capacity to distinguish between the positive and negative categories (*i.e.* the learning model is no better than random guessing)<d-cite key="Tape2001"></d-cite>.

{% include figure.html path="assets/img/blog/blog7.8.png" class="img-fluid rounded z-depth-1" zoomable=true %}

## Overfitting vs underfitting the training data

One of the most fundamental concepts to grasp in ML is the notion of a learning model *underfitting* or *overfitting* a set of training data. Underfitting occurs when a learning model does not perform well in respect of either the training set or the validation set, as illustrated by a learning model with a complexity of "A" in Figure~\ref{4.fig.underover}. In this case, the model is too simple and cannot model the underlying pattern (or trend) in the training set, resulting in a poor ability to predict unseen observations accurately. Overfitting, on the other hand, occurs when a learning model performs well in respect of the training set, but significantly poor in respect of the validation set, as illustrated by a learning model with a complexity of "C" in Figure~\ref{4.fig.underover}. In this case, the learning model is not learning the underlying trend. It is rather memorising the observations in the training set, again resulting in a poor generalisation ability (and thus a poor prediction performance) in respect of unseen observations. An ideal learning model is, therefore, one that achieves a suitable trade-off between underfitting and overfitting the observations in the training set, as illustrated by a learning model with a complexity of "B" in Figure~\ref{4.fig.underover}. In this case, the learning model is able to generalise the underlying trend displayed appropriately by the observations in the training set which result in a better overall prediction performance in respect of unseen observations.

{% include figure.html path="assets/img/blog/blog7.8.png" class="img-fluid rounded z-depth-1" zoomable=true %}

## Model performance vs model interpretability

Understanding why an ML model makes a specific prediction may, in many applications, be just as crucial as the accuracy of the prediction itself. A model that is interpretable creates a certain degree of trust in its predictions, provides insights into the process being modelled, and highlights potential biases present in the data under consideration. The lack of interpretability of many ML models (often called *black-box* models) is a frequent barrier to their adoption, since end-users typically prefer solutions that are understandable to a human<d-cite key="Molnar2018"></d-cite>. This may require researchers to sacrifice models which achieve superior predictive ability in favour of models that yield more understandable results (often called *white-box* models), but possibly achieve a lower overall performance. In the ML literature, this is referred to as the trade-off between the accuracy and interpretability of a model's output.

The ethical considerations associated with autonomous vehicles, for example, are often called into question since the learning models (typically reinforcement learning algorithms<d-cite key="Dar2018"></d-cite>) used to control such a vehicle are inherently black-box in nature. Consequently, if an autonomous vehicle is involved in an RA, humans are not able to understand *why* an autonomous vehicle made the manoeuvres it did. This has significant legal implications as the notion of accountability comes into question, especially if an autonomous vehicle RA results in fatalities<d-cite key="Bonnefon2016"></d-cite>. Researchers have, therefore, been investing more time into developing techniques that provide insights into the working of specific black-box learning models so that humans may begin to understand the methods by which specific models arrive at their conclusions<d-cite key="Lewis2017"></d-cite>. A wide variety of methods have recently been proposed for addressing this issue<d-cite key="Bach2015, Datta2016, Lipovetsky2001, Ribeiro2016, Shrikumar2016, Strumbelj2014"></d-cite>. The understanding of how these methods are related and which method is preferable to another was, however, still unclear until Lundberg and Lee<d-cite key="Lundberg2017"></d-cite> proposed their idea of unifying these methods into a single approach for interpreting model predictions, which they termed *Additive Feature Attribution* (AFA) methods.

An AFA method seeks to locally approximate a complex, non-linear ML model by using a simpler, linear model, called an *explanation model*, whose predictions are much easier to interpret. As a way of explanation, let $f$ be the original learning model to be explained and let $g$ be the associated explanation model. Most explanation models are expressed in terms of so-called *simplified inputs* $\mathbf{x}^{\prime}=\langle x_1^{\prime}\cdots x_N^{\prime}\rangle$ that map to the original inputs through a mapping function $\mathbf{x}=\mathbf{h}_{\mathbf{x}}(\mathbf{x}^{\prime})$. These local approximation methods, therefore, attempt to ensure that $g(\mathbf{z}^{\prime})\approx f(\mathbf{h}_{\mathbf{x}}(\mathbf{z}^{\prime}))$ whenever $\mathbf{z}^{\prime}\approx \mathbf{x}^{\prime}$. An AFA method is, therefore, one in which its explanation model is a linear function of binary variables which takes the form

\begin{equation}
g(\mathbf{z}^{\prime})=\phi_0+\sum_{i=1}^N\phi_iz_i^{\prime},\label{3.eqn.afa}
\end{equation}

where $\phi_0,\ldots,\phi_N\in\mathbb{R}$, $\mathbf{z}^{\prime}\in\{0,1\}^N$ and $\phi_0=f(\mathbf{h}_{\mathbf{x}}(\mathbf{0}))$<d-cite key="Lundberg2017"></d-cite>. In essence, an importance $\phi_i$ is attributed to each of the $N$ features and, by summing the effects of all feature attributions, an approximation is obtained of the original model output $f(\mathbf{x})$.

Lundberg and Lee<d-cite key="Lundberg2017"></d-cite> demonstrated that many current methods in the literature conform to the representation in (\ref{3.eqn.afa}) but it was still not obvious how to compare one AFA method with another appropriately. They therefore went on to define three desirable properties that any AFA method should exhibit: The first is the so-called *local accuracy property* which requires the output of the explanation model $g$ to match the original model $f$ for the simplified input $\mathbf{x}^{\prime}$ being explained (*i.e.* $f(\mathbf{x})=g(\mathbf{x}^{\prime})=\phi_0+\sum_{i=1}^N\phi_ix_i^{\prime})$. The second is the *missingness property* which specifies that a missing feature should have no attributed importance (*i.e.* $x_i^{\prime}=0\Longrightarrow\phi_i=0$). And finally, the third property is that of *consistency* which states that if feature $i$ always has the same or a greater impact in model $f^{\prime}$ than in model $f$, then the importance $\phi_i$ of feature $i$ should be greater in model $f^{\prime}$ than in model $f$, described mathematically as

\begin{equation}
f^{\prime}_{x}(\mathbf{z}^{\prime})-f^{\prime}_{x}(\mathbf{z}^{\prime}\backslash i)\geq f_{x}(\mathbf{z}^{\prime})-f_{x}(\mathbf{z}^{\prime}\backslash i)\mbox{ for all } \mathbf{z}^{\prime}\in\{0,1\}^N\Longrightarrow\phi_i(f^{\prime},\mathbf{x})\geq\phi_i(f,\mathbf{x}),\label{3.pro.3}
\end{equation}

where $f_{x}(\mathbf{z}^{\prime})=f(\mathbf{h}_{\mathbf{x}}(\mathbf{z}^{\prime}))$ and $\mathbf{z}^{\prime}\backslash i$ denotes setting $z^{\prime}_i=0$.

It follows from the theory of cooperative games that there exists only one solution for the values $\phi_0,\ldots,\phi_N$ in (\ref{3.eqn.afa}) which always satisfies the three aforementioned properties:

\begin{equation}
\phi_i(f,\mathbf{x})=\sum_{\mathbf{z}^{\prime}\subset_0 \mathbf{x}^{\prime}}\frac{|\mathbf{z}^{\prime}|!(N-|\mathbf{z}^{\prime}|-1)!}{N!}[f_{x}(\mathbf{z}^{\prime})-f_{x}(\mathbf{z}^{\prime}\backslash i)],\label{3.eqn.shapely}
\end{equation}

where $|\mathbf{z}^{\prime}|$ is the number of non-zero entries in $\mathbf{z}^{\prime}$, $\mathbf{z}^{\prime}\subset_0 \mathbf{x}^{\prime}$ represents all the vectors for which the non-zero entries are a subset of the non-zero entries in $\mathbf{x}^{\prime}$, and the values $\phi_i$ are known as Shapley values<d-cite key="Shapely1953"></d-cite>. In essence, the importance $\phi_i$ of feature $i$ is determined by comparing a model's prediction with and without the feature, weighted over all possible feature combinations. Due to the large computational cost associated with exploring all possible feature combinations, sampling techniques used to approximate the Shapely values in (\ref{3.eqn.shapely}) are commonly employed<d-cite key="Lundberg2017"></d-cite>.

Using this result, Lundberg and Lee<d-cite key="Lundberg2017"></d-cite> proposed a unified measure of feature importance, called SHAP values, which are defined as the Shapley values of a conditional expectation function of the original model, and are thus solutions to (\ref{3.eqn.shapely}) for which $f_x(\mathbf{z}^{\prime})=f(\mathbf{h}_{\mathbf{x}}(\mathbf{z}^{\prime}))=E[f(\mathbf{z})\mid \{z_i=x_i\mid i\in\mathcal{S}\}]$, where $\mathcal{S}$ is the set of non-zero indices in $\mathbf{z}^{\prime}$. By way of example, consider the illustration in Figure~\ref{3.fig.shap}. In this case, $f(\mathbf{x})$ is the output predicted by the model, and $E[f(\mathbf{z})]$ is the base value (or average model output) which would be predicted if no features were present. If the feature value $x_1$ is included in the model, then $\phi_1$ explains how the base value $E[f(\mathbf{z})]$ became the new predicted value $E[f(\mathbf{z})\mid z_1=x_1]$. Repeating this process for the remaining feature values $x_2$, $x_3$ and $x_4$, estimates of the SHAP values for $\phi_2$, $\phi_3$ and $\phi_4$ are obtained, showing how the model ultimately arrives at the predicted output $f(\mathbf{x})$.

{% include figure.html path="assets/img/blog/blog7.9.png" class="img-fluid rounded z-depth-1" zoomable=true %}
