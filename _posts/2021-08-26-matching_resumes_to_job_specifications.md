---
layout: distill
title: 📄 Matching Resumes to Job Specifications (and vice versa)
description: Using NLP, Python, ElasticSearch and Streamlit
date: 2021-08-26
tags: CaseStudy
giscus_comments: true
thumbnail: assets/img/blog/blog10.1.jpg

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
  - name: 2. Understanding the clients needs
  - name: 3. The solution architecture
  - name: 4. Productionising the solution
  - name: 5. Project impact
  - name: 6. Wrapping up

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

{% include figure.html path="assets/img/blog/blog10.1.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

I always jump at the opportunity to tackle new and interesting challenges using Machine Learning and Advanced Analytics. In the constantly changing field of AI, this is how I discover and push the bounds of what is truly possible.

## 1. The problem

In the fast-paced world of job recruitment, staying ahead requires innovative solutions. Recently, I was tasked with probably my toughest challenges by a client, [JobCrystal](https://www.jobcrystal.com/), who operated a job recruitment firm within South Africa. I undertook the challenge of revolutionizing JobCrystal's current recruitment process by harnessing the power of advanced Natural Language Processing (NLP) techniques.

In this blog post, we'll take you through the intricacies of our project, detailing the architecture, preprocessing pipelines, data storage, APIs, trained models, user interface, and the intricate AI logic that powers our system. 

***

## 2. Understanding the clients needs



***

## 3. The solution architecture


{% include figure.html path="assets/img/blog/blog10.2.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 1: Solution architecture.</em> 
</div>

### 3.1. Initialize

The system ingests data from two primary locations, namely: JobCrystal's FTP server and Dropbox account. The FTP server holds approximately 35 000 unlabelled resumes and job specs, while Dropbox contributes around 3,000 labeled resumes associated with job specifications. All documents undergo an initialization step whereby raw text is extracted from each of the documents and stored under a unique index in an Elasticsearch database. Using this raw text, 5 metadata steps are also exectuted:

1. The purpose of the *Update Dropbox* pipeline is to fetch and construct a metadata description of all files in the *Place roles* Dropbox folder. The output is the `dropbox_sample_data_dir_cache.csv` file written to the `data` folder. These files have industry and job title labels and are used for the BERT model training.
2. The purpose of the *Update Zindi* pipeline is to fetch and construct a metadata description of all files in the *Zindi CVs* and *Zindi job specs* Dropbox folders. The output is the `zindi_sample_data_dir_cache.csv` file written to the `data` folder. These files have industry and job title labels and are used for the BERT model training. This metadata is what's used as input in the *Initialisation* pipline to fetch these files.
3. The purpose of the *Update FTP Server* pipeline is to fetch and construct a metadata description of all files on the JobCrystal staging server. The output is the `ftp_server_data_dir_cache.csv` file written to the `data` folder. This metadata is what's used as input in the *Initialisation* pipline to fetch these files.
4. The purpose of the *Update FTP server pipeline* is to fetch and construct a metadata description of all files on the JobCrystal staging server. The output is the `prod_server_data_dir_cache.csv` file written to the `data` folder. This metadata is what's used as input in the *Initialisation* pipline to fetch these files.
* The purpose of the *Update Job Title Metadata* pipeline is to construct the `job_to_labels.pkl` and `labels_to_job.pkl` files in the `data` folder. These are based on JocCrystal's *JobTitleAlternatives.csv file* where one job may be associated with many labels and a label may be associated with many job titles.
* The purpose of the *Update Entity Lists* pipeline is to construct a set of all named entities uncovered by the NER model (for each entity class) and output the `all_colleges.pkl`, `all_companies.pkl`, `all_degrees.pkl` and `all_skills.pkl` files. This metadata is what was used in the keyword filter dropdown lists (Step 4 in the UI) and now the `filter_options` GET request.
* The *Preprocess Doc2vec/UMAP/HDBSCAN* pipeline is to fit a UMAP reducer model to the document vector representations, as well as an HDBSCAN clustering model to the reduced embedding space. These models are necessary later on when doing the unsupervised ranking and thus need to be fitted on all of the data now.

### 3.2. Trained Models

* The *IndustryBERT* model is trained by calling `run(industry_label=True)` in the *text classification* script. But first you need to construct the BERT training data by running the *construct BERT training data* pipeline, again with `run(True)`.
* The *JobTitleBERT* models are trained by calling `run(industry_label=Flase, filter_industry=<industry name here>)` in the *text classification* script. You can also specify `filter_industry='job_title'` to predict over all possible job titles regardless of the industry. But first you need to construct the BERT training data by running the *construct BERT training data* pipeline, again with `run(industry_label=Flase, filter_industry=<industry name here>)`.
* The custom spaCy NER model were trained in Colab using this notebook. We train both a tok2vec and transformer spacy model. Since we are going to use Sovren though, these models and code will probably become obsolete.

### 3.3. Preprocess

All documents are then fed through various preprocessing steps, the outputs of which are each stored in separate indices in ElasticSearch:

1. During the *Process Resume Parts* pipeline, each sentence of a document is fed through [a BERT model](https://huggingface.co/manishiitg/distilbert-resume-parts-classify) that had been finetuned for classifying
texts according to which part of a resume this sentence likely comes from. All results are stored under the `resume_parts` index in ElasticSearch.
2. The *Process SentenceBERT* pipeline should save, for each sentence, a vector representation using the [SentenceTransformers](https://github.com/UKPLab/sentence-transformers) package.
3. The *Process Sovren* pipeline should wrap both the resume and job spec [Sovren APIs](https://www.sovren.com/technical-specs/latest/rest-api/overview/). As mentioned, we are going to be using [Sovren](https://www.sovren.com/) to do the NER matching. They have been around for 25 years and their software is waaaay more mature than we could ever hope to build. Ideally, this would replace our current NER pipeline.
4. During the *Process NERs* pipeline, named entities are extracted from a document using a custom spaCy tok2vec and transformer NER models trained on [this dataset](https://github.com/DataTurks-Engg/Entity-Recognition-In-Resumes-SpaCy). All results are stored under the `ner` index in ElasticSearch. The performance of these models are pretty poor. We try and hybridise these models with the [pyresparser](https://github.com/OmkarPathak/pyresparser) package which provides some more specialised regex. This is really the best we can do with off-the-shelf open-source resources... hence why Sovren will be a game changer.
5. During the *Process Industry* pipeline, our own BERT model that has been finetuned to classify text according to a job industries is applied to each sentence. All results are stored under the `industry` index in ElasticSearch.
6. During the *Process Job Title* pipeline, our own BERT model that has been finetuned to classify text according to a job titles is applied to each sentence. All results are stored under the `job_title` index in ElasticSearch.

### 3.4. Data Store

* We are using an Elasticsearch database to store all preprocessed document data and well as a handful of pickle files to store some metadata and models (ideally, all these pickle files will be done away with).
* We can instantiate an Elasticsearch database class to establish a connection to a database instance. The default configurations for this class can be changed in this yaml file.
* When testing, you can spin up a local instance of Elasticsearch. Otherwise, our live database is stored on this EC2 instance.

### 3.5. "AI Logic"

* The AI logic is where everything comes together to make a set of CV/job spec recommendations.
* It starts by selecting to match a resume/job to a set of jobs/resumes (step 1) and then the user uploading the resume/job (step 2).
* This document is then passed through the `init` and all the preprocessing pipelines.
* The magic now happens by querying all resumes/jobs that have the same predicted industry and job title confirmed by the user (step 3), as well as any keyword entities specified by the user (step 4), as the document uploaded by the user.
* We rank the document subset by the cosine similarity between the source and matched documents.
* We then perform a hybrid unsupervised ranking using the fitted UMAP and HDBSCAN models.
* [INCOMPLETE] Additionally, the "external" results of Hiretule can be appended with the "internal" documents uncovered by our system. The inputs to Hiretule are the named entities uncovered by Sovren, if Hiretule goes ahead...
* These final results are then returned to the user.

### 3.6. User Interface

Before constructing the flask API, we provided Sasha with a Streamlit Dashboard to test the solution. This is obsolete now as Zani is developing the UI on the JobCrystal side and we are only providing the API responses they need. Here is what the Streamlit Dashboard looked like:

{% include figure.html path="assets/img/blog/blog10.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 2: Solution architecture.</em> 
</div>

***

## 4. Productionising the solution

{% include figure.html path="assets/img/blog/blog10.4.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 3: Solution architecture.</em> 
</div>

{% include figure.html path="assets/img/blog/blog10.5.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 4: Solution architecture.</em> 
</div>


***


## 5. Project impact

{% include figure.html path="assets/img/blog/blog10.6.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 5: Client feedback.</em> 
</div>

<div class="col-sm mt-3 mt-md-0">
    {% include video.html path="assets/video/jobcrystal.mp4" class="img-fluid rounded z-depth-1" controls=true %}
</div>


## 6. Wrapping up

