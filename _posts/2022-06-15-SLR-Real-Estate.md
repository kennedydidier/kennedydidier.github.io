---
layout: post
title: Simple Linear Regression Algorithms on Client Real Estate Data
categories: python, structured learning algorithms, modeling, data analysis, real estate
---

Now that I've produced preliminary graphs and a interactive map of listings for my client, it's time to get into the real heart of my project: building regression models that predict import KPIs. Based on our prior conversations, I know the first things she is interested in are:
    1. The influence of **time** on the average **price** of 
          (a.) the listings she sells for clients and
          (b.) the listings her clients purchase.
    2. The influence of **listing price** on the amount of **time** a listing spends on the market.
    3. The influence of **time on market** on the average **price increase or decrease** of a listing.
    4. The influence of **neighborhood** on **home price.**
    5. The influence of various **house characteristics** on the average **price increase or decrease** of a listing.

All of my dependent variables in these inquiries are continuous, and I'm first interested in exploring the simple relationships between one independent variable and one dependent variable, so I'll begin by building simple ordinary least squares linear regression algorithms.

#### Simple Linear Regression of Time on Housing Price

{% highlight python %}
import pandas as pd
import numpy as np
from sklearn.linear_model import LinearRegression
import matplotlib.pyplot as plt
import statsmodels
import seaborn as sns
from sklearn.model_selection import train_test_split
import statesmodels.formula.api as smf

x=dat['sale_date']
y=dat['sale_price']
x_train, x_test, y_train, y_test = train_test_split9
x, y, test_size=0.33, random_state=42)
x_train
y_train

{% endhighlight %}




#### Simple Linear Regression of Time on Days on Market


#### Simple Linear Regression of Time on Percent Increase or Decrease of Listing



