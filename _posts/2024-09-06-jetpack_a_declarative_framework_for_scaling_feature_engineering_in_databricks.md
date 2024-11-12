---
layout: distill
title: 🚀 Jetpack - A Declarative Framework for Scaling Feature Engineering in Databricks
description: How I reduced feature creation and operationalisation time by 10x 
date: 2024-09-06
tags: CaseStudy
giscus_comments: true
thumbnail: assets/img/blog/blog12.1.jpg

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
  - name: 4. How does it work?
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

{% include figure.html path="assets/img/blog/blog12.1.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

## 1. Introduction


<div class="col-sm mt-3 mt-md-0">
    {% include video.html path="assets/video/jobcrystal.mp4" class="img-fluid rounded z-depth-1" controls=true %}
</div>

***

## 2. The need for a feature store



***

## 3. Goals of Jetpack

The primary goal of Jetpack is to:

> Accelerate feature engineering by enabling Data Scientists to <em>declaratively</em> define efficient <em>end-to-end</em> feature pipelines and easily manage the <em>operational lifecycle</em> of features.

***

## 4. How does it work?

{% include figure.html path="assets/img/blog/blog12.2.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 1: The high-level jetpack architecture.</em> 
</div>

***

## 5. Wrapping up
