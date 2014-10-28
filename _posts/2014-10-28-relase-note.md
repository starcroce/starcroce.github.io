---
layout: post
title: "Relase Note"
description: ""
category: tech
tags: [Text Classification, Sentiment Analysis, NLP, Data Mining]
---
{% include JB/setup %}

Just to take some notes about 1st release parameters.

*	max_df: 0.2
*	n_gram: (1, 2)
*	tokenizer: StemTokenizer()
*	stop_words: 'english'
*	C: 1.0

Preprocess:

*	normalization
*	convert to lower
*	remove punctuation
*	stem
*	remove stopwords

All the previous may be changed according to the updated data set. And the service code needs to be modified according to the updated training result.