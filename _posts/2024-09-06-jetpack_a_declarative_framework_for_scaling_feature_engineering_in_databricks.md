---
layout: distill
title: 🚀 Jetpack - A Declarative Framework for Scaling Feature Engineering in Databricks
description: How I reduced feature creation and operationalisation time by 10x 
date: 2024-09-06
tags: CaseStudy
giscus_comments: true
thumbnail: assets/img/blog/blog14.1.jpg

authors:
  - name: Shane van Heerden
    affiliations:
      name: Luno

bibliography: bibliography.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: 1. Introduction
  - name: 2. The need for a feature store
  - name: 3. Goals of Jetpack
  - name: 4. How does Jetpack work?
  - name: 5. Wrapping up

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

# 🚧 <b>WORK IN PROGRESS</b> 🚧

{% include figure.html path="assets/img/blog/blog14.1.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

## 1. Introduction

{% include figure.html path="assets/img/blog/blog14.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 1: The components needed to put an ML system into production.</em> 
</div>

<div class="col-sm mt-3 mt-md-0">
    {% include video.html path="assets/video/jetpack.mp4" class="img-fluid rounded z-depth-1" controls=true %}
</div>

***

## 2. The need for a feature store

Feature engineering is a critical step in machine learning pipelines, where raw data is transformed into meaningful features that drive model performance. With ML practitioners typically spending 60-70% of their time on feature and data engineering, streamlining this process has the ability to significantly accelerate a company's AI maturity. However, traditional feature engineering approaches often result in:

- **Complexity:** Features are scattered across multiple scripts, notebooks, and databases.
- **Fragility:** Changes to feature definitions or dependencies can break downstream models.
- **Scalability:** Feature computation and management become cumbersome as feature stores grow.

{% include figure.html path="assets/img/blog/blog14.3.png" class="img-fluid rounded z-depth-1" zoomable=true %} <center>(a)</center><br>
{% include figure.html path="assets/img/blog/blog14.4.png" class="img-fluid rounded z-depth-1" zoomable=true %} <center>(b)</center>
<div class="caption">
    <em>Figure 2: Data flow of ML systems (a) without and (b) with a feature store.</em> 
</div>

To address these challenges, we've introduced a declarative framework for facilitating feature engineering at Luno, which we’ve named **Jetpack**.

{% include figure.html path="assets/img/blog/blog14.5.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 3: The custom feature engineering infrastructure developed at major tech companies.</em> 
</div>

***

## 3. Goals of Jetpack

The primary goal of Jetpack is to:

> Accelerate feature engineering by enabling Data Scientists to <em>declaratively</em> define efficient <em>end-to-end</em> feature pipelines and easily manage the <em>operational lifecycle</em> of features.

By defining features using high-level configurations, we decouple feature definitions from their implementation details. This approach brings numerous benefits, namely:

- **Simplification:** Feature definitions are concise, readable, and maintainable.
- **Reusability:** Features can be easily shared across models and projects and better organised according to standard entities.
- **Flexibility:** Changes to feature definitions are properly managed and propagated to downstream models.
- **Scalability:** Feature computation and management can be optimised for performance.

This approach is largely inspired by [Doordash’s approach](https://www.infoq.com/presentations/fabricator-ml/) to scaling feature engineering albeit reimagined within a Databricks context. In about a year of adopting this approach, Doordash was able to scale their number of unique features by 5x, their daily feature values by 10x, and their feature jobs by 8.33x.

***

## 4. How does Jetpack work?

On a high level and with reference to Figure 4, all features are constructed from upstream silver and/or gold tables, or even existing feature store tables. The definition of the feature together with the orchestration details are specified as a simplified YAML config files. The actual orchestration of features is facilitated by the Jetpack system which periodically writes feature values to downstream feature store tables.

{% include figure.html path="assets/img/blog/blog14.6.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 4: The high-level jetpack architecture.</em> 
</div>

Three main categories of information are required when defining a feature, namely:

1. **Upstream table dependency information** which is needed to facilitate the feature computation,
2. **Feature information** which describes the logic needed to compute a feature as well as additional metadata, and
3. **Orchestration information** which describe the details of how the feature should be computed.

An example of a feature configuration file is shown below. In this example, we want to create a series of features that count the number of broker buys and sells for a customer over multiple time windows. Take some time to read through the comments in the example config file to get a better idea of the information needed to define this feature.

```YAML
# Upstream
dependencies:  # Add all upstream table dependencies used in the 'sql' feature definition below must be defined here. You can add other table dependency by extending this list.
  - table: base.bitx.vw_users  # This is the table in catalog.schema.table format.
    alias: users  # This is an alias name you can give the table to use in the 'sql' feature definition below.
  - table: base.broker.quotes
    alias: quotes

# Feature
name: count_broker_[quote_type.key]s_[day_window]d  # This is the name of the feature. Parameter injection notation, defined in 'params', can be used here.
description: The number of broker [quote_type.key]s exercised by the customer in the last [day_window] days.  # This is the description of the feature. Parameter injection notation, defined in 'params', can be used here.
tags:  # These are the tags associated with the feature. Tags are useful when grouping features by domain.
  - fraud
  - churn
is_pii: false  # Set this to 'true' if your feature is PII.
data_type: numerical  # This is the data type of the feature. Parameter injection notation, defined in 'params', can be used here. Supported data types include: 'numerical', 'binary', 'categorical' and 'ordinal'.
version: 1  # If this is the first version of the feature, this should have a value of 1. If you are proposinging updated logic to an existing feature that is being used by downstream models, a new feature yaml file should be created with an incremented version number. Depricated features should be retired in the corresponding '_archive' folder only when all upstream consumers have been migrated off the feature.
params:  # These are parameters that can be injected into the 'name', 'description', 'sql' and 'orchestration.depends_on' fields. This is useful for cases when you want to generate many different features from the same underlying logic but with different parameter values. You can use parameter injection by specifying parameter names using square bracket notation (i.e. [])). There are two parameter types that you may choose:
  day_window: [7, 14, 30, 90, 180, 365]  # List parameters are defined like this. This means that anywhere we specify [day_window], the values 7, 14, 30, 90, 180 and 365 from this list would be sequentially injected.
  quote_type: {"buy": 1, "sell": 2}  # Dictionary parameters are defined like this. This means that anywhere we specify [quote_type.keys], the values 'buy' and 'sell' would be sequentially injected. In the same way, anywhere we specify [quote_type.values], the values '1' and '2' would be sequentially injected.
# In this example, since there are 6 'day_window' parameter values and 2 'quote_type' parameter values, this example.yaml file will create 6*2=12 feature columns in our final feature store.
sql: >-  # This is where you define the logic for your feature. Your SQL must contain a column called "id" (and optionally "reference_date"), together with the single feature value column. If you are defining features on a users level, a final join on users is recommended like in this example. If a "reference_date" column is not specified, it will be set automatically based on the 'cadence' specified under 'orchestration' below. The '>-' syntax at the beginning of this SQL statement allows us to write the SQL statement over multiple lines.
      WITH user_broker_tx AS (
          SELECT user_id, COUNT(DISTINCT id) as count_broker_tx
          FROM {quotes}  # This is the 'alias' table name defined in 'dependencies' above.
          WHERE status = 4 -- Complete
          AND quote_type = [quote_type.value] -- Buy/Sell
          AND exercised_at IS NOT NULL
          AND base_currency in [CRYPTO_CURRENCIES]  # Specific static lists (not defined under 'params') can also be injected into the SQL query. This is useful when you do not want to hardcode specific values which may change over time (e.g. the crypto currency codes we support). For a list of supported lists, see here: https://gitlab.com/lunomoney/product-engineering/py/-/blob/main/notebooks/ml/features/jetpack.py#L26-31.
          AND DATEDIFF(CAST(GETDATE() AS Date),CAST(exercised_at AS Date)) <= [day_window] # The [day_window] values are being injected here.
          GROUP BY user_id
      )
      SELECT id, coalesce(count_broker_tx, 0) AS [NAME] # The NAME parameter should be used to ensure the feature name is identical to the value specifed under the 'name' attribute
      FROM {users} a  # This is the 'alias' table name defined in 'dependencies' above.
      LEFT JOIN user_broker_tx b
      ON a.id = b.user_id

# Downstream
orchestration:  # All parameters concering the orchestration of the feature are defined here.
  enabled: false  # Set this to 'true' if you want to enable computation of this feature.
  cadence: daily  # This is how often we write out the feature. Supported cadences include: 'daily', 'weekly' and 'monthly'.
  cluster: small  # This is the cluster size to be used to compute the feature. Supported types include: 'small'.
  engine: standard  # This is the runtime engine to be used to compute the feature. Supported types include: 'standard' and 'photon'.
  depends_on:
    - feature_v1  # If this feature depends on a lower level feature, the dependency should be defined here.
```

Assuming a set of feature configurations have been defined, Jetpack is now employed to facilitate the feature orchestration and computation process. A detailed visual description of the feature computation process is shown in Figure 5.

The process is initiated by a job which assumes a set of `cadence`, `cluster` and `engine` specifications. When the job kicks off, any features with the same `cadence`, `cluster` and `engine` specifications as the job are identified as the subset of features to be computed in this job run.

Since one may specify dependencies between features (using the `depends_on` feature attribute), this results in a directed acyclic graph (DAG) of feature orderings. Hence, features must therefore be organised into batches and computed sequentially. In other words and with reference to Figure 2, since `feature2.yaml` depends on `feature1.yaml` (i.e. the `sql` logic in `feature2.yaml` needs the results of `feature1.yaml`), we first compute `feature1.yaml` in `Batch 1` and merge the result into the feature store before proceeding with `Batch 2` which contains `feature2.yaml`.

In a computational batch, the `sql` logic defining the feature’s value is computed using the `spark.sql()` method, where all upstream `dependiencies` are passed as keyword arguments to reduce the number of read operations if there are shared table dependencies between features in the batch. The individual feature results are then combined using an outer join and subsequently merged into the appropriate feature store.

{% include figure.html path="assets/img/blog/blog14.7.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 5: A visual depiction of the feature orchestration and computation process facilitated by the Jetpack framework.</em> 
</div>

***

## 5. Wrapping up
