---
layout: post
title:  "Getting the hang of Pandas"
date:   2020-02-07 17:16:54 +0000
categories: machine learning
---

Think of Pandas like Excel, but for hackers. It is infinitely faster and more powerful. Did I also mention it was free? 

Yes it's free.

In pandas, you will work commonly with Series and DataFrame. So, what's the difference between the two? 

## Series
This is basically a one-dimensional array or a list - nothing new in the world of programming. Most often, you'll come across a series in Pandas when you extract a column from a dataframe, because a series is what each column is made of in a dataframe.

## DataFrame
This is where the juice lies. A dataframe is basically a table from your spreadsheet. As you might expect, it can have columns with rows of data. As we start out, this sounds pretty cool, but its true power starts to shine when you begin to manipulate a 50-dimensional table, or 500 for that matter.

----

Without any further ado, let's jump in! Before running any code, remember to import `pandas` along with `Series` and `DataFrame`. We also import a very handy library called `numpy` which we will look in-depth in another post.

{% highlight python %}
from pandas import Series, DataFrame
import pandas as pd
import numpy as np
{% endhighlight %}

### Series cheatsheet

To create a new series:

{% highlight python %}
my_series = Series([10, 8, 3, 6])
{% endhighlight %}

Presumably you want to do something with it, such as using a predicate to filter and return a new series:

{% highlight python %}
filtered_series = my_series[my_series > 7]
{% endhighlight %}

after this, `filtered_series` will contain `[8, 10]`.

Some more handy features include the `isnull` and `notnull` operations. Given a series `raw_series` with values `[5, 10, 15, NaN]`, if we do `pd.isnull()`, our output will be:

```
raw_series
0   False
1   False
2   False
3   True
```

Conversely, using `pd.notnull()`, we get: 

```
raw_series
0   True
1   True
2   True
3   False
```

Real world data is messy, and these operations remain our best mates when tackling certain columns with missing data.

One of the cool things which differentiates a series from a standard array is the ability to name indices. Imagine `my_series` above represents points scored by different players in a game, then, instead of remembering the element `0` represents `Tom` and `1` represents `Jane`, we can just alter the index of `my_series` _in-place_, meaning it changes the original series, not its view: 

{% highlight python %}
my_series.index = ['Tom', 'Jane', 'Kathy', 'Sam']
{% endhighlight %}

This gives the output:
```
my_series
Tom    10
Jane    8
Kathy   3
Sam     6
```

You can then easily pick out the score of `Kathy` through `my_series['Kathy']` which returns `3`.

### Dataframe cheatsheet

There's absolutely a ton of stuff you can do with dataframes. To begin with, let's see how we can read a csv file into a pandas dataframe. In this example, we're using the nCoV-2019 coronavirus data originating from Wuhan, in Hubei province, China.

{% highlight python %}
url = 'https://raw.githubusercontent.com/globalcitizen/2019-wuhan-coronavirus-data/master/data-sources/bno/data/20200124-145500-bno-2019ncov-data.csv'
sample_df = pd.read_csv(url, sep='|', skiprows=2)
{% endhighlight %}

If you see an error, use it's most likely due to a breaking change in the header. Simply skip reading in the header like so:

{% highlight python %}
sample_df = pd.read_csv(url, sep='|', header=None skiprows=3)
{% endhighlight %}

_In progress..._
