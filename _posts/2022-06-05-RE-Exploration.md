---
layout: post
title: Real Estate Data Exploration
categories: [python, data exploration, real estate]
---

After transforming my client's data appropriately, I now want to produce some descriptive analytics for her sales portfolio. Based on our conversations, I knew the first things I would want to look into were:
1. how many listings my client handled per year
      1. in how many she represented the seller 
      2. in how many she represented the buyer
 2. the average price of listings in which she represented the seller per year
 3. the average price of listings in which she represented the buyer per year

{% highlight python %}

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from tabulate import tabulate
dat['sale_price'].describe()
dat['total_price_change'].describe()
dat['total_percent_price_change'].describe()
dat['onmarket_price_change'].describe()
dat['percent_onmarket_price_change'].describe()
dat['incontract_price_change'].describe()
dat['percent_incontract_price_change'].describe()

x=dat['sale_year']
plt.hist(x, bins=10, color='pink', edgecolor='black', linestyle='-', alpha=0.5)
plt.title("Total Sales Per Year")
plt.ylabel("Number of Listings Handled")
plt.xlabel("Year")
plt.show()
{% endhighlight %}

![total sales per year](https://user-images.githubusercontent.com/102122956/173248884-e50af173-a6c6-468b-aa40-2a315c67209b.png)

{% highlight python %}
x=sold['sale_year']
plt.hist(x, bins=10, color='orange', edgecolor='black', linestyle='-', alpha=0.5)
plt.title("Listings Sold Per Year")
plt.ylabel("Number of Listings Sold")
plt.xlabel("Year")
plt.show()
{% endhighlight %}

![listings sold per year](https://user-images.githubusercontent.com/102122956/173249263-62a97339-c4fa-492e-8d00-c950b5d3ae43.png)

{% highlight python %}
x=bought['sale_year']
plt.hist(x, bins=10, color='green', edgecolor='black', linestyle='-', alpha=0.5)
plt.title("Listings Purchased Per Year")
plt.ylabel("Number of Listings Purchased")
plt.xlabel("Year")
plt.show()
{% endhighlight %}
![listings purchased per year](https://user-images.githubusercontent.com/102122956/173249377-fcbd025a-5ad4-4d07-a72a-f5b5c72046ed.png)



{% highlight python %}
sold.groupby('sale_year', as_index=False)['sale_price'].mean()
sns.barplot('sale_year', 'sale_price', data=sold, ci=False)
plt.title("Average Price of Listings Sold per Year")
plt.show()
{% endhighlight %}
![avg price listings sold per year](https://user-images.githubusercontent.com/102122956/173249650-24c83071-9f09-4b51-9c88-3310323170c1.png)


{% highlight python %}
bought.groupby('sale_year', as_index=False)['sale_price'].mean()
sns.barplot('sale_year', 'sale_price', data=bought, ci=False)
plt.title("Average Price of Listings Purchased Per Year")
plt.show()
{% endhighlight %}

![average price of listings purchased](https://user-images.githubusercontent.com/102122956/173249620-f2b74724-edcb-4a4d-badb-8ce9beb241a0.png)

Now that I have produced those first graphs and made them client-accessible, I want to generate a few more metrics for my client to give her a thorough overview of her portfolio.

{% highlight python %}
sold_mean_table=sold[['days_on_market', 'sale_price', 'price_per_sqfoot', 'original_price', 'last_list_price', 'total_price_change', 'total_percent_price_change', 'onmarket_price_change', 'percent_onmarket_price_change', 'incontract_price_change', 'percent_incontract_price_change', 'sq_feet', 'bedrooms']].mean()
print(sold_mean_table.to_markdown())

bought_mean_table=bought[['days_on_market', 'sale_price', 'price_per_sqfoot', 'original_price', 'last_list_price', 'total_price_change', 'total_percent_price_change', 'onmarket_price_change', 'percent_onmarket_price_change', 'incontract_price_change', 'percent_incontract_price_change', 'sq_feet', 'bedrooms']].mean()
print(bought_mean_table.to_markdown())

sold[sold['sale_price']==sold['sale_price'].max()]
bought[bought['sale_price']==bought['sale_price'].max()]
sold[sold['sale_price']==sold['sale_price'].min()]
bought[bought['sale_price']==bought['sale_price'].min()]
{% endhighlight %}

Table for Listings Sold          | Average        |
---------------------------------|----------------|
 Days on Market                  |    88.8269     |
 Sale Price                      |   $1513942.308 |
 Sale Price Per Sq.Ft            | $410.187/sq.ft |
 Original Price                  |   $1589942.308 |
 Last List Price                 | $1531498.077   |
 Total Price Change ($)          | -$76000.0000   |
 Total Price Change (%)          |   -3.0297%     |
 On Market Price Change ($)      |     -$58444.23 |
 On Market Price Change (%)      |     -2.768     |
 In Contract Price Change ($)    |     -$17555.77 |
 In Contract Price Change (%)    |     -.4015     |
 Square Feet                     | 3819.808 sq.ft | 
 Bedrooms                        |        4.615   |  

  
  
  

Table for Listings Purchased      | Average        | 
----------------------------------|----------------|
 Days On Market                   |    68.0417     |
 Sale Price                       |  $1442479.146  |
 Sale Price Per Sq.Ft             | $431.1367/sq.ft|
 Original Price                   |   $1508174.979 |
 Last List Price                  | $1454445.813   |
 Total Price Change ($)           | -$65695.8000   |
 Total Price Change (%)           | -2.6613%       | 
 On Market Price Change ($)       |     -$53729.2  |
 On Market Price Change (%)       |       -2.903%  |
 In Contract Price Change ($)     |       -11966.7 |
 In Contract Price Change (%)     |       0.154%   |
 Square Feet                      | 3397.69 sq.ft  |
 Bedrooms                         | 4.375          |

So now I have a few graphs and a table of averages for her separated by listings in which she represented the buyer and listings in which she represented the seller. These calculations give my client a thorough, broad-stroked overview of her sales portfolio, before we get into looking at the graphical distribution of her listings and start building predictive models. Onto the next step!

