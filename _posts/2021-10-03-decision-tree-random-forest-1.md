---
layout: post
title: Decision Tree and Random Forest — PT 1
description: Decision Tree and Random Forest — PT 1
summary: Decision Tree and Random Forest — PT 1
tags: typography
minute: 6
---

# Decision Tree and Random Forest — PT 1

![Wallpaper]({{ site.url }}{{ site.baseurl }}/assets/images/decision-tree-random-forest/wallpaper.jpg)

> This article is also available on [medium](https://medium.com/@francesco.disalvo/decision-tree-and-random-forest-pt-1-729b74db1756).

Tree based algorithms are probably the most used algorithms in both classification and regression problems. In fact, if you see a couple of Kaggle notebooks, it is quite easy to understand that most of the people blindly try **Random Forest** as a first trial.

Today I would like to go deeper in this topic and try to explain what are their main advantages and disadvantages.

Even though there are several tree based algorithms, most of the times we refer to **Decision Tree** and **Random Forest**.
<br /><br />

## Decision Tree
**Decision tree** is a **supervised learning** algorithm that predicts the new labels by **recursively splitting** the predictor space into **non overlapping** distinct regions. For every observation that falls into any region we make the same prediction, which is the **majority class** in that region (for classification) or the **mean** of the response value (for regression).

Since considering every possible partition is computationally infeasible (**NP- hard problem**), the tree is constructed with a **greedy top down approach**, known as recursive binary splitting.

It is top-down because it begins at the top of the tree and then it successively splits the predictor space; each split is indicated via two new branches further down on the tree. Then, it is greedy because at each step of the tree-building process, the best split is made at that particular step, rather than looking ahead and picking a split that will lead to a better tree in some future step.

![BinarySplitting]({{ site.url }}{{ site.baseurl }}/assets/images/decision-tree-random-forest/splitting.jpeg)

> Source : ([link](https://tex.stackexchange.com/questions/526560/plotting-regression-tree-and-partitions))

In order to select the best split to make for classification problems, we consider the split that minimize something. Here we have two main alternatives, the **Gini Index** and the **Entropy**.

The Gini Index is a measure of impurity and in particular it measures the variances across the classes. It is defined as:

$$
\mbox {Gini} = 1 - \sum_j p_j^2
$$

where j is the current class evaluated and p_j is the probability (percentage) of class j. If we have one single class in the current partition its value will be 0 (the minimum) but if we have an equally distributed number of samples, we will have a Gini index equal to 
1-(1/#classes).

On the other hand, the Entropy measures the so called “information gain”, defined as the disorder of the features with the current target. It is defined as:

$$
\mbox{Entropy} = \sum_j p_j \log_2 p_j
$$

> In regression problems, we aim to minimize the RSS (Residual Sum of Squares)

The main **advantage** of a decision tree is that it is fairly **interpretable**. The expanded decision tree can be easily explained if it is not too big. The problem is not only the interpretability, but huge trees could also have some redundant sub trees. Therefore, there are some techniques that allow to compress the dimensions, obtaining a less complex and less redundant tree. It can be pruned after the whole generation of the tree (**post-pruning**), replacing the less informative subtrees with leaves. However, the most efficient way to do that is to prune it before it grows on its entirety (**pre-pruning**) because it simplifies it step by step, and not in the end. The pruning can be performed for different reasons, for example we may decide to prune it if it overcomes a given threshold for the depth or for the Gini/Entropy.

If we come up with a non-enormous tree, it may be also understood by non-experts. However, the **disadvantages** overcome the benefits and this is the reason why decision trees are barely used. In particular, the performances are not always optimal but most importantly, they are strongly affected by **noise** and **outliers**.

These limitations have brought to light a more advanced tree-based algorithm, that is **Random Forest**, an ensemble method that combines multiple decision trees (that is why it is called “forest”) with the **bagging** technique, obtaining a more accurate and robust model.
<br /><br />

## Random Forest
The idea of **bagging** is to make prediction on **N bootstrapped** copies of the training set (i.e. sampled with replacement). In order to decorellate the trees, **feature bagging** is the best option. In particular, each time a split in a tree is considered, a fixed number of features (typically √p) is randomly chosen.
> p is the number of features of the given dataset

The final prediction will be given by a **majority voting scheme** of all the estimators (or the **average**, in regression tasks).

This is why this algorithm is fairly **robust** to **noise** and **outliers** and will have much **less variance** rather than a single decision tree. It is robust to noise and outliers because the “impact” of these data points will be spread over all estimators and therefore their impact will decrease as the number of estimators increases. Then, it has a less variance because if we consider n observations with variance σ^2, the variance of the sample mean (over all estimators) will be exactly σ^2/n.

![RandomForest]({{ site.url }}{{ site.baseurl }}/assets/images/decision-tree-random-forest/random_forest.jpg)
> Random Forest ([source](https://www.tibco.com/reference-center/what-is-a-random-forest))

On average, each bagged tree uses two third of the observation, therefore the remaining one third can be used to fit the given tree in order to evaluate its performances. This error is called **out of bag error** and sometimes it can be used for “stopping” the training procedure.

The million-dollar question is: **how many trees do I need?** I faced the same problem during my first trials. Intuitively, the higher is the number of estimators, the better will be the results BUT the improvements is not linear, therefore up to a certain point the computational complexity introduced will not be justified by its benefits.

When I was looking for the same answer I have found [this](https://www.researchgate.net/publication/230766603_How_Many_Trees_in_a_Random_Forest) paper that conducted an analysis on 29 different datasets and showed that “from 128 trees there is no more significant difference between the forests using 256, 512, 1024, 2048 and 4096 trees”.

Since we talked about the advantages we have to mention probably the main drawback : the **computational cost**. If we have limited resources, this algorithm may not be the perfect choice, especially if we have a large number of features and chosen estimators.
<br /><br />

## Conclusions
Today we went a bit deeper on the theory behind Decision Tree and Random Forest. Assuming that we have to select one of these two algorithms, we could say in a couple of lines that Decision Tree could be a valid option if we need to explain our model to non-experts or if we want to understand a bit what is going on inside our model. But, if we care only about the overall performances and we do not have any particular computational limitations, Random Forest should be preferred.

This was just the first part and soon or later I will post the second one, from which we will go through the implementation of both algorithms!

Thanks for reading the whole post and I hope it is all clear. If you still have some doubts or if you wanna just drop me a message, I would be more than happy to read it! Feel free to contact me on [LinkedIn](https://www.linkedin.com/in/francescodisalvo-pa/) :)
<br /><br />

## References
* Wallpaper : https://unsplash.com/photos/7EqQ1s3wIAI
* Gini vs Entropy : https://en.wikipedia.org/wiki/Recursive_partitioning
* Bagging vs Boosting : https://quantdare.com/what-is-the-difference-between-bagging-and-boosting/
* Number of trees in RF : https://www.researchgate.net/publication/230766603_How_Many_Trees_in_a_Random_Forest