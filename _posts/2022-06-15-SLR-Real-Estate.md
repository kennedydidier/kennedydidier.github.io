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
    3. The influence of **neighborhood** on **home price.**  
    4. The influence of various **house characteristics** on the average **price increase or decrease** of a listing.  

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

{% endhighlight %}

I'm going to start by looking at a boxplot of Year and Sale Price.

{% highlight python %}
sns.boxplot(y='sale_price', x='sale_year', data=sold)
plt.xlabel('Year Sold')
plt.ylabel('Sale Price')
{% endhighlight %}

![sold price year boxplot](https://user-images.githubusercontent.com/102122956/174668867-b62c685a-4eab-4d5f-8a9c-8569e394cbba.png)

It looks like there might be a slight negative effect of Sale Year on Sale Price. Let's investigate further and build a univariate regression model.

{% highlight python %}
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

#### Simple Linear Regression of Sale Year on Price of Listings Purchased for Clients

I repeated the same splitting, regression, and plotting process for the purchased properties dataset, and produced the following graphs:

![lrptb train](https://user-images.githubusercontent.com/102122956/174651831-031300f7-9e28-4235-8e26-68324a6e5ea8.png)
![lrptb test](https://user-images.githubusercontent.com/102122956/174653005-27ac2e9b-b670-4ca9-82e4-02c5ce8ee2aa.png)

This looks like there might be a slight positive influence of purchase year on purchase price. Again, I want to call the OLS module to assess its statistical significance. 

{% highlight python %}
x_stat=sm.add_constant(x_train)
lrptbsum=sm.OLS(y_train, x_stat).fit()
lrptbsum.summary()
lrpt.score(x_test, y_test)
lrptb.score(x_test, y_test)
{% endhighlight %}

It looks like these are still not statistically significant. Likewise, our score results are negative, which suggests that this model performs quite poorly. Let's dig into some other relationships and then begin building an additive model.


#### Simple Linear Regression: Price on Days on Market

I now want to see if there's a statistically significant relationship between listing price and the amount of time one of my client's listings stays on the market. 

After repeating the same process from above, the univariate model using Price to predict days on market performed negligibly.  

This does not mean I have nothing to report to my client about these figures, however. The absence of statistical significance is, in fact, important to my client -- I can tell her that the prices of the homes she sells and the homes she purchases are not dependent on the year in which the sale occurs, meaning variation in sale price must be the result of another factor. Likewise, I can inform her that in this univariate model, the amount of time a listing spent on the market was not significantly or clearly related to the price of that listing, meaning that variation in days on market must be explicable by other factors (neighborhood, number of bedrooms, etc.). While not significant in simple linear models, I will return to these variables when building multiple regression models later.

#### Neighborhood on Home Price, Price Per Square Foot, and Total Price Change

For the next few models, I'm going to use datasets that include listings both sold and purchased for clients because we really want a glimpse of house characteristics, not realtor KPIs. 
{% highlight python %}
dat.neighborhood=dat.neighborhood.fillna('outside_scarsdale')
sns.boxplot(y='sale_price', x='neighborhood', data=dat)
plt.xlabel('Neighborhood')
plt.ylabel('Sale Price (Millions)')
plt.title('Boxplot of Neighborhood on Price')
plt.show()
{% endhighlight %}

![boxplot neighborhoo price](https://user-images.githubusercontent.com/102122956/174687174-69601e40-ff1f-4d13-841f-e2c8c76e65f4.png)


This suggests there may be a neighborhood influence on sale price of my client's listings, with Heathcote and Fox Meadow having higher average home price than Quaker Ridge, Greenacrs, and Edgewood. I now want to run a linear regression using dummy variables for each of the neighborhoods to determine if this is statistically significant.

{% highlight python %}


x=dat['neighborhood']
y=dat['sale_price']
x=pd.get_dummies(data=x, drop_first=True)
x_train, x_test, y_train, y_test=train_test_split(x, y, test_size=0.33)
reg=LinearRegression()
reg.fit(x_train, y_train)
pred=reg.predict(x_test)
>>>.2867866952610716
reg.score(x_test, y_test)
x_stat=sm.add_constant(x_train)
regsum=sm.OLS(y_train, x_stat).fit()
regsum.summary()

{% endhighlight %}

<img width="605" alt="Screen Shot 2022-06-20 at 7 07 42 PM" src="https://user-images.githubusercontent.com/102122956/174687344-ee753089-d5e3-4bff-9f31-4764923639ed.png">
<img width="603" alt="Screen Shot 2022-06-20 at 7 08 08 PM" src="https://user-images.githubusercontent.com/102122956/174687378-2ca5f29c-200e-4822-a5b2-dff60a9c1e87.png">



This return is really informative. I can now see that this model predicts sale price correctly 28% of the time, and that all of my dummy variables for neighborhoods are statistically significant at the p<0.05 level except those "Outside Scarsdale". The comparison neighborhood was Edgewood, which, as visible in the boxplot, has the lowest average sale price. According to our regression output, Heathcote has the largest positive impact on sale price. This is consistent with the suspicions of my client, who told me at our initial meeting that Heathcote was the wealthiest and most desirable neighborhood in Scarsdale. Fox Meadow has the second highest coefficient, followed by Greenacres and Quaker Ridge which have about the same coefficient.






