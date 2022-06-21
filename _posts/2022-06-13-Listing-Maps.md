---
layout: post
title: Mapping Client Listings
categories: python, mapping, dataexploration, realestate
---


After basic exploration, the first thing my client was interested in was the geographical distribution of her sales portfolio. My first step was finding the latitude and longitude coordinates for all of her addresses.

{% highlight python %}
import geopandas as gpd
import geopy
from geopy.geocoders import Nominatim
from geopy.extra.rate_limiter import RateLimiter
locator=Nominatim(user_agent="scarsgeocoding")
geocode=RateLimiter(locator.geocode, min_delay_seconds=1)
dat['location'] = dat[address'].apply(geocode)
dat['lat']=dat['lat'].apply(lambda x: x.latitude)
dat['lon']=dat['lon'].apply(lambda x: x.longitude)
{% endhighlight %}

Now that I have the latitude and longitude coordinates, my next step is to properly build a choropleth map -- my client is specifically interested in looking at how her listings are distributed throughout the neighborhoods of Scarsdale, NY. No GIS map is available in plotly or mapbox that delineates the neigborhood boundaries, so I first built the geometry in Geojson.io and then exported it to Mapbox Studio, where I properly colored, shaded, and labeled the map. Finally, I used the code below to build an interactive map for my client so she could view examine her data over the neighborhood map of Scarsdale I built. I decided to copy and rename some of the columns in my pandas dataframe for maximum client legibility. Building upon our previous discussions, I specifically highlighted only relevant and important information in my interactive map, making sure to not overload it with information but also to include everything she wanted to see.

{% highlight python %}
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
import numpy as np
import pandas as pd
import chart_studio
dat["form_date"]=dat["sale_date"].dt.strftime("%d-%B-%Y")
dat_copy=dat.copy()
dat_copy=dat_copy.rename(columns={'sale_price': 'Price', 'form_date': 'Date'})
scars="mapbox://styles/kennedydidier/cl4hh1tc1001u14pxk09j7fci"
token="pk.eyJ1Ijoia2VubmVkeWRpZGllciIsImEiOiJjbDRoZms0eTMwMHVlM2NydmoxbmEwcmZ6In0.TeL4oVCFAXCEWP5xSlKdZw"
fig=px.scatter_mapbox(dat_copy, lat="lat", lon="lon", color_discrete_sequence=["#1E90FF", "#00008B"], color="type", hover_name="address", hover_data={"type":False, "Price":True, "Date":True, "lon":False, "lat":False}, zoom=12, labels={"type": "Cindy Represented:"})

fig.update_layout(mapbox_style=scars, mapbox_accesstoken=token)
fig.show()
fig.write_html('listings.html')
username='kennedydidier'
api_key='nkAt8RK5svtVOCY1kjsz'
chart_studio.tools.set_credentials_file(username=username, api_key=api_key)
import chart_studio.plotly as py
py.plot(fig, filename = 'scars_listings', auto_open=True)
{% endhighlight %}

I exported it as web-embeddable for my client, and I embedded it below as well!

<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plotly.com/~kennedydidier/1.embed"></iframe>
