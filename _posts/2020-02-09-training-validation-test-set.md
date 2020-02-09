---
layout: post
title:  "Training, Validation and Test Set"
date:   2020-02-09 13:49:14 +0000
categories: ML
---

When deploying a machine learning solution, we want our model to make predictions based on training data. But, it must make these predictions with data it has never seen before. This gives rise to errors in predictions which engineers must try to reduce before deploying the model. This whole exercise of splitting data is to make our validation and test set representative of unknown future data.

You might think we pour all our data into our model to get the perfect solution, but this is far from the truth. Data is carefully split up into three categories during the exploratory analysis phase to minimise the likelihood of error in prediction, which results in a much more useful system with fewer false-positives and true-negatives.

Finding the exact split ratio is very much dependent on the nature of the data you are dealing with. But as a rule of thumb, a typical split ratio as outlined in the seminal book [The Elements of Statistical Learning](https://web.stanford.edu/~hastie/Papers/ESLII.pdf), says 50% should be allocated to a training set, with the remaining 50% evenly split between validation and test set.

![50% training set, 25% validation set, 25% test set](/assets/C50C302C-E55A-4905-8171-D2D6B08285CF.jpeg)

**Note:** The term _validation set_ and _test set_ is sometimes used interchangeably in the industry.

## Training set

This should contain both your independent and dependent variables - also known as input and 
target vectors. 50% of your data should belong to this set and you should only use this set to actually train your model to prevent overfitting. 

## Validation set

This set consists of the next 25% of your dataset and is used to estimate the prediction error for different models so that you can empirically test which model has the highest accuracy.

## Test set

Keep this set locked up until your model is ready for production. Do not, in any way, try to use this data to train your model as this will definitely result in overfitting and will result in a less accurate model. This set should only contain independent variables and when correctly used, should give us the generalisation error of the final model you have chosen - because sometimes, your model may score a low prediction error just through sheer chance.
