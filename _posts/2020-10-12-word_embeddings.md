---
layout: distill
title: Word Embeddings
description: Natural Language Processing Series
date: 2020-10-12
tags: NLP

authors:
  - name: Shane van Heerden
    affiliations:
      name: Cape AI

bibliography: 2018-12-22-distill.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: 1. What are Word Embeddings?
  - name: 2. Word2Vec Methods
  - name: 3. Training a Word2Vec Model
  - name: 4. Word2Vec Tutorial
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

*This blog post was originally featured on the [Cape AI Medium page](https://medium.com/cape-ai-stories/natural-language-processing-series-8d51b87b6004).*

{% include figure.html path="assets/img/blog2.1.png" class="img-fluid rounded z-depth-1" zoomable=true %}

This is the second post in a series of blog posts focusing on the exciting field of Natural Language Processing (NLP)! In our previous blog post, we saw that word embeddings were an important milestone in the history of NLP. Although these were used as far back as 2001, in 2013 Mikolov et al. [1] proposed a simple but novel method for efficiently training word embeddings (or word2vec models) on very large unlabelled corpora which ultimately led to their wide-scale adoption. So, what actually are word embeddings and how do they fit into an NLP practitioner’s toolkit? Let’s find out!

## 1. What are Word Embeddings?

A word embedding is simply an alternative representation for text where words are represented as real-valued vectors as opposed to a sequence of string characters. This representation is “learnt” in such a way that words that share the same or similar meaning also have similar vector representations.

To get a feel for this concept, let’s look at Figure 1 which is a visual representation of a word’s vector, where red indicates a value close to 2 and blue a value close to -2. As we can see, the vectors for “man” and “woman” appear to be more similar in colour to each other than when compared to the word “king”. A ‘mystical’ property of word embeddings is that they often capture semantic meaning between words. This means that we can add and subtract word vectors and arrive at intuitive results. Probably the most famous of these is the vector resulting from taking the word “king”, subtracting the word “man” and adding the word “woman”. The resultant vector is visually shown in Figure 1 and, if we compare this against all 400 000 words that make up the embedding vocabulary, we find that the vector for the word “queen” is in fact the closest to this resultant vector!

{% include figure.html path="assets/img/blog2.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}

***

## 2. Word2Vec Methods

Now that we have a bit of an intuitive feel of word embeddings, let’s get a better grasp on the theory surrounding word embeddings. In their paper, Mikolov et al. proposed two methods for creating word embeddings, namely the Continuous Bag-of-Words (CBOW) and Skip-gram method, as shown in Figure 2. In the CBOW method, the current word w(t) is predicted based on the context of the surrounding words. The Skip-gram method does the exact opposite by attempting to predict the surrounding words given the current word w(t).

{% include figure.html path="assets/img/blog2.3.png" class="img-fluid rounded z-depth-1" zoomable=true %}

Let’s expand on this a bit more by diving into how one would go about constructing a dataset from an unlabelled corpus of text to leverage each of these two methods. For a more in-depth explanation of these concepts, we highly recommend reading Jay Alammar’s blog post on this subject which inspired this section.

### 2.1. Continuous Bag-of-Words

As a way of example, suppose we had a large text corpus which contained the sentence: “Thou shalt not make a machine in the likeness of a human mind”. In order to create a dataset from this, the CBOW method, in essence, slides a context window of a fixed word size (let’s say three in this case) over the sentence, as illustrated in Figure 3. In this case, we take the first two words in the window as input features and the last word to be the output label. We repeat this process of constructing the dataset by moving the window one word on until the end of the corpus has been reached.

{% include figure.html path="assets/img/blog2.4.png" class="img-fluid rounded z-depth-1" zoomable=true %}

### 2.2. Skip-gram

Continuing with the previous example, the Skip-gram model constructs a dataset in a slightly different way. Instead of just considering the n previous words when trying to predict a target word, we now consider predicting all the words within a specific context window surrounding a specific word. A context window could be, say, two words before and after the word being considered. This is illustrated in Figure 4. In this case, the centre word in the window is the input word and a target word is created for each surrounding word. Again, we repeat this window-sliding process of constructing the dataset until the end of the corpus has been reached.

{% include figure.html path="assets/img/blog2.5.png" class="img-fluid rounded z-depth-1" zoomable=true %}

In reality, training a model on this form of the dataset is computationally expensive. This is due to the fact that we have to compute the error of the neural language model against the whole output vocabulary vector for every training sample! In practice, a technique called negative sampling is employed. This involves transforming the dataset shown in Figure 4 to a new form shown in Figure 5. In this procedure, the input and output words are now both features and a new target column is added, where a value of ‘1’ indicates that the two words are neighbours.

In other words, the task has now changed from predicting the neighbour of a word to predicting if two words are neighbours.

In order to ensure that not all samples have a target variable of one, we introduce a few negative samples (hence the method’s name) to the dataset by sampling a set of random words from our vocabulary and setting the corresponding target label to zero.

{% include figure.html path="assets/img/blog2.6.png" class="img-fluid rounded z-depth-1" zoomable=true %}

***

## 3. Training a Word2Vec Model

Alright, quick checkpoint: CBOW and Skip-gram with negative sampling are methods used to create/train word embeddings and not for training a word2vec model. Let us now dive into the process of training a word2vec model.

At the beginning, we initialise each word in our dataset as a vector (with some specified length) of random values. In each training step, we look at just a single positive sample with its associated negative samples, as shown in Figure 6. If we compute the dot product between the input and output word vectors for each sample and squash this value through a sigmoid function (so that all values are between zero and one), we obtain a specific score. Now, we know that in the case of the positive sample the resultant sigmoid score should be close to one (because the two words are indeed neighbours) and the others should be close to zero. We can therefore compute an error by subtracting this sigmoid score from the target labels.

Here comes the “learning” part: we can now use this error to adjust the values of each word vector in such a way so that we compute a lower error score the next time. We continue this process for all word vectors in our dataset, incrementally adjusting their values to achieve slightly better embeddings until the learning algorithm (which is usually a neural network) converges.

{% include figure.html path="assets/img/blog2.7.png" class="img-fluid rounded z-depth-1" zoomable=true %}

***

## 4. Word2Vec Tutorial

Time to code!

Okay, enough with the theory. Let’s get a bit more practical by showing you how to implement this in the real world using Python. In this tutorial, we are going to walk you through how to construct your own text corpus and how to train your very own word2vec model.

### 4.1. Package Installations and Imports

First things first, we need to install and import a few packages. We will begin by installing the beautifulsoup4 package which will be used for cleaning up scraped data from Wikipedia, together with the lxml package to parse HTML content from Wikipedia pages. Additionally, we will also need the nltk and gensim packages to do most of the NLP heavy-lifting for us.

Go ahead and run:

{% highlight bash %}

$ pip install beautifulsoup4
$ pip install lxml
$ pip install nltk
$ pip install gensim

{% endhighlight %}

### 4.2. Creating a Corpus

In practice, word2vec models are commonly trained on millions of words. For illustration purposes, however, in this tutorial, we are just going to scrape a small corpus of text from the Wikipedia article on Machine Learning to train our model.

The following Python script does this for us:

{% highlight python %}

import bs4 as bs
import urllib.request
import re

# Scrape text from Wikipedia
scrapped_html = urllib.request.urlopen('https://en.wikipedia.org/wiki/Machine_learning')
raw_html = scrapped_html.read()  # Parse all paragraphs
parsed_html = bs.BeautifulSoup(raw_html, 'lxml')
paragraphs = parsed_html.find_all('p')
text = " ".join([p.text for p in paragraphs])

{% endhighlight %}

In the script above, we first scrape and read all html data from the Wikipedia page using the urlopen method from the requests package. We then parse the raw html into a BeautifulSoup object and extract all text contained within p (paragraph) tags using the find_all method. Finally, we combine all of the paragraphs into a single text string.

### 4.3. Text Pre-Processing

The next step involves pre-processing the text so that it is in the correct format for training the Gensim Word2Vec model:

{% highlight python %}

import nltk
from nltk.corpus import stopwords

# Clean the text
clean_text = text.lower()
clean_text = re.sub('[^a-zA-Z]', ' ', clean_text)
clean_text = re.sub(r'\s+', ' ', clean_text)

# Prepare the dataset
sentences = nltk.sent_tokenize(clean_text)
words = [nltk.word_tokenize(s) for s in sentences]

# Remove any stopwords
for i in range(len(words)):
    words[i] = [w for w in words[i] if w not in stopwords.words('english')]

{% endhighlight %}

In the script above, we clean the text by first converting all characters to their lowercase form and then removing any digits, special characters and trailing spaces that may exist in the text. Next, we prepare the dataset into a collection of words so that it can be used to train Gensim’s Word2Vec model. We first split our clean_text into individual sentences using nltk’s sent_tokenize method and then further into individual words using the word_tokenize method. Finally, we cycle through our collection of words and remove any which are in nltk’s predefined list of English stopwords (or common words like “the”, “and”, “to”, etc).

### 4.4. Creating a Word2Vec Model

With this, we can now easily train our own Word2Vec model as follows:

{% highlight python %}

from gensim.models import Word2Vec

# Create the model
word2vec = Word2Vec(words, sg=1, negative=5, min_count=2)

# Print vocabulary of model
vocab = word2vec.wv.vocabprint(vocab.keys())

{% endhighlight %}

Here, we simply instantiate a Gensim Word2Vec object using our collection of words. In this instantiation, we specify a value of 1 for the sg parameter and a value of 5 for the negative parameter — telling Gensim to utilise the skip-gram model and employ 5 negative samples respectively. We also specify a value of 2 for the min_count parameter so that the model only includes words that have appeared at least twice in our corpus. We can view a list of all of the unique words of the model by printing out the keys of the wv.vocab attribute.

### 4.5. Exploring the Model

Congratulations! You now have your very own word2vec model trained on your own corpus of text. Let’s explore your creation a bit further.

We know that the word2vec model is used to map all words to a vector representation. We can view the vector corresponding to any word in our model’s vocabulary as follows:

{% highlight python %}

# Print vector of word
print(word2vec.wv['learning'])

>> [-0.0321747  -0.09254234  0.15719296 -0.18106991 -0.05903807  0.06826855 -0.0546078  -0.14852802  0.00465734  0.00357654 -0.11191269  0.00123405 -0.00260608 -0.01252941  0.03632444  0.05868229 -0.01147301  0.01271282 0.20245183 -0.08651368 -0.02063143  0.10166611  0.05482733 -0.06414133 -0.09078009  0.03067572 -0.06758043  0.00037985  0.10958873  0.00133575 -0.17905715 -0.20932317 -0.08205332 -0.02681177 -0.04361923  0.06187554 -0.03218165 -0.12924905  0.02013807  0.06686062  0.0272868  -0.02437109 0.00260349 -0.02714068  0.02746866  0.05804974  0.08129382 -0.03174321 0.08987447 -0.05901277  0.07368492  0.04791974  0.08164269 -0.05956567 -0.12904255 -0.07182135 -0.0183635   0.0681318   0.09037064  0.03774516 0.12213644  0.1648079  -0.18914327  0.02890724 -0.02372251 -0.11012954 -0.1020454  -0.01853278 -0.05396225 -0.00242959 -0.10753106 -0.07141761 0.10347419 -0.10766012 -0.16925317 -0.10747337 -0.08550954  0.01928806 0.2854533  -0.12928957  0.05894407  0.05380363  0.06413457 -0.18124926 0.06807419 -0.12306273 -0.16328035  0.14293176 -0.23447195 -0.07432053 -0.1858378   0.07563769  0.06148988  0.10675731 -0.03910929 -0.00869188 -0.05734098  0.07828433  0.04085582  0.0544676]

{% endhighlight %}

From these outputs, we can see that Gensim, by default, maps all words to a one hundred dimensional numpy vector. As we have seen, one of the benefits of using word2vec is that the model is able to capture the semantic meaning of words in relation to one another. Let’s try and confirm this by printing out the words most similar (in vector space) to the word “learning”:

{% highlight python %}

# Print most similar words
print(word2vec.wv.most_similar('learning'))

>>  [('machine', 0.9990278482437134),('data', 0.9989383220672607),
('model', 0.9988012313842773),('training', 0.9987066984176636), ('biases', 0.9984654784202576),('set', 0.9983999729156494),('classification', 0.9983198046684265),('used', 0.9982497692108154),('use', 0.9981957674026489),('systems', 0.9980695247650146)]

{% endhighlight %}

The list above shows us the words most similar to the word “learning” and displays their corresponding cosine similarities. No surprise, the word “machine” appears at the top of our list. We can also see words such as “data,” “model” and “training” also appearing near the top which means they often occur with the word “learning” — this makes sense. We should therefore be quite confident that our model has indeed successfully managed to capture some sort of semantic relationship between words using just a single Wikipedia page!

***

## 5. Wrapping up

And there you have it! In this blog post, we described the two flavours of word2vec models, namely CBOW and Skip-gram as well as the model training procedure. Armed with this information, we got a bit more practical by walking you through how to create your very own word2vec model in Python using the Gensim library.

Stay tuned to this series to learn more about the awesome world of NLP as we share more on the latest developments, code implementations and thought-provoking perspectives on NLP’s impact on the way we interact with the world. It’s an extremely exciting time for anyone to get into the world of NLP!

***
