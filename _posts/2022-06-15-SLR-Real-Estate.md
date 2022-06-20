---
layout: post
title: Simple Linear Regression Algorithms on Client Real Estate Data
categories: python, structured learning algorithms, modeling, data analysis, real estate
---

Now that I've produced preliminary graphs and a interactive map of listings for my client, it's time to get into the real heart of my project: building regression models that predict import KPIs. Based on our prior conversations, I know the first things she is interested in are:
    1. The influence of **sale year** on the average **price** of 
          (a.) the listings she sells for clients and
          (b.) the listings her clients purchase.
    2. The influence of **listing price** on the amount of **time** a listing spends on the market.
    3. The influence of **time on market** on the average **price increase or decrease** of a listing.
    4. The influence of **neighborhood** on **home price.**
    5. The influence of various **house characteristics** on the average **price increase or decrease** of a listing.

All of my dependent variables in these inquiries are continuous, and I'm first interested in exploring the simple relationships between one independent variable and one dependent variable, so I'll begin by building simple ordinary least squares linear regression algorithms.

#### Simple Linear Regression of Sale Year on Price of Listings Sold for Clients

{% highlight python %}
import pandas as pd
import numpy as np
from sklearn.linear_model import LinearRegression
import matplotlib.pyplot as plt
import statsmodels
import seaborn as sns
from sklearn.model_selection import train_test_split
import statsmodels.api as sm

x=sold['sale_year']
y=sold['sale_price']
x_train, x_test, y_train, y_test = train_test_split(
x, y, test_size=0.33)
x_test=x_test.to_numpy().reshape(-1,1)
x_train=x_train.to_numpy().reshape(-1,1)
y_test=y_test.to_numpy().reshape(-1,1)
y_train=y_train.to_numpy().reshape(-1,1)

lrpt=LinearRegression()
lrpt.fit(x_train, y_train)
y_pred=lrpt.predict(x_test)
plt.scatter(x_train, y_train, color='blue')
plt.plot(x_train, lrpt.predict(x_train), color='green')
plt.title('Training Set')
plt.xlabel('Year Sold')
plt.ylabel('Sale Price')
plt.show()
plt.scatter(x_test, y_test, color='pink')
plt.plot(x_train, lrpt.predict(x_train), color='purple')
plt.title('Test Set')
plt.xlabel('Year Sold')
plt.ylabel('Sale Price')
plt.show()
{% endhighlight %}

![lrpts training set](https://user-images.githubusercontent.com/102122956/174644556-5cec23f1-6612-4474-b742-1e03d20aab35.png)

![lrpt test set](https://user-images.githubusercontent.com/102122956/174644566-be259a63-be29-4025-825a-c51d438533d5.png)

These graphs reveal a pretty steady rate of sale price from 2017-2021. I want to know more specifics, so I'm going to call the OLS estimator module.

{% highlight python %}
x_stat=sm.add_constant(x_train)
lrptsum=sm.OLS(y_train, x_stat).fit()
lrptsum.summary()
{% endhighlight %}

The p-values of my intercept and constant reveal neither are statistically significant. This suggests that there is no significant impact of sale year on sale price. Let's take a look at listings my client purchased with buyers.

I repeated the same splitting, regression, and plotting process for the purchased properties dataset, and produced the following graphs:

![lrptb train](https://user-images.githubusercontent.com/102122956/174651831-031300f7-9e28-4235-8e26-68324a6e5ea8.png)
![lrptb test](https://user-images.githubusercontent.com/102122956/174653005-27ac2e9b-b670-4ca9-82e4-02c5ce8ee2aa.png)




#### Simple Linear Regression of Time on Days on Market


#### Simple Linear Regression of Time on Percent Increase or Decrease of Listing



