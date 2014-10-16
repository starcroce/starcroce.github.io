---
layout: post
title: "Data Mining Result Analysis"
description: ""
category: tech
tags: [Data Mining, Statistics, Maths]
---
{% include JB/setup %}

Just want to take some notes about how to analysis the classificaiton results.

### Confusion Matrix:

TP / FP / FN / TN:

*	**True Positive**: category A classified as category A.
*	**False Positive**: category B classified as category A.
*	**False Negative**: category A classified as category B.
*	**True Negative**: category B classified as category B.

The confusion matrix looks like:

![](http://www.gepsoft.com/gepsoft/APS3KB/Chapter09/Section2/confusionmatrix.png)

There is another confusion matrix that exchange the posisiton of FN and FP. I don't know which one is better.

### Sensitivity, Specificity and other

Parameter|Value|
:---------------|:---------------|
sensititvity (recall, true positive rate) | TP / (TP + FN)
false negative rate | FN / (TP + FN) = 1 - recall
specificity (true negative rate) | TN / (FP + TN)
fall-out (false positive rate) | FP / (FP + TN) = 1 - spec
positive predication value (precision) | TP / (TP + FP)
false discovery rate | FP / (FP + TP) = 1 - ppv
negative prediction value | TN / (TN + FN)
false omission rate | FN / (FN + TN)
accuracy | (TP + TN) / ALL
f1 score | 2 * TP / (2 * TP + FP + FN) = 2 * precision * recall / (precision + recall)

All the above info can be found in this [wiki page](http://en.wikipedia.org/wiki/Confusion_matrix)

### Receiver Operating Characteristic

Use x = FPR and y = TPR, we can draw a point in a space from (0, 0) to (1, 1). The best position for a classifier is at top left point (0, 1), which means zero FPR and zero FNR. And the result of a random classifier will be at the line from (0, 0) to (1, 1), which is the *line of no-discrimination*.