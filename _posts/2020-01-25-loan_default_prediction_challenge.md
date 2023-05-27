---
layout: distill
title: 🏦 Loan Default Prediction Challenge
description: Can you predict who will default on a loan?
date: 2020-01-25
tags: CaseStudy
giscus_comments: true
thumbnail: assets/img/blog/blog8.1.jpg

authors:
  - name: Shane van Heerden
    affiliations:
      name: Cape AI

bibliography: bibliography.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: 1. The problem
  - name: 2. The data
  - name: 3. Methodology
  - name: 4. Data exploration
  - name: 5. Predictive model results
  - name: 6. Conclusion

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

{% include figure.html path="assets/img/blog/blog8.1.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

## 1. The problem

SuperLender is a local digital lending company who prides themselves in delivering profitable and high-impact loan alternatives to the people of Nigeria. The company has approached Cape Town AI to develop an effective credit risk model to predict the odds that a returning customer will repay their loan. The assessment approach should be based on two main risk drivers of loan default prediction: (1) The willingness and (2) the ability of a customer to pay back their loan. In this report, a detailed description of the analysis conducted by Cape Town AI is provided, together with recommendations to SuperLender with regards to the types of customers who are more and less likely to default on their loans.

***

## 2. The data

SuperLender provided us with three separate sets of data namely: (1) Performance data, (2) previous loans data and (3) demographic data. A breakdown of the features, data types and descriptions for the performance, previous loans and demographics datasets are provided in Tables~\ref{2.tbl.performance}, \ref{2.tbl.previous} and \ref{2.tbl.demographics}, respectively. The performance dataset contains repeat loans taken by customers and contains a binary feature {\tt good\_bad\_flag} indicating whether the customer settled their loan on time or not --- this feature serves at the target variable. The previous loans dataset contains all previous loans that a customer in the performance dataset took out prior to the loan. And finally, the demographics dataset contains demographic information corresponding to each customer in the performance dataset. All three of the aforementioned datasets are related {\em via} the {\tt customerid} key.
<table>
  <tr>
    <th>#</th>
    <th>Feature name</th>
    <th>Data type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>1</td>
    <td>customerid</td>
    <td>Meta</td>
    <td>Primary key</td>
  </tr>
  <tr>
    <td>2</td>
    <td>systemloanid</td>
    <td>Meta</td>
    <td>The id associated with the particular loan</td>
  </tr>
  <tr>
    <td>3</td>
    <td>loannumber</td>
    <td>Continuous</td>
    <td>The number of the loan</td>
  </tr>
  <tr>
    <td>4</td>
    <td>approveddate</td>
    <td>Timestamp</td>
    <td>Date that loan was approved</td>
  </tr>
  <tr>
    <td>5</td>
    <td>creationdate</td>
    <td>Timestamp</td>
    <td>Date that loan application was created</td>
  </tr>
  <tr>
    <td>6</td>
    <td>loanamount</td>
    <td>Continuous</td>
    <td>Loan value taken</td>
  </tr>
  <tr>
    <td>7</td>
    <td>totaldue</td>
    <td>Continuous</td>
    <td>Total repayment required to settle the loan</td>
  </tr>
  <tr>
    <td>8</td>
    <td>termdays</td>
    <td>Continuous</td>
    <td>Term of loan</td>
  </tr>
  <tr>
    <td>9</td>
    <td>referredby</td>
    <td>Meta</td>
    <td>The customerid of the referral customer</td>
  </tr>
  <tr>
    <td>10</td>
    <td>good_bad_flag</td>
    <td>Categorical</td>
    <td>Whether the customer settled their loan on time or not</td>
  </tr>
</table>
<div class="caption">
    <em>Table 1:</em> The features, data types and descriptions of the performance dataset.
</div>
<table>
  <tr>
    <th>#</th>
    <th>Feature name</th>
    <th>Data type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>1</td>
    <td>customerid</td>
    <td>Meta</td>
    <td>Primary key</td>
  </tr>
  <tr>
    <td>2</td>
    <td>systemloanid</td>
    <td>Meta</td>
    <td>The id associated with the particular loan</td>
  </tr>
  <tr>
    <td>3</td>
    <td>loannumber</td>
    <td>Continuous</td>
    <td>The number of the loan</td>
  </tr>
  <tr>
    <td>4</td>
    <td>approveddate</td>
    <td>Timestamp</td>
    <td>Date that loan was approved</td>
  </tr>
  <tr>
    <td>5</td>
    <td>creationdate</td>
    <td>Timestamp</td>
    <td>Date that loan application was created</td>
  </tr>
  <tr>
    <td>6</td>
    <td>loanamount</td>
    <td>Continuous</td>
    <td>Loan value taken</td>
  </tr>
  <tr>
    <td>7</td>
    <td>totaldue</td>
    <td>Continuous</td>
    <td>Total repayment required to settle the loan</td>
  </tr>
  <tr>
    <td>8</td>
    <td>closeddate</td>
    <td>Timestamp</td>
    <td>Date that the loan was settled</td>
  </tr>
  <tr>
    <td>9</td>
    <td>referredby</td>
    <td>Meta</td>
    <td>The customerid of the referral customer</td>
  </tr>
  <tr>
    <td>10</td>
    <td>firstduedate</td>
    <td>Timestamp</td>
    <td>Date of first payment due</td>
  </tr>
  <tr>
    <td>11</td>
    <td>firstrepaiddate</td>
    <td>Timestamp</td>
    <td>Actual date that customer paid the first payment</td>
  </tr>
</table>
<div class="caption">
    <em>Table 2:</em> The features, data types and descriptions of the previous loans dataset.
</div>
<table>
  <tr>
    <th>#</th>
    <th>Feature name</th>
    <th>Data type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>1</td>
    <td>customerid</td>
    <td>Meta</td>
    <td>Primary key</td>
  </tr>
  <tr>
    <td>2</td>
    <td>birthdate</td>
    <td>Timestamp</td>
    <td>Date of birth of the customer</td>
  </tr>
  <tr>
    <td>3</td>
    <td>bank_account_type</td>
    <td>Categorical</td>
    <td>Type of primary bank account</td>
  </tr>
  <tr>
    <td>4</td>
    <td>longitude_gps</td>
    <td>Continuous</td>
    <td>Longitude of customer</td>
  </tr>
  <tr>
    <td>5</td>
    <td>latitude_gps</td>
    <td>Continuous</td>
    <td>Latitude of customer</td>
  </tr>
  <tr>
    <td>6</td>
    <td>bank_name_clients</td>
    <td>Categorical</td>
    <td>Name of customer's bank</td>
  </tr>
  <tr>
    <td>7</td>
    <td>bank_branch_clients</td>
    <td>Categorical</td>
    <td>Location of customer's branch</td>
  </tr>
  <tr>
    <td>8</td>
    <td>employment_status_clients</td>
    <td>Categorical</td>
    <td>Customer's type of employment</td>
  </tr>
  <tr>
    <td>9</td>
    <td>level_of_education_clients</td>
    <td>Categorical</td>
    <td>Customer's highest level of education</td>
  </tr>
</table>
<div class="caption">
    <em>Table 3:</em> The features, data types and descriptions of the demographics dataset.
</div>

***

## 3. Methodology

This section contains a description of the methodology followed during this investigation. The section opens in \S\ref{sec3.1} with a description of the features constructed in order to draw more information from the three sets of data. This is followed, in \S\ref{sec3.2}, by an elucidation of the data mining architecture constructed for the purpose of this analysis.

### 3.1. Feature engineering

Arguably, the most involved and important tasks in the data mining process is that of feature engineering which requires the data scientist to construct informative features based on one or more feature(s) currently present in the data \cite{Ng2012}. The features manually constructed for the purpose of predicting whether a SuperLender customer will default on their loan is described in this subsection.

In the previous loans dataset, the number of days a customer's loan was over/under the days stipulated by the loan's term is defined mathematically as

\begin{equation}
{\tt days\_}{\tt over\_}{\tt under}={\tt closedate}-({\tt approvedate}+{\tt termdays}).\label{daysoverunder}
\end{equation}

### 3.2. Data mining architecture



## 4. Data exploration



### 4.1. Data distribution analysis



### 4.2. Spatial analysis



### 4.3. Temporal analysis



## 5. Predictive model results



### 5.1. Hyperparameter tuning



### 5.2. Model performances



### 5.3. Model interpretations


## 6. Conclusion



### 6.1. Recommendations



### 6.2. Future work


