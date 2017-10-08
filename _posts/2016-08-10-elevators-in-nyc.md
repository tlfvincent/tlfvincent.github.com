---
layout:     post
title: "A map of elevators in NYC"
date:       2016-08-31
author:     "Thomas Vincent"
header-img: "img/galaxy.jpg"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

Not too long ago, I came across a random tweet pointing to a [GitHub repository](https://github.com/datanews) full of miscallaneous datasets. I was imemdiately excited by the various data science and visualization problems tha I could take on and immediately decided to get my hands dirty. One of the first datasets that I saw was a dataset called `eleavtors`. As stated on the github repo, it contains

```
A list of registered elevator devices in New York City provided by the Department of Buildings in response to a September 2015 FOIL request. The data was received on a DVD in November 2015. The spreadsheet contains information on 76,088 elevators in New York City.
```

As a resident of NYC, and someone who has mayny times complained abut going to friends houses with no elevators (admittedly a first-world problem...), I wanted to take a look at that data. (Full disclosure, half-way through this little project, I realized that fivethirtyeight had already beaten me to the punch and performed an [analysis of its own](http://fivethirtyeight.com/features/new-yorks-elevators-define-the-city/).

<center>
   <h4>A map of elevators in NYC</h4>
  <iframe width="900" height="600" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/elevator_map_nyc.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>


The main point of this post is to illustrate how simple it is to produce interactive leaflet map using the `leaflet` and `htmlwidgets` package in `R`. in fact, it is only a single line of code to produce the map above, the rest is just processing the data into the correct format. A word of advice when using the `leaflet` package at the zipcode level: the library expects 5 digits for all US-based zipcode, while many databases produce zipcodes that sometimes contain 4 digits. If that happens, it is necessary to pad "0"s to the zipcode until the string in question has a length of five. This can be achieved using a neat little trick that is shown in line 11 of the code below!

{% highlight python %}
library(dplyr)
library(data.table)
library(leaflet)
library(htmlwidgets)

# read in prison details and zip coordinates
# load required libraries
library(data.table)
library(rCharts)
library(dplyr)
library(leaflet)

# read in source data and process
elevators_dt <- fread('nyc_elevators.csv')
elevators_dt <- elevators_dt[elevators_dt[['STREET_NAME']] != 'DUMMY RECORD', ]

# define function to extract zip code of elevators
extract_zip <- function(x) {
    if(nchar(x) == 9) {
        return(substring(as.character(x), 1, 5))
    }
    else{
        return(x)
    }
}

# count frequency of elevators by zip code and coordinates
zip_cnt <- elevators_dt[, .N, by=ZIP_CODE]
zip_cnt[, 'zip' := unlist(sapply(zip_cnt$ZIP_CODE, function(x) extract_zip(x)))]
coord_pairs <- elevators_dt[, .N, by=list(LATITUDE, LONGITUDE)]

# generate interactive leaflet map of elevators in NYC
p <- leaflet(coord_pairs) %>%
     addTiles('http://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}.png', 
     attribution='Map tiles by <a href="http://stamen.com">Stamen Design</a>,
     <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> Map data
     <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>') %>% 
     setView(-73.977896, 40.731724, zoom = 12) %>% 
     addCircles(~LONGITUDE, ~LATITUDE, weight = 3, radius=5, color="#ffa500", stroke = TRUE, fillOpacity = 0.8) %>% addLegend("bottomright", colors= "#ffa500", labels="Dunkin'", title="In Connecticut")

saveWidget(p, file='elevator_map_nyc.html')
{% endhighlight %}


