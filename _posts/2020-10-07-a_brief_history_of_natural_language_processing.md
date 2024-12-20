---
layout: distill
title: 🕰️ A Brief History of Natural Language Processing
description: Part 1 - Natural Language Processing Series
date: 2020-10-07
tags: NLP
giscus_comments: true
thumbnail: assets/img/blog/blog1.1.png

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
  - name: 1. Neural Language Models (2001)
  - name: 2. Multi-task Learning (2008)
  - name: 3. Word Embeddings (2013)
  - name: 4. Neural Networks for NLP (2013)
  - name: 5. Sequence-to-Sequence Models (2014)
  - name: 6. Attention Mechanisms (2015)
  - name: 7. Pre-trained Language Models (2018)
  - name: 8. Where we are today and looking forward…

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

*This blog post was originally featured on the [Cape AI Medium page](https://medium.com/cape-ai-stories/natural-language-processing-series-8d51b87b6004).*

{% include figure.html path="assets/img/blog/blog1.1.png" class="img-fluid rounded z-depth-1" zoomable=true %}

This is the first blog post in a series focusing on the wonderful world of Natural Language Processing (NLP)! In this post I'll present you with the lay of the land — describing seven important milestones which have been achieved in the NLP field over the last 20 years. This is largely inspired by [Sebastian Ruder's talk](https://www.youtube.com/watch?v=sGVi4gb90zk) at the 2018 Deep Learning Indaba which [I attended](https://shanevanheerden.github.io/news/news11/) in Stellenbosch.

Short disclaimer before I begin: This post is heavily skewed towards neural network-based advancements. Many of these milestones, however, were built on many influential ideas presented by non-neural network-based work during the same era, which, for brevity purposes, have been omitted from this post.

## 1. Neural Language Models (2001)

It's 2001 and the field of NLP is quite nascent. Academics all around the world are beginning to think more about how language could be modelled. After a lot of research, Neural Language models are born. Language modelling is simply the task of determining the probability of the next word (often referred to as a *token*) occurring in a piece of text given all the previous words. Traditional approaches for tackling this problem were based on n-gram models in combination with some sort of smoothing technique<d-cite key="Kneser1995"></d-cite>. Bengio *et al.*<d-cite key="Bengio2000"></d-cite> were the first to propose using a feed-forward neural network, a so-called word "lookup-table", for representing the *n* previous words in a sequence as illustrated in Figure 1. Today, this is known as *word embeddings*.

{% include figure.html path="assets/img/blog/blog1.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 1:</em> The first feed-forward neural network used for language modelling<d-cite key="Kneser1995"></d-cite>.
</div>

***

## 2. Multi-task Learning (2008)

Excitement and interest grows steadily in the years following Neural Language models. Advances in computer hardware allow researchers to push the boundaries on language modelling, giving rise to new NLP methods. One such method is multi-task learning. The notion of multi-task learning involves training models to solve more than one learning task, while also using a set of shared parameters. As a result, models are forced to learn a representation that exploits the commonalities and differences across all tasks.

Collobert and Weston<d-cite key="Collobert2008"></d-cite> were the first to apply a form of multi-task learning in the NLP domain back in 2008. They trained two convolutional models with max pooling to perform both part-of-speech and named entity recognition tagging, while also sharing a common word lookup table (or word embedding), as shown in Figure 2. Years later, their paper was highlighted by many experts as a fundamental milestone in deep learning for NLP and received the Test-of-time Award at the 2018 *International Conference on Machine Learning* (ICML).

{% include figure.html path="assets/img/blog/blog1.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 2:</em> The first multi-task model sharing a common word lookup table<d-cite key="Collobert2008"></d-cite>.
</div>

***

## 3. Word Embeddings (2013)

If you've had any exposure to NLP, the first thing you have probably come across is the idea of word embeddings (or more commonly known as *word2vec*). Although we have seen that word embeddings have been used as far back as 2001, in 2013 Mikolov *et al.*<d-cite key="Mikolov2013"></d-cite> proposed a simple but novel method for efficiently training these word embeddings on very large unlabeled corpora which ultimately led to their wide-scale adoption.

Word embeddings attempt to create a dense vector representation of text, and addresses many challenges faced with using traditional sparse bag-of-words representation. Word embeddings were shown to capture every intuitive relationship between words such as gender, verb tense and country capital, as illustrated in Figure 3.

{% include figure.html path="assets/img/blog/blog1.4.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 3:</em> The intuitive relationships captured by word embeddings<d-cite key="Mikolov2013"></d-cite>.
</div>

***

## 4. Neural Networks for NLP (2013)

Looking back, 2013 appeared to be an inflection point in the NLP field, as research and development grew exponentially thereon. The advancements in word embeddings ultimately sparked the wider application of neural networks in NLP. The key challenge that needed to be addressed was architecturally allowing sequences of variable lengths to be inputted into the neural net which ultimately lead to three architectures emerging, namely: *recurrent neural networks* (RNNs) (which were soon replaced by *long-short term memory* (LSTM) networks), *convolutional neural networks* (CNNs), and recursive neural networks. Today, these neural network architectures have produced exceptional results and are widely used for many NLP applications.

***

## 5. Sequence-to-Sequence Models (2014)

Soon after the emergence of RNNs and CNNs for language modelling, Sutskever *et al.*<d-cite key="Sutskever2014"></d-cite> were the first to propose a general framework for mapping one sequence to another, which is now known as *sequence-to-sequence* models. In this framework, an encoder network processes an input sequence token by token and compresses it into a vector representation, represented by the blue layers in Figure 4. A decoder network (represented by the red layers) is then used to predict a new sequence of output tokens based on the encoder state, which takes every previously predicted token as input.

{% include figure.html path="assets/img/blog/blog1.5.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 4:</em> A visual representation of a sequence-to-sequence model used for translation<d-cite key="See2016"></d-cite>.
</div>

This architecture is particularly useful in tasks such as *machine translation* (MT) and *natural language generation* (NLG). It's no surprise that, in 2016, Google announced that it is in the process of replacing all of its statistical-based MT systems with neural MT models<d-cite key="Wu2016"></d-cite>. Additionally, since the decoder model can be conditioned on any arbitrary representation, it can also be used for tasks like generating captions for images<d-cite key="Vinyals2015"></d-cite>.

***

## 6. Attention Mechanisms (2015)

Although useful in a wide range of tasks, sequence-to-sequence models were struggling with being able to capture long-range dependencies between words in text. In 2015, the concept of attention was introduced by Bahdanau *et al.*<d-cite key="Bahdanau2015"></d-cite> as a way of addressing this bottleneck. In essence, attention in a neural network is a mechanism for deciding which parts of the input sequence to attend to when routing information. Various attention mechanisms have also been applied in the computer vision space for image captioning<d-cite key="Xu2015"></d-cite>, which also provides a glimpse into the inner workings of the model, as is seen in Figure 5.

{% include figure.html path="assets/img/blog/blog1.6.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 5:</em> A visual representation of the attention mechanism in an image captioning model<d-cite key="Xu2015"></d-cite>.
</div>

Attention is not only restricted to the input sequence and can also be used to focus on surrounding words in a body of text — commonly referred to as *self attention* — to obtain more contextual meaning. This is at the heart of the current state-of-the-art *transformer* architecture, proposed by Vaswani *et al.*<d-cite key="Vaswani2017"></d-cite> in 2017, which is composed of multiple self-attention layers. The transformer sparked an explosion of new language model architectures (and an inside joke among AI practitioners regarding Sesame Street Muppets), the most notable being *Bidirectional Encoder Representations from Transformers* (BERT) and *Generative Pre-trained Transformers* (GPT).

{% include figure.html path="assets/img/blog/blog1.7.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 6:</em> The various language model architectures based on the transformer model<d-cite key="Wang2019"></d-cite>.
</div>

## 7. Pre-trained Language Models (2018)

Dai & Le<d-cite key="Dai2015"></d-cite>. were the first to propose using pre-trained language models in 2015 but this notion was only recently shown to be beneficial across a broad range of NLP-related tasks. More specifically, it was shown that pre-trained language models could be *fine-tuned* on other data related to a specific target task<d-cite key="Ramachandran2017, Howard2018"></d-cite>. Additionally, language model embeddings could also be used as features in a target model leading to significant improvements over the then state-of-the-art models<d-cite key="Peters2018"></d-cite>, as shown in Figure 7.

{% include figure.html path="assets/img/blog/blog1.8.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    <em>Figure 7:</em> Improvements over state-of-the-art models when employing language model embeddings<d-cite key="Peters2018"></d-cite>.
</div>

***

## 8. Where we are today and looking forward…

Nowadays, there exists an array of initiatives aimed at open-sourcing many large state-of-the-art pre-trained models. These models can be fine-tuned to perform various NLP-related tasks like *sequence classification*, *extractive question answering*, *named entity recognition* and *text summarization* (to name a few). NLP is advancing at an incredible pace and is giving rise to global communities dedicated to solving the world's most important problems through language understanding.

Stay tuned to this series to learn more about the awesome world of NLP as I share more on the latest developments, code implementations and thought-provoking perspectives on NLP's impact on the way we interact with the world. It's an extremely exciting time for anyone to get into the world of NLP!
