---
layout: distill
title: Neural Language Models
description: Natural Language Processing Series
date: 2020-11-12

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
  - name: What exactly is a language model?
  - name: Building our own Neural Language Model
  - name: Wrapping up

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

{% include figure.html path="assets/img/blog3.1.png" class="img-fluid rounded z-depth-1" zoomable=true %}

This is the third blog post in our series of blog posts focusing on the exciting field of Natural Language Processing! In our first post, we saw that the application of neural networks for building language models was a major turning point in the NLP timeline, and in our second post we explored the significance of Word Embeddings in advancing the field. With this, we’re now ready to build our own language model!

## What exactly is a language model?

In its most simple form:

The task of a language model is simply to predict the probability of the next word appearing in a sequence of text given the previous words that have occurred.

Traditionally, this problem was tackled with Statistical Language Models which primarily consisted of using so-called n-gram models in combination with some sort of smoothing technique [1]. The big pivot in the way researchers thought about this problem occurred when Bengio et al. [2] proposed using a feed-forward neural network together with a word “lookup-table” for representing the n previous words (often referred to as a token) in a sequence, as shown in Figure 1. Today, this “lookup-table” is known as a word embedding which you may already be familiar with if you read our second blog post. And thus the Neural Language Model was born!

{% include figure.html path="assets/img/blog3.2.png" class="img-fluid rounded z-depth-1" zoomable=true %}

***

## Building our own Neural Language Model

We’re going to keep things very practical in this post by jumping straight into a coding example! In this example, we are going to walk you through:

1. How you can prepare a document of input text for developing a word-based language model,
2. How you can design and implement your very own neural language model, and
3. How you can use this model to generate some new text with a similar tone to the input text.

Let’s get started!

### Package installations

We only need to install two packages for this tutorial: good-ol’ numpy and keras (which will do most of the deep learning heavy-lifting for us). Go ahead and run the following in your terminal:

{% highlight bash linenos %}

$ pip install numpy
$ pip install keras

{% endhighlight %}

### Creating a training document

Now we need some high-quality text. And what better place to look than the much-loved Cat in the Hat story by Dr Seuss that we all probably all read as kids. Thankfully, Robert Dionne has already compiled a text file containing the full story which we can read in using the following code:

{% highlight python linenos %}

import requests
 
response = requests.get('https://raw.githubusercontent.com/robertsdionne/rwet/master/hw2/catinthehat.txt')
doc = response.text
print(doc[:300])

{% endhighlight %}

In the script above, we use the very useful requests module and get the text file directly from GitHub. Let’s print out the first 300 characters of our document just to be sure we’ve grabbed the right file:

{% highlight python linenos %}

>> The Cat in the Hat
By Dr. Seuss
The sun did not shine.
It was too wet to play.
So we sat in the house
All that cold, cold, wet day.
I sat there with Sally.
We sat there, we two.
And I said, "How I wish
We had something to do!"
Too wet to go out
And too cold to play ball.
So we sat in the house.

{% endhighlight %}

Seems right!

### Text pre-processing

Next, we need to do some text pre-processing in which we will transform our document into a sequence of tokens which we can use to construct a training data set for our model. Based on the short snippet of the story we saw, we clean the text in the following way:

{% highlight python linenos %}

import string
 
doc = doc.replace('\n', ' ').lower()
doc = doc.translate(str.maketrans('', '', string.punctuation))
tokens = doc.split()
vocab_size = len(set(tokens))

print(f"Tokens: {tokens}")
print(f"Total tokens: {len(tokens)}")
print(f"Vocabulary size: {vocab_size}")

{% endhighlight %}

We begin by replacing all new line characters with spaces and converting all characters to their lower case form. Next, we remove any punctuation from our document. We can then split our document into individual tokens (or words) based on the space characters by making use of the sring’s .split() method. Let’s count the number of tokens to see what we have to work with:

{% highlight python linenos %}

Tokens: ['the', 'cat', 'in', 'the', 'hat', 'by', 'dr', 'seuss', ...
Total tokens: 6290
Vocabulary size: 855

{% endhighlight %}

Seems like our story contains 6290 tokens in total but has a vocabulary size of only 855 unique words.

Now, the next step is to transform our tokens into a set of sequences that can act as our training dataset. For this, let’s organise our list of tokens into sequences of 64 input words and one target word (giving us a total sequence length of 65). We can do this by “sliding” a window (of size 65) sliding across our list of tokens and joining them together to create a sequence sample.

{% highlight python linenos %}

length = 64 + 1
sequences = []
for i in range(length, len(tokens)):
  line = ' '.join(tokens[i - length:i])
  sequences.append(line)
print(f"Number of sequences: {len(sequences)}")

{% endhighlight %}

Let’s also print out how many training sequences we obtained:

{% highlight python linenos %}

Number of sequences: 6225

{% endhighlight %}

This should be more than enough training sequences for demonstration purposes.

### Prepare the dataset

Although the string representation of our words is nice for humans to look at, it won’t mean much for a neural network which only deals with numbers. We therefore need to map each of the words in our vocabulary to a corresponding integer value.

{% highlight python linenos %}

import numpy as np
from keras.preprocessing.text import Tokenizer
from keras.utils import to_categorical
 
tokenizer = Tokenizer()
tokenizer.fit_on_texts(sequences)
sequences = np.array(tokenizer.texts_to_sequences(sequences))
vocab_size += 1

{% endhighlight %}

For this, we can use keras’s Tokenizer class. We can first define a tokenizer object and fit it on all our set of sequences, which, in effect, finds all unique words in our data and maps each of them to a unique integer ID. More specifically, words are assigned values from 1 to the total number of words. We then use the tokenizer to re-define our sequences to be a set of integers and store the result as a numpy array. At this stage, since the word at the end of the vocabulary will be 855 but Python indexing of arrays starts at zero, we increment the vocabulary size to correct for this.

Now that our sequences are properly encoded, we can split them into the set of features X and target variables y.

{% highlight python linenos %}

X, y = sequences[:,:-1], sequences[:,-1]
y = to_categorical(y, num_classes=vocab_size)
seq_length = X.shape[1]

{% endhighlight %}

For indexing reasons, we utilise numpy’s handy splicing operation to perform this splitting. After this, we one-hot encode the target word using keras’s to_categorical() method which, in effect, transforms our output to a vector of length vocab_size with a value of 1 in the place of the word’s position and a value of 0 everywhere else. It will then be the job of the model to learn a probability distribution over all words in our vocabulary.

### Define and train the model

Hooray! We’ve arrived at the fun part of choosing the structure of our neural language model and training it.

{% highlight python linenos %}

from keras.models import Sequential
from keras.layers import Dense, LSTM, Embedding
 
model = Sequential()
model.add(Embedding(vocab_size, 100, input_length=seq_length))
model.add(LSTM(128, return_sequences=True))
model.add(LSTM(128))
model.add(Dense(128, activation='relu'))
model.add(Dense(vocab_size, activation='softmax'))
print(model.summary())

{% endhighlight %}

We’ll keep things fairly standard by defining a keras sequential model and adding some layers to it. More specifically, we will use an Embedding Layer to learn the representation of words, as well as a Long Short-Term Memory (LSTM) recurrent neural network to learn to predict words based on their context. The inputs to the Embedding layer is the vocabulary size, the length of the embedding vector (which we choose as 100) and the length of the sequence. We also specify a layer size of 128 for both LSTM layers (since powers of two are computationally more efficient). Finally, we will define two fully connected layers which will be used to interpret the features extracted from the sequence, the first having again 128 layers and a Rectified Linear Unit (ReLU) activation function and the last having 856 layers and a softmax activation function (to ensure the outputs are scaled between zero and one). We can print out a summary of our model as a sort of sanity check:

{% highlight python linenos %}

Model: "sequential"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
embedding_1 (Embedding)      (None, 64, 64)            54784     
_________________________________________________________________
lstm_1 (LSTM)                (None, 64, 128)           98816     
_________________________________________________________________
lstm_2 (LSTM)                (None, 128)               131584    
_________________________________________________________________
dense_1 (Dense)              (None, 128)               16512     
_________________________________________________________________
dense_2 (Dense)              (None, 856)               110424    
=================================================================
Total params: 412,120
Trainable params: 412,120
Non-trainable params: 0
_________________________________________________________________

{% endhighlight %}

Everything seems in order. Now we can compile and train our model as follows:

{% highlight python linenos %}

model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
model.fit(X, y, batch_size=128, epochs=100)

{% endhighlight %}

We define a cross entropy loss function which is sensible since we are technically dealing with a multi-class classification problem. We will also specify that keras must use the efficient Adam optimizer for updating the model weights evaluated on accuracy. Finally, we fit the model on our data for 100 training epochs with a fairly modest batch size of 128. Now all we have to do is go grab a coffee and let our model train.

### Training the model

Congratulations! You have just trained your very own neural language model! Let’s explore your new model’s capabilities by generating some random text with it. For this, we are going to create a function that takes as input the model and associated tokenizer we have just created, together with the sequence length, number of words to generate, and some input text which will act as a starting point in the generation process.

{% highlight python linenos %}

from keras.preprocessing.sequence import pad_sequences
 
def generate_sequence(model, tokenizer, seq_length, num_words, input_text):
  word_lookup = dict((v, k) for k, v in tokenizer.word_index.items())
  for _ in range(num_words):
    encoded = tokenizer.texts_to_sequences([input_text])[0]
    encoded = pad_sequences([encoded], maxlen=seq_length, truncating='pre')
    y_pred = model.predict_classes(encoded, verbose=0)[0]
    new_word = word_lookup[yhat]
    input_text += ' ' + new_word
 return input_text

{% endhighlight %}

We begin by defining a dictionary that can act as a sort of lookup that provides us with the string representation of a word given a word’s ID. Next, we encode the input text according to the mapping defined by our tokenizer. To ensure that the input text doesn’t grow too long, we truncate the text to the sequence length required by our model using Keras’s pad_sequences() method. We can now pass this encoded sequence to our model as input and the output we receive is the ID of the most likely word in the sequence. We can then use our lookup dictionary to get the string representation of our word and append it to our input text with a space.

All that’s left to do now is try our function out.

{% highlight python linenos %}

input_text = "then the cat turned around and laughed"
generate_sequence(model, tokenizer, seq_length, input_text, 10)

{% endhighlight %}

We pass the model, tokenizer and sequence length, as well as some input text and specify that 10 additional tokens must be generated. And the moment of truth…

{% highlight python linenos %}

'then the cat turned around and laughed you should a yink i have a lot of one my teeth are gold'

{% endhighlight %}

Interesting choice of words, but I don’t quite think our model is going to be the next Dr Seuss. Nonetheless, this should give you a good enough idea about how neural language models are working under the hood. Advanced neural language models produce significantly better results since they utilise much more sophisticated architectures, are trained on much more text and have significantly more parameters compared to our toy example. But I encourage you to take this example and make it your own. Try playing around with the model architecture and training hyperparameters or even change the input text to something completely different and see what kind of results you can generate!

***

## Wrapping up

And that’s all! In this post, I walked you through how to create your very own neural language model in Python with some help from Keras and how you can use this model to generate your own text sequences.

***
