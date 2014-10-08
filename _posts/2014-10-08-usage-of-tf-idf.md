---
layout: post
title: "Usage of TF IDF"
description: ""
category: tech
tags: [NLP, Text Classification, Data Mining, Tf-idf, LDA]
---
{% include JB/setup %}

### Defination
TF - IDF is short for **term frequency - inverse document frequency**. The wiki page can be found here: [tf - idf](http://en.wikipedia.org/wiki/Tf–idf). 

Term frequency means the raw frequency of a term *t* in occurs in a document *d*, which can be represented as *tf(f, d) = f(t, d)*. 

Inverse document frequency measures whether the term is common or rare across all documents. It is the logarithmically scaled fraction of the documents that contain the word, obtained by **dividing the total number of documents by the number of documents containing the term**, and then taking the logarithm of that quotient, which can be represented as *idf(t, D) = log(N / {d in D: t in d})*.

Finally *tfidf(t, d, D) = tf(t, d) x idf(t, D)*.

### Usage
Apparently, if a term *t* has a very high *tf* in one document, it means that this term appears lots of times in that document. Then we can believe that this term can help us classify this document.

If the number of documents containing term *t* is very small, the *idf* will be larger. Then we can also believe that the term *t* can help us classify this kind of documents. And idf can help us find some common words like "a", "the", "is". These words can be appeared many times in a document, so the tf value will be very large. But they are also existed in every document, so the idf value will be very small, even 0. Then the tf-idf can help us filter these words.

### Disadvantage
A basic assumption of tf-idf is that the most meaningful word that help us classify the document should appear most times. However, it is not always correct. A better way is to introduce semantic analysis. A recent way is to use LDA (Latent Dirichlet Allocation). I haven't use this in my experiment, but I will try to talk a little about that in the next section.

### LDA
LDA is short for Latent Dirichlet Allocation. Here is a very good explanation about LDA on Quora if you don't want to read the boring wiki page:

<http://www.quora.com/What-is-a-good-explanation-of-Latent-Dirichlet-Allocation>

Basically, LDA is an unsupervised learning algorithm. It can provide the topics that each document contains. If given 3 documents and 2 topics, LDA can return something like "document A is 100% topic 1, document B is 100% topic 2, document C is 80% topic 1 and 20% topic 2".

LDA assumes that documents are mixtures of topics. In this model, LDA assumes that documents are created in the following steps:

*	Decide how many words that a document have.
*	Choose a topic mixture for the document. For example, we have two topics "food" and "animal", and we decide to create a document with 30% "food" and 70% "animal".
*	Pick a topic. So we have 30% probability to pick "food" and 70% probability to pick "animal".
*	For each topic we picked, generate a word. For example, "food" may generate "beef" with 40% and "animal" will generate "dog" with 35%.

After that, LDA will try to backtrack from the document to find a set of topics that is most likely to generate this document.

This is just a simple example about how LDA works. I will explain it after I try it in my experiment and read more documents.