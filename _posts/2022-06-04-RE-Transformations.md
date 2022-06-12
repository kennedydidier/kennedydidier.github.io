---
layout: post
title: Real Estate Data Transformations
categories: [real estate, python, data exploration]
---

This is part of a project I completed for Cindy Schwall, a top real estate agent in Westchester. My first objective was to track "unconventional" KPIs --- basically, anything outside of sales volume, the traditional metric of interest for agents and brokerages. We worked together to identify what some of these might be and which we could actually track given the available data Cindy had stored from past sales. We came up with a few that I would start looking at -- including the graphical distribution of listings and purchases, how the average price of her listings has changed over time, and the average percentage increase or decrease from list to sale.
<!--more-->

This small project demonstrates how I explored and transformed the data provided to me by my client so that I would be able to adequately analyze it.

#### I. Basic Dimensions
{% highlight python %}
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')

dat=pd.read_csv("~/listingdata.csv")
dat
dat.shape
dat.columns
dat.info()
dat.describe()
dat.isnull().sum()
{% endhighlight %}

It looks like most of the data is complete. I have columns for address, city, zipcode, sale year, sale month, sale day, square feet, school district, days on the market, original list price, last list price, sale price, among others. There are there are several nulls in the columns _sale_brokerage_, _sale_agent_, _buying_agent_, and _buying_brokerage_, but it looks like Cindy has identified for each listing whether she represented the seller or the buyer. This is important, as the metrics we want to track differ by which she represented. 

My first tasks are to generate some new columns and alter some others so I can generate useful descriptive statistics for my client.

{% highlight python %}
dat['sale_month']=dat['sale_month'].apply(lambda x: '{0:0>2}'.format(x))
dat['sale_day']=dat['sale_day'].apply(lambda x: '{0:0>2}'.format(x))
dat['sale_date']=dat['sale_year'].map(str)+dat['sale_month'].map(str)+dat['sale_day'].map(str)
dat['sale_date']=pd.to_datetime(dat['sale_date'], format='%Y%m%d')
print(dat.dtypes)
dat['last_list_price']=dat['last_list_price'].str.replace(',', '')
dat['last_list_price']=dat['last_list_price'].astype(int)

print(dat)
dat['total_price_change']=dat.apply(lambda row: row.sale_price-row.original_price, axis=1)
dat['total_percent_price_change']=dat.apply(lambda row: (row.total_price_change/row.original_price) * 100, axis=1)
dat['onmarket_price_change']=dat.apply(lambda row: row.last_list_price-row.original_price, axis=1)
dat['percent_onmarket_price_change']=dat.apply(lambda row: (row.onmarket_price_change/row.original_price)* 100, axis=1)
dat['incontract_price_change']=dat.apply(lambda row: row.sale_price-row.last_list_price, axis=1)
dat['percent_incontract_price_change']=dat.apply(lambda row: (row.incontract_price_change/row.last_list_price) * 100, axis=1)

bought=dat[(dat.buying_agent == "Cindy Schwall") & (dat.sale_agent != "Cindy Schwall")]
bought.info()
sold=dat[(dat.sale_agent=="Cindy Schwall") & (dat.buying_agent != "Cindy Schwall")]
sold.info()
{% endhighlight %}

From this, I can see that for the last 100 listings my client handled, we have about equal numbers for which she represented the buyer and for which she represented the seller. I made a few new important variables and made the data fully usable for my exploration and analysis. Necessary transformations complete!

