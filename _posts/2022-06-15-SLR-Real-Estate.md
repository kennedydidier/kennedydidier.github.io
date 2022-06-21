---
layout: post
title: Simple Linear Regression Algorithms on Client Real Estate Data
categories: python, structuredlearningalgorithms, prediction, dataanalysis, realestate
---

Now that I've produced preliminary graphs and a interactive map of listings for my client, it's time to get into the real heart of my project: building regression models that predict import KPIs. Based on our prior conversations, I know the first things she is interested in are:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1. The influence of **neighborhood** on **home price.**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2. The influence of various **house characteristics** on **home price**.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3. The influence of **sale year** on the average **price** of  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(a.) the listings she sells for clients and  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(b.) the listings her clients purchase.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 4. The influence of **listing price** on the amount of **time** a listing spends on the market. 

All of my dependent variables in these inquiries are continuous, and I'm first interested in exploring the simple relationships between one independent variable and one dependent variable, so I'll begin by building simple ordinary least squares linear regression algorithms.

#### Simple Linear Regression of Home Price, Price Per Square Foot, and Total Price Change on Neighborhood

For the first few models, I'm going to use datasets that include listings both sold and purchased for clients because I'm interested in house characteristics. 

Because my predictor variable is categorical, I want to start off by visualizing my data using a boxplot.

**Sale Price Regressed on Neighborhood**
{% highlight python %}
import pandas as pd
import numpy as np
from sklearn.linear_model import LinearRegression
import matplotlib.pyplot as plt
import statsmodels
import seaborn as sns
from sklearn.model_selection import train_test_split
import statsmodels.api as sm

dat.neighborhood=dat.neighborhood.fillna('outside_scarsdale')
sns.boxplot(y='sale_price', x='neighborhood', data=dat)
plt.xlabel('Neighborhood')
plt.ylabel('Sale Price (Millions)')
plt.title('Boxplot of Neighborhood on Price')
plt.show()
{% endhighlight %}

![boxplot neighborhoo price](https://user-images.githubusercontent.com/102122956/174687174-69601e40-ff1f-4d13-841f-e2c8c76e65f4.png)


This preliminary boxplot suggests there may be a neighborhood influence on sale price of my client's listings, with Heathcote and Fox Meadow having a higher average home price than Quaker Ridge, Greenacres, and Edgewood. I now want to run a linear regression using dummy variables for each of the neighborhoods to determine if this is statistically significant.


{% highlight python %}

x=dat['neighborhood']
y=dat['sale_price']
x=pd.get_dummies(data=x, drop_first=True)
x_train, x_test, y_train, y_test=train_test_split(x, y, test_size=0.33)
reg=LinearRegression()
reg.fit(x_train, y_train)
pred=reg.predict(x_test)
reg.score(x_test, y_test)
>>>0.2867866952610716
x_stat=sm.add_constant(x_train)
regsum=sm.OLS(y_train, x_stat).fit()
regsum.summary()

{% endhighlight %}

<img width="605" alt="Screen Shot 2022-06-20 at 7 07 42 PM" src="https://user-images.githubusercontent.com/102122956/174687344-ee753089-d5e3-4bff-9f31-4764923639ed.png">
<img width="603" alt="Screen Shot 2022-06-20 at 7 08 08 PM" src="https://user-images.githubusercontent.com/102122956/174687378-2ca5f29c-200e-4822-a5b2-dff60a9c1e87.png">

This return is really informative. I can now see that this model predicts sale price correctly 28% of the time, and that all of my dummy variables for neighborhoods are statistically significant at the p<0.05 level except those "Outside Scarsdale". The comparison neighborhood was Edgewood, which, as visible in the boxplot, has the lowest average sale price. According to our regression output, Heathcote has the largest positive impact on sale price. This is consistent with the suspicions of my client, who told me at our initial meeting that she thinks her most expensive listings are in Heathcote, and that it is considered the wealthiest and most desirable neighborhood in Scarsdale. Fox Meadow has the second highest coefficient, followed by Greenacres and Quaker Ridge which have about the same coefficient. Finally, the returned R-squared value suggests that neighborhood does account for a significant amount of the variance in sale price of my client's listings.

While my client asked specifically asked for neighborhood on sale price, I also want to provide her information on the influence of neighborhood on price per square foot, and also on days on market.

**Price Per Square Foot Regressed on Neighborhood**
{% highlight python %}
sns.boxplot(y='price_per_sqfoot', x='neighborhood', data=dat)
plt.xlabel('Neighborhood')
plt.ylabel('Price Per Square Foot')
plt.title('Boxplot of Price Per Square Foot on Neighborhood')
plt.show()

{% endhighlight %}

![bp neighborhood ppsft](https://user-images.githubusercontent.com/102122956/174692469-e50eed06-4479-45c2-a47e-f8550aa0d6ae.png)

{% highlight python %}
y=dat['price_per_sqfoot']
x=dat['neighborhood']
x=pd.get_dummies(data=x, drop_first=True)
x_train, x_test, y_train, y_test=train_test_split(x, y, test_size=0.33)
reg=LinearRegression()
reg.fit(x_train, y_train)
pred=reg.predict(x_test)
reg.score(x_test, y_test)
>>>0.07323535828660688
x_stat=sm.add_constant(x_train)
regsum=sm.OLS(y_train, x_stat).fit()
regsum.summary()

{% endhighlight %}

My output for price per square foot reveals that there is a negligible and non-statistically significant amount of variance in price per square foot explained by neighborhood. Maybe the number of bedrooms or other factors influences this more. Let's try Days on Market!

**Days on Market Regressed on Neighborhood**

{% highlight python %}

sns.boxplot(y='days_on_market', x='neighborhood', data=dat)
plt.xlabel('Neighborhood')
plt.ylabel('Days on Market')
plt.title('Boxplot of Days on Market on Neighborhood')
plt.show()

{% endhighlight %}

![bxp dm on neigh](https://user-images.githubusercontent.com/102122956/174698948-10dd36af-b14b-4a45-be7a-096ba04b0208.png)

This boxplot looks..... fuzzy, likely due to the presence of outliers. This will likely be reproduced in our training set. I'm going to make sure to model our training set using a robust regression algorithm.

{% highlight python %}
from sklearn.linear_model import HuberRegressor
y=dat['days_on_market']
x=dat['neighborhood']
x=pd.get_dummies(data=x, drop_first=True)
x_train, x_test, y_train, y_test=train_test_split(x, y, test_size=0.33)
mod=HuberRegressor()
mod.fit(x_train, y_train)
pred=mod.predict(x_test)
mod.score(x_test, y_test)
>>>-0.1399834593199174
x_stat=sm.add_constant(x_train)
modsum=sm.RLM(y_train, x_stat, M=sm.robust.norms.HuberT())
modsumres=modsum.fit()
modsumres.summary()

{% endhighlight %}

According to this, our model (one variable, neighborhood) predicts days on market very very poorly. Only Heathcote's coefficient is statistically significant. Let's move on and return to the neighborhood variable when we build additive predictive models.


#### Sale Price Regressed on Various House Characteristics

My client wants to know how two specific home characteristics (style and year built) influence sale price. Let's take a look!

**Sale Price Regressed on Style**

{% highlight python %}
dat['style'].value_counts()
{% endhighlight %}

It looks like there aren't really enough instances of any styles other than colonial, tudor, and contemporary to appropriately include, so I am going to constrict my dataset to only those.

{% highlight python %}
sdat=dat[(dat['style'] == 'colonial') | (dat['style'] == 'tudor') | (dat['style'] == 'contemporary')]
sns.boxplot(y='sale_price', x='style', data=sdat)
plt.xlabel('Style')
plt.ylabel('Sale Price')
plt.title('Boxplot of Sale Price Regressed on Style')
plt.show()
{% endhighlight %}

![bp sp s](https://user-images.githubusercontent.com/102122956/174707685-c0922d03-daa1-404a-ae12-95d03e0af05c.png)

After repeating the same regression process as above, I determined no styles were statistically significant at predicting Sale Price. Let's take a look at Year Built!

**Sale Price Regressed on Year Built**

{% highlight python %}
x=dat['year_built']
y=dat['sale_price']
x_train, x_test, y_train, y_test=train_test_split(x, y, test_size=0.33)
x_test=x_test.to_numpy().reshape(-1,1)
x_train=x_train.to_numpy().reshape(-1,1)
y_test=y_test.to_numpy().reshape(-1,1)
y_train=y_train.to_numpy().reshape(-1,1)
lryp=LinearRegression()
lryp.fit(x_train, y_train)
y_pred=lryp.predict(x_test)
plt.scatter(x_train, y_train, color='dodgerblue')
plt.plot(x_train, lryp.predict(x_train), color='darkred')
plt.title('Price Regressed on Year Built -- Training Set')
plt.xlabel('Year Built')
plt.ylabel('Sale Price')
plt.show()
plt.scatter(x_test, y_test, color='pink')
plt.plot(x_train, lryp.predict(x_train), color='darkred')
plt.title('Price Regressed on Year Built -- Test Set')
plt.xlabel('Year Built')
plt.ylabel('Sale Price')
plt.show()
{% endhighlight %}

![p on year train](https://user-images.githubusercontent.com/102122956/174856791-3e7fda92-93c6-4018-9279-48e2024c535f.png)

![price on y test](https://user-images.githubusercontent.com/102122956/174857433-95307911-6565-4008-8c13-85609829fdd1.png)

It looks like there's a slight positive influence of year built on sale price, but want more confirmation that our trained regression line fits our test data. Let's call the OLS estimator module, and score the models performance.

{% highlight python %}
lryp.score(x_test, y_test)
>>>0.024305825696060768
{% endhighlight %}

Yikes. This model is only correct 2% of the time!

{% highlight python %}
x_stat=sm.add_constant(x_train)
lrypsum=sm.OLS(y_train, x_stat).fit()
lrypsum.summary()
{% endhighlight %}

It looks like year built doesn't have any sizeable influence on home price in a univariate model. Let's move on to exploring other univariate relationships.


#### Simple Linear Regression of Sale Year on Price of Listings Sold for Clients

For this inquiry, I'm going to start by looking at a boxplot of Year and Sale Price, only for listings in which ny client represented sellers (i.e., for listings she was responsible for selling for clients). 

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












