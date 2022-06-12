---
layout: post
title: Real Estate Data Exploration
categories: [python, data exploration, real estate]
---

{% highlight python %}

dat['sale_price'].describe()
dat['total_price_change'].describe()
dat['total_percent_price_change'].describe()
dat['onmarket_price_change'].describe()
dat['percent_onmarket_price_change'].describe()
dat['incontract_price_change'].describe()
dat['percent_incontract_price_change'].describe()

plt.hist(x, bins=10, color='pink', edgecolor='black', linestyle='-', alpha=0.5)
plt.title("Sales Per Year")
plt.show()
{% endhighlight %}

![Sales Per Year Graph](https://user-images.githubusercontent.com/102122956/173245459-8de0d3c6-46a6-42c7-be77-37aa303689a6.png)


{% highlight python %}
sold.groupby('sale_year', as_index=False)['sale_price'].mean()
sns.barplot('sale_year', 'sale_price', data=sold, ci=False)
plt.title("Average Price of Listings per Year")
plt.show()
{% endhighlight %}
![average listing price per year](https://user-images.githubusercontent.com/102122956/173246371-5adb8cbe-b188-4586-98d1-69d313838cb9.png)




