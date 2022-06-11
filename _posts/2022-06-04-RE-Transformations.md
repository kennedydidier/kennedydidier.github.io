---
layout: post
title: Real Estate Data Transformations
categories: [real estate, python, data exploration]
---

This is part of a project I completed for Cindy Schwall, a top real estate agent in Westchester. My first objective was to track "unconventional" KPIs --- basically, anything outside of sales volume, the traditional metric of interest for agents and brokerages. We worked together to identify what some of these might be and which we could actually track given the available data Cindy had stored from past sales. We came up with a few that I would start looking at -- including the graphical distribution of listings and purchases, how the average price of her listings has changed over time, and the average percentage increase or decrease from list to sale.
<!--more-->

This small project demonstrates how I explored and transformed the data provided to me by my client so that I would be able to adequately analyze it.

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

