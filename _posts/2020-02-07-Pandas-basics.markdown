---
layout: post
comments: true
title:  "Getting the hang of Pandas"
date:   2020-02-07 17:16:54 +0000
categories: ML
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

Let's preview the file before we read it, to get a sense of what we're dealing with:

```
# source: BNO @ https://bnonews.com/index.php/2020/01/the-latest-coronavirus-cases/
# update: 2020-02-3 5:02:00 ET
place|confirmed_cases|deaths|notes|sources
Hubei|11,177|350|1,223 serious, 478 critical|http://wjw.hubei.gov.cn/fbjd/dtyw/202002/t20200203_2018272.shtml
Zhejiang|724|0|48 serious, 12 critical|https://www.zjwjw.gov.cn/art/2020/2/3/art_1202101_41869217.html
Guangdong|725|0|58 serious, 22 critical|http://wsjkw.gd.gov.cn/zwyw_yqxx/content/post_2882427.html
Henan|566|2|30 serious, 14 critical|https://m.weibo.cn/status/4467799441404602
Hunan|521|0|58 serious|http://wjw.hunan.gov.cn/wjw/xxgk/gzdt/zyxw_1/202002/t20200203_11168209.html
Anhui|408|0|4 critical|http://wjw.ah.gov.cn/news_details_54452.html
Jiangxi|391|0| 34 serious |http://hc.jiangxi.gov.cn/doc/2020/02/03/138004.shtml
```

Ok, so the first two lines beginning with `#` are comments, so we need to skip that. Also, it appears that fields are separated by `|`. We can account for both of these by adding a couple of arguments to `read_csv`:

{% highlight python %}
url = 'https://raw.githubusercontent.com/globalcitizen/2019-wuhan-coronavirus-data/master/data-sources/bno/data/20200124-145500-bno-2019ncov-data.csv'
ncov = pd.read_csv(url, sep='|', skiprows=2)
{% endhighlight %}

To get a list of all column names: `ncov.columns`

{% highlight python %}
In[18]: ncov.columns
Out[18]: Index(['place', 'confirmed_cases', 'deaths', 'notes', 'sources'], dtype='object')
{% endhighlight %}

Let's make a table showing a list of places and the corresponding number of deaths:

{% highlight python %}
In[19]: death_pivot = ncov.pivot_table(index='place', values='deaths')
Out[19]: death_pivot
place       deaths
Anhui	    0.0
Beijing	    1.0
CHINA TOTAL 361.0
Cambodia    0.0
Canada	    0.0
Chongqing   2.0
{% endhighlight %}

Transposing is another handy feature for long tables, such as the one above. If you do: `death_pivot.T`, you will get a table that spans horizontally instead of vertically.

You may also want to delete columns from your dataframe. The `drop` method will help you with this. It takes an argument `axis` which tells panda what dimension to target. An example is the best way to understand this: `axis=0` refers to rows (x-axis), `axis=1` refers to columns (y-axis), `axis=2` refers to frames (z-axis). Of course, in a 70-dimension dataset, your axis value can range from 0-69.

{% highlight python %}
# remove 'sources' column
In[20]: ncov.drop('sources', axis=1)
{% endhighlight %}

Say you wish to pick out rows 5-10 from your dataframe, you can use the following:
{% highlight python %}
In[21]: ncov[4:10]
{% endhighlight %}

Basically, the syntax for slicing is: `df[start_index:end_index:increment]` where:
- `start_index` is inclusive
- `end_index` is non-inclusive
- `increment` with a value of `2` would pick every two rows in the range specified.

Ok, now we want to get the top-10 places with the highest number of deaths. `ascending=False` gives a descending order list as you might expect and `head(10)` returns the first 10 rows from the dataframe.

{% highlight python %}
In[22]: ncov.sort_index(by='deaths', ascending=False).head(10)
{% endhighlight %}

This is only the beginning of what we can do with Pandas. There's a lot more to explore and in future posts, we will do some analysis with various datasets from Kaggle, so if you don't have an account with them, I highly recommend signing up to get access to lots of data and cloud-powered jupyter notebooks free of charge.
