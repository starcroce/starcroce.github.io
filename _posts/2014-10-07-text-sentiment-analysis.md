---
layout: post
title: "Text Sentiment Analysis"
description:
category: tech
tags: [Python, NLP, Sentiment Analysis, Text Classification]
---
{% include JB/setup %}

### Attention
Just for my own memo, not a public tutorial.

### Environment
Python, NLTK, scikit-learn

### Preprocess
*"80% of the data mining work is preprocess"*, cited by Winnie.

For the raw post from our service, there are serveral preprocessing steps:

*	convert "@abcd" to "@USERNAME"
*	convert "https://www.google.com" to "URL"
*	convert all char to lower case
*	remove punctuations and tokenize
*	stemming or lemmatization

For the first three steps, just use regex:

	import re
	text = re.sub('@[^\s]+', '@USERNAME', text)
	text = re.sub('((www\.[^\s]+)|(https?://[^\s]+))', 'URL', text)
	text.lower()
	
Actually, the third step can be done at anytime. It makes no difference. Now, these data can be used for third party manual tagging, which is our potential training set.

For removing the punctuations and tokenziation, we can use RegexTokenizer of NLTK.

	from nltk.tokenize import RegexTokenizer
	tokenizer = RegenTokenizer(r'\w+')
	tokens = tokenizer.tokenize(text)
	
It will return a list of tokens without punctuations. But for the tokens, we still need to stem or lemmatize it.

The basic difference between stemmer and lemmatizer can be found here: 

<http://stackoverflow.com/questions/17317418/stemmers-vs-lemmatizers>. 

NLTK provides some stemmer and lemmatizer like LancasterStemmer and WordNetLemmatizer. For the specific difference, please check NLTK documentation.

	from nltk.stem.lancaster import LancasterStemmer
	from nltk.stem import WordNetLemmatizer
	stemmer = LancasterStemmer()
	stemmer.stem('having')			# become 'hav'
	lemmatizer = WordNetLemmatizer()
	lemmatizer.lemmatize('dogs')		# become u'dog'
	lemmatizer.lemmatize('having', 'v')	# become 'have'
	
Need to test all of these method and choose the best way. I will write a new post about the difference between stemming and lemmatization later.

### Support Vector Machine
scikit-learn provides two implementation of SVM classifier: libsvm and liblinear. The basic difference can be found here: 

<http://stackoverflow.com/questions/11508788/whats-the-difference-between-libsvm-and-liblinear>.

For linear SVM, there is an important parameter C. The basic infulence can be found here: 

<http://stats.stackexchange.com/questions/31066/what-is-the-influence-of-c-in-svms-with-linear-kernel>. 

In my experiment, I tried from 1e-2 to 1e5. There are many other parameters in SVC with linear kernel or Linear SVC, but I keep them as the default. 

For the library documentation, we can check 

<http://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html> 

<http://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html>. 

The former is libsvm implementation and the latter is liblinear implmentation.

In scikit-learn, the step of text classification is as follows:

*	load the training set
*	count vectorizer
*	tf-idf transformer
*	fit the classifer

And step 2 and 3 can be combined together as tf-idf vectorizer.

When load the training set, the format of the training file set should be like:

*	category_1_dir: file1, file2, file3, ...
*	category_2_dir: file1, file2, file3, ...

I don't want to talk about what is tf-idf and how count vectorizer and tf-idf transformer works here. I will write a new blog to specify them in the future.

#### Parameter Tuning
Here is a simple example about parameter tuning using scikit-learn: 

<http://machinelearningmastery.com/how-to-tune-algorithm-parameters-with-scikit-learn/>. 

There is a class `sklearn.grid_search.GridSearchCV` that can help us to find the best parameter combination. The detailed document is here: 

<http://scikit-learn.org/stable/modules/generated/sklearn.grid_search.GridSearchCV.html>.

After parameter tuning, we can get the best parameters. Then we can start to train the classifier, save it as the binary file which can be used in the future online service.