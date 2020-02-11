---
layout: post
title:  "Bias Variance Tradeoff (Overfitting vs Underfitting)"
date:   2020-02-11 17:16:05 +0000
categories: ML
---

Have you noticed that when you ask a child to do a task for you, sometimes they are overly pedantic about small details? 
Say you ask them to pour about two cups of milk in a pan. They might excitedly get out a syringe from the drawer with millimetre precision and pour out the milk in the saucepan.

Now imagine making the same request to a moody teenager. They probably can't be bothered to get involved with this chore and will grudgingly pour out the milk - the amount poured could be way less or way more than the two cups you had asked for.

You on the other hand might  have years of experience on the job. When someone tells you to pour two cups of milk in a pan, you can estimate and pour straight from the bottle - chances are, your estimate will be pretty close to the _true value_ of two cups.

If you have been able to follow the analogy I have just given, then the notion of over and underfitting a model is intuitive to you. The child's model that has overfitted and the teenager's has underfitted - you on the other hand have acquired a model with _just_ the right degree of tradeoff.

**Overfitting**
When we use a model that is too complex, we end up adapting too closely to our data which results in _high variance_ as it fluctuates wildly from one datapoint to the next. The model pays so much attention to every little detail in data that it doesn't generalise well and often results in incorrect predictions for new, unseen data.

**Experiment**

Inspired by the [scikit-learn documentation](https://scikit-learn.org/stable/auto_examples/model_selection/plot_underfitting_overfitting.html) I did a quick experiment to see a practical example of the bias-variance tradeoff. 

Begin by importing the prerequisites:
{% highlight python %}
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import cross_val_score
{% endhighlight %}

<p align="center">
  <img src="/assets/AKOGHLREOP1TIOSM.png" alt="drawing" width="500"/>
</p>

**Underfitting**

![Dodecic polynomial model overfitting the data](/assets/5OI3FKC2OU82R44V.png)

**Ways to deal with the problem**

![Quartic polynomial model fits the data quite well](/assets/LKPRO99PFXEBV4SO.png)

_In progress..._
