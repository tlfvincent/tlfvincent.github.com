---
layout:     post
title: "Prison Locations around the USA"
date:       2015-09-09
author:     "Thomas Vincent"
header-img: "img/galaxy.jpg"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

I recently discovered the [enigma.io](http://enigma.io/) resource, a repository of freely available public with the following goals (as stated on their website):

```
The volume of data created by governments and businesses is growing exponentially. Organizations struggle just to store it all, let alone make sense of it. Enigma helps organizations and individuals fuse, organize, and explore data to make smarter decisions.​​
```

It is my intention to start playing around with a lot of the data they have made available, and I figured I would start easy and produce a map of prison locations around the USA (Full disclosure, this is inspired from another post I've seen, but I can't seem to find the original. if you have any idea what I am talking about then please send me the original so I can reference it fairly!).


<center>
   <h4>A map of USA prisons</h4>
  <iframe width="900" height="600" src="https://dl.dropboxusercontent.com/s/pqk6wa43zoq9sxv/prison_map_usa.html?dl=0" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>


The main point of this post is to illustrate how simple it is to produce interactive leaflet maps using the leaflet and htmlwidgets package in R. in fact, it is only a single line of code to produce the map below, the rest is just processing the data into the correct format. A word of advice when using the leaflet package at the zipcode level: the library expects 5 digits zipcode, while many databases produce zipcodes that sometimes contain 4 digits. If that happens, it is necessary to pad "0"s to the zipcode until the string in question has a length of five. This can be achieved using a neat little trick that is shown in line 11 of the code below!

{% highlight R linenos %} 
library(dplyr)
library(data.table)
library(leaflet)
library(htmlwidgets)

# read in prison details and zip coordinates
prisons <- fread(input="enigma-enigma.prisons.all-facilities.csv")
zip <- fread(input="zip_code_database.csv")

# pad zip code with zeros
zip[, 'zip_code' := sprintf("%05d", zip)]

# remove trailing zip codes
prisons[, 'zip_code' := unlist(lapply(strsplit(prisons$zip, split='-'), function(x) x[1]))]

# merge coordinates to prison details
df <- merge(zip[, list(zip_code, latitude, longitude)],
      prisons[, list(zip_code, type, facility_name, facility_address1, city, state)], by='zip_code')

# add popup information
df[, 'popup_info' := paste(sep = "<br/>", paste0("<b>", facility_name, "</b>"), facility_address1, city, state)]

# plot prison locations on map
p <- leaflet(df) %>% 
  addTiles() %>%
  addCircleMarkers(lng = ~longitude, 
                   lat = ~latitude, 
                   radius = 5, 
                   color = "red",
                   stroke=FALSE,
                   fillOpacity = 0.5,
                   popup=~popup_info)

saveWidget(p, file='prison_map_usa.html')
{% endhighlight %}

