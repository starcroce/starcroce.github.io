---
layout: post
title: "Architecture of My Project"
description: ""
category: tech
tags: [Python, NLP, Data Mining, Text Classification, Sentiment Analysis]
---
{% include JB/setup %}

Just want to take some high level notes about my project architecture. No tech details, no code.

# Service

## Client

PHP code, input is the raw social post extracted from our Chrome extensioin. It will send this data to the NLP server via socket and wait for the result.

## Server

Main thread is listening on **WORK_PORT** and waiting for the input data. It will create a new thread to handle the input request when received the new data.

For the processing part, there are two steps: preprocessing and classification.

### Preprocessing

I have already talked something about the preprocessing. Basically there are three steps:

*	Normalization
*	Tokenize
*	Lemmatization

The details can be found in the [previous blog](http://starcroce.github.io/tech/2014/10/07/text-sentiment-analysis/).

### Classification

Load the binay file of classifier and some other objects, read the preprocessed input, classify it.

Well, seems very simple, ah? Just like Howard Wolowitz said:

>how to fly an airplane: get in the airplane, know where you want to go and fly it.

Come on...

# Binary Object

Since I have told my manager that our online service don't need any third party library, I have to save all the objects depending on NLTK or sklearn as a binary file.

Basically what we need are the following objects:

*	Classifier
*	Lemmatization tokenizer
*	Target result (not sure if we must need that)
*	The current training result shows that removing stopwords may decrease the accuracy

# File Issue

When I am trying to load the training set in my local machine, it will always run into some problem. But it works fine if I run it on the server. The result may be the `.DS_Store` file created by Mac.

---

# Parameters (updates)

LinearSVM, SVM, Naive Bayes (sklearn, nltk), SGDClassifier,

max_df: 0.0 - 1.0

stopwords: none, english

max_feature: none, 5000, 10000, 50000

ngram: 1, 2, 3

sublinear_tf: true, false

tokenizer: stem, lemma, other

classifier parameters:

*	SVM: C
*	SGDClassifier: alpha, penalty, n_iter