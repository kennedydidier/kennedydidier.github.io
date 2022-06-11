---
layout: post
title: Real Estate Data Transformations
categories: [real estate, python, data exploration]
---

This project is the first of several I completed for Cindy Schwall, a top real estate agent in Westchester.
<!--more-->

{% highlight python %}
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')

dat=pd.read_csv("~/cindydatabookfinal.csv")
dat
dat.shape
dat.columns
dat.info()
dat.describe()
dat.isnull().sum()
{% endhighlight %}

