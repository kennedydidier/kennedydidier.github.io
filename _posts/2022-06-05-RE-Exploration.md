---
layout: post
title: Real Estate Data Exploration
categories: [python, data exploration, real estate]
---

After transforming my client's data appropriately, I now want to produce some descriptive analytics for her sales portfolio. Based on our conversations, I knew the first things I would want to look into were:
1. how many listings my client handled per year
      1. in how many she represented the seller 
      2. in how many she represented the buyer
 2. the average price of listings in which she represented the buyer per year
 3. the average price of listings in which she represented the seller per year
 <!--more->

{% highlight python %}

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

{% highight python %}
x=sold['sale_year']
plt.hist(x, bins=10, color='orange', edgecolor='black', linestyle='-', alpha=0.5)
plt.title("Listings Sold Per Year")
plt.ylabel("Number of Listings Sold")
plt.xlabel("Year")
plt.show()
{% endhighlight %}
![listings sold per year](https://user-images.githubusercontent.com/102122956/173249263-62a97339-c4fa-492e-8d00-c950b5d3ae43.png)

{% highlight pythong %}
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
plt.title("Average Price of Listings per Year")
plt.show()
{% endhighlight %}
![average listing price per year](https://user-images.githubusercontent.com/102122956/173246371-5adb8cbe-b188-4586-98d1-69d313838cb9.png)




