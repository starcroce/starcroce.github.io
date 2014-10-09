---
layout: post
title: "Python Encoding in My Project"
description: ""
category: tech
tags: [Python, Encoding]
---
{% include JB/setup %}

Previous raw service log: ascii.

Preprocess function ("https://www.google.com" -> "URL", "@Tom" -> "@USERNAME") needs ascii format.

Generate random log for manual label: ascii.

Write manual label result into mysql database: both can work, can keep ascii.

Posts in MySQL database: utf-8. MySQL will convert it automatically.

Query post from MySQL into training file: keep utf-8. The default encoding of `sklearn.dataset.load_files()` is utf-8.

After load training set, it seems can do other preprocessing like tokenzie, stemming, lemmatization, filter stopwords without considering the encoding format. Need to confirm it later.

Input from future online service: ascii, seems no issue based on previous experiment. Need to check if preprocess function will introduce any issue.

Test set: if use `sklearn.dataset.load_files()`, they should be utf-8 and are already after the preprocessing. If we want to simulate the future online service, it should be ascii because we need to apply the preprocss function.

I am working on the last part of simluating future online service. For now I use UCSC demo data and manual label result of our own socila log as the training set. The test set is the bully related images.