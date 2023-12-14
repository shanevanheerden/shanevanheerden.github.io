---
layout: distill
title: 📄 Matching Resumes to Job Specifications
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

In the fast-paced world of job recruitment, staying ahead of the curve requires innovative solutions. Recently, I was tasked with probably my toughest challenges by a client who operates a job recruitment firm within South Africa. I undertook the challenge of revolutionizing JobCrystal's current recruitment process by harnessing the power of advanced Natural Language Processing (NLP) techniques.

In this blog post, I'll take you through the intricacies of the project, detailing the architecture, preprocessing pipelines, data storage, APIs, trained models, user interface, and the intricate AI logic that powers the system. 

***

## 2. Understanding the clients needs

Understanding the client's needs was the crucial first step in the journey. The client's existing recruitment process was time-consuming and inefficient, relying heavily on its recruiters to manually review each resume and match it to relevant job openings. This resulted in slower placement times and potential missed opportunities for both job seekers and employers.

To address these challenges, the client sought a solution that could not only streamline their current processes by automating the matching of resumes to job specifications but also bring innovation to the forefront of their operations. They wanted a system that could accurately identify relevant resumes for each open position (and vice versa), eliminating the need for manual screening.

Before developing the solution, I spent time understanding the specific needs of the client. I met with the company's recruiters to understand their key pain points and how they currently matched resumes to job openings. I also analyzed the company's data to identify any patterns or trends that could be used to improve the matching process. Through this collaborative process, I gained crucial insights into the intricacies of their current recruitment workflow, enabling myself to tailor my solution to their specific needs. As a result, I developed a set of requirements for the proposed solution:

- **Automated**: The solution should be able to automatically match resumes to job openings (and vice versa) without any manual intervention.
- **Accurate**: The solution should be able to accurately match resumes to job openings (and vice versa) based on the skills, experience, and qualifications of the candidates.
- **Scalable**: The solution should be able to handle a large volume of resumes and job openings.
- **Easy to use**: The solution should be easy to use for JobCrystal's recruiters.

***

## 3. The solution architecture

The success of the project relied heavily on creating a well-designed solution architecture. The system's core comprises multiple components collaborating to provide a strong and intelligent recruitment solution. Each stage, from data ingestion to storing results in ElasticSearch, was crafted with emphasis on scalability, efficiency, and smooth integration into the client's current infrastructure. Figure 1 visually depicts the interconnected components, showcasing the architecture of the solution.

{% include figure.html path="assets/img/blog/blog10.2.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 1: The architecture of the proposed solution.</em> 
</div>

### 3.1. Data initialisation

To kickstart the process, the system ingests data from two primary sources: The client's FTP server and Dropbox account. The FTP server contains approximately 35 000 unlabelled documents, while Dropbox contributes around 3 000 labeled resumes associated with job specifications. This data undergoes an initialization step, whereby raw text is extracted from the documents and stored as separate sentences in an Elasticsearch database. Additionally, five metadata extraction steps are executed at this stage which encompass updating information related to file locations, industry and job title labels, and constructing sets of named entities.

### 3.2. Training the models

The system utilized various trained models to perform specific tasks associated with document preprocessing and unsupervised ranking. Two *Bidirectional Encoder Representations from Transformers* (BERT) models are finetuned in order to predict, given the raw resume text, a candidate's industry and associated job title, giving rise to the *IndustryBERT* and *JobTitleBERT* models, respectively. The fine-tuned models ensure accurate categorization, forming the foundation for subsequent stages in the process.

A transformer-based spaCy *Named Entity Recognition* (NER) model is also trained at this stage to extract key information like key skills, location, degrees and college names which may be used for downstream filtering. Additionally, *Uniform Manifold Approximation and Projection* (UMAP) and *Hierarchical Density-Based Spatial Clustering of Applications with Noise* (HDBSCAN) models are fitted to facilitate document vector representations which is an essential component in the subsequent unsupervised ranking step.

### 3.3. Preprocessing the data

All sentences comprising the raw resume text are then fed through various preprocessing steps, the outputs of which are each stored in separate indices in Elasticsearch. During this phase, the finetuned industry and job title BERT models as well as the custom NER model are leveraged, together with two pre-trained BERT-based models. More specifically:

- Each resume sentence is classified according to the most likely industry and associated job title using the IndustryBERT and JobTitleBERT models.
- Resume-specific named entities are extracted from the resume using the custom spaCy NER model.
- Each resume sentence is converted to a vector representation using the well-known [SentenceTransformers](https://github.com/UKPLab/sentence-transformers) package.
- Each resume sentence is classified according to what part of a resume the sentence likely describes (e.g. experience, education, skills, certifications, awards, hobbies, references, etc.) using [a pretrained model](https://huggingface.co/manishiitg/distilbert-resume-parts-classify).

### 3.4. Data storage

Elasticsearch served as the central repository for storing preprocessed document data. Furthermore, the ability to execute database queries according to the [cosine similarity measure](https://en.wikipedia.org/wiki/Cosine_similarity) makes it an extremely efficient database for enabling efficient document retrieval. A dedicated AWS EC2 instance is used to host the Elasticsearch instance.

### 3.5. The "AI logic"

The AI logic is the heart of the system, orchestrating the matchmaking process. By considering predicted industry and job titles, user-specified keyword entities, and leveraging unsupervised ranking techniques, our system intelligently filters and ranks documents, providing personalized recommendations to users. This is accomplished through 5 sequential steps facilitated by a [Streamlit](https://streamlit.io/) user interface dispalyed in Figure 2.

{% include figure.html path="assets/img/blog/blog10.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 2: The graphic user interface for the resume-job matching solution.</em> 
</div>

- **Step 1.** The first step requires the user to select whether they are wanting to match a resume to a job specification.
- **Step 2.** The user may then upload the resume to the system in their PDF or DOCX format. Uploading a resume triggers the initialisation and prepocessing procedures described in §3.1 and §3.3, the results of which are also stored in the Elasticsearch database. During this step, the industry and corresponding job title of the candidate are predicted using the IndustryBERT and JobTitleBERT models desribed in §3.2, respectively.
- **Step 3.** The predicted industry and corresponding job title of the candidate are then surfaced to the user as a confirmation. If an errorous prediction was made by the model, the user has the opportunity to correct the classification at this stage. This step is important since the candidate's suitability will only be assessed according to those job specifications matching the candidate's industry and job title.
- **Step 4** In some cases, the user may wish to further filter out the suitability of the candidate to a job specification unless specific entities are not present in both documents. More specifically, the user may specify in this step that specific skills, degrees, colleges and/or histroic company names must be present in both documents. This is accomplished by utilising our custom spaCy NER model described in §3.2.
- **Step 5**: The combination of all information obtained in Steps 1-4 culminate in this final step. All job specifications which assume the same industry and job titles as the resume confirmed by the user in Step 3, as well as any entities specified by the user in Step 4, are queried from the Elasticsearch database as potential candidates. All candidate job specifications then undergo an unsupervised ranking procedure according to the similarity to the uploaded resume. Two unsupervised ranking algorithms where proposed and evaluated according to preference-based assessments by a job recruitment expert:
    - The first algorithm relies on ordering all candidate job specifications by utilising Elasticsearch's [text similarity functionality](https://www.elastic.co/search-labs/blog/articles/text-similarity-search-with-vectors-in-elasticsearch). More specifically, job specifications are ordered according to the [cosign similarity](https://en.wikipedia.org/wiki/Cosine_similarity) between the text vector representations of the job specifications according to the resume's vector representation.
    - The second algorithm

### 3.6. User interface

The user interface provided a user-friendly platform for interacting with the system. Users could upload resumes or job specifications, specify keyword entities, and receive personalized recommendations.

Initially, a Streamlit Dashboard provided a user-friendly interface for testing. However, the focus has shifted to integrating the solution seamlessly into the JobCrystal UI through the development of a Flask API. This ensures that the end user, in this case, JobCrystal's team, can easily access and benefit from the system's recommendations.

The user interface, initially developed as a Streamlit Dashboard for testing, serves as a bridge between the user and the system. Although the Streamlit Dashboard is now obsolete as UI development shifts to the JobCrystal side, it provided a visual representation of the system's capabilities.

While the technical backend is fascinating, the user interface is where the magic becomes tangible. Though initially using a Streamlit Dashboard for testing, our current focus is on delivering seamless API responses for integration into JobCrystal's evolving user interface.

Initially, a Streamlit Dashboard served as a testing interface, providing insights into system functionality. However, with Zani spearheading the UI development on the JobCrystal side, our focus shifted to delivering streamlined API responses to meet their evolving requirements.

Before constructing the flask API, we provided Sasha with a Streamlit Dashboard to test the solution. This is obsolete now as Zani is developing the UI on the JobCrystal side and we are only providing the API responses they need. Here is what the Streamlit Dashboard looked like:



***

## 4. Productionising the solution

The system was deployed on AWS EC2 instances and scaled to handle the volume of resumes and job specifications processed daily. We implemented continuous integration (CI) and continuous delivery (CD) pipelines to ensure that the system was always up-to-date and running smoothly.

To deploy the solution into production, we followed these steps:

Infrastructure Setup: An AWS EC2 instance was provisioned to host the system components.

Deployment: The system code was deployed to the EC2 instance using a continuous integration/continuous delivery (CI/CD) pipeline.

Monitoring: Monitoring tools were implemented to track system performance and resource utilization.

API Integration: The system's API was integrated with JobCrystal's existing platform to seamlessly incorporate the new functionality.

To ensure the solution's scalability and reliability, we deployed it on an Amazon Web Services (AWS) EC2 instance. This cloud-based infrastructure provides the necessary resources to handle large volumes of data and user requests.

The solution was implemented using a variety of technologies, including Python, Elasticsearch, and Streamlit. The solution is deployed on an Amazon Web Services (AWS) cloud infrastructure.

The productionization phase was a critical step in transitioning from development to a fully operational system. Figure 3 and Figure 4 provide snapshots of this process. They showcase the deployment and scaling aspects, emphasizing the robustness and scalability of our solution in a production environment.

The productionization phase involved scaling our solution for real-world deployment. Figures 3 and 4 illustrate the architecture's adaptation for live deployment, ensuring optimal performance, reliability, and accessibility.

With the successful development and testing phases behind us, the focus shifted to productionizing the solution for seamless integration into JobCrystal's operations. This stage involved deploying the entire system on AWS EC2 instances to ensure scalability, reliability, and optimal performance. The architecture was fine-tuned to handle real-time data processing, and necessary measures were implemented to monitor and maintain the system efficiently.

The true measure of success lies in the impact on JobCrystal's operations. Client feedback and testimonials serve as a testament to the positive transformations witnessed.

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

The implementation of this NLP-powered system significantly improved JobCrystal's recruitment process:

Reduced Manual Screening: The system automatically matched resumes to job specifications, reducing the time recruiters spent on manual screening.

Improved Candidate Matching: The system's AI-powered matching capabilities ensured that the most relevant candidates were identified for each open position.

Enhanced Recruitment Efficiency: The system streamlined the recruitment process, saving time and resources for the company.

The implementation of the NLP-powered recruitment system resulted in significant improvements for JobCrystal:

Reduced Matching Time: The automated matching process significantly reduced the time required to match resumes to job specifications, allowing recruiters to focus on more strategic tasks.

Improved Accuracy: The NLP-based matching algorithm improved the accuracy of matching, leading to better placements and a more efficient recruitment process.

Enhanced Candidate Experience: Job seekers benefited from a more personalized and relevant search experience,

The implementation of our NLP-powered solution has significantly transformed JobCrystal's recruitment process. The system has:

Reduced the time required to match resumes to job specifications by over 90%.

Improved the accuracy of matches, leading to a higher percentage of qualified candidates being placed in suitable roles.

Enhanced the overall efficiency of JobCrystal's recruitment operations.

The impact of our project extends beyond the technical aspects. Client feedback, as depicted in Figure 5, highlights the positive outcomes and improvements observed by JobCrystal. The accompanying video offers a firsthand look at the project's impact, providing insights into the transformative changes experienced by the client.

The impact of our project on JobCrystal's recruitment process has been substantial. Client feedback reflects the positive outcomes achieved through the implementation of advanced NLP techniques, streamlining their workflow and enhancing the overall efficiency of their recruitment process.

The impact of our NLP-driven solution on JobCrystal's recruitment process has been profound. Client feedback reflects significant improvements in candidate matching accuracy, reduced manual effort in resume screening, and overall time savings in the hiring workflow. The streamlined process has empowered JobCrystal to make data-driven decisions and focus more on engaging with qualified candidates.

The impact of our solution on JobCrystal's recruitment process has been substantial. Figure 5 showcases positive client feedback, emphasizing the tangible benefits experienced. The accompanying video provides a firsthand look at the transformative impact our solution has had on streamlining recruitment operations.

{% include figure.html path="assets/img/blog/blog10.6.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 5: Client feedback.</em> 
</div>

To provide a more immersive understanding of the project's impact, we've compiled a video showcasing JobCrystal's experience and the positive outcomes of our collaboration.

<div class="col-sm mt-3 mt-md-0">
    {% include video.html path="assets/video/jobcrystal.mp4" class="img-fluid rounded z-depth-1" controls=true %}
</div>


## 6. Wrapping up

This project demonstrates the power of NLP in revolutionizing traditional recruitment processes. By leveraging advanced NLP techniques, we were able to create a system that significantly improved JobCrystal's efficiency and effectiveness in finding the right candidates for their open positions. As NLP continues to evolve, we can expect even more innovative applications in the field of recruitment, further transforming the way companies find and hire top talent.

This project has been a testament to the power of NLP in revolutionizing traditional industries like job recruitment. By leveraging advanced NLP techniques, we have significantly improved JobCrystal's ability to connect qualified candidates with suitable job openings, streamlining their recruitment process and enhancing their overall efficiency.

As we conclude this journey, it's essential to reflect on the challenges overcome, the innovations introduced, and the positive impact realized. Our collaboration with JobCrystal exemplifies the power of advanced NLP techniques in revolutionizing traditional processes. The project's success underscores the importance of understanding client needs, designing robust architectures, and continuously refining solutions for maximum impact.

In conclusion, our project for JobCrystal represents a significant leap forward in the realm of job recruitment. By harnessing the power of advanced NLP techniques, we've not only addressed specific challenges but also paved the way for a more efficient, data-driven, and user-friendly recruitment process. As we reflect on the journey, it's evident that the fusion of technology and recruitment has the potential to reshape industry standards, opening doors to new possibilities and innovations.

As we conclude this journey, we reflect on the challenges overcome, the lessons learned, and the positive impact our solution has had on JobCrystal's recruitment process. The collaboration with the client and the continuous evolution of our system mark the beginning of a new era in innovative job recruitment solutions.

In conclusion, our journey with JobCrystal represents a successful collaboration in revolutionizing their recruitment processes. By leveraging advanced NLP techniques, we've not only met but exceeded expectations, paving the way for a more efficient, data-driven, and intelligent recruitment ecosystem. As we reflect on the challenges overcome and the positive outcomes achieved, we look forward to continued innovation and impact in the evolving landscape of job recruitment.

As we conclude this journey with JobCrystal, we reflect on the transformation achieved in the realm of job recruitment. The integration of advanced NLP techniques, streamlined preprocessing pipelines, and a user-friendly interface has not only met but exceeded the client's expectations. The success of this project serves as a testament to the power of innovation in solving real-world challenges within the job recruitment landscape. We remain committed to pushing the boundaries of technology to drive positive change in diverse industries.
