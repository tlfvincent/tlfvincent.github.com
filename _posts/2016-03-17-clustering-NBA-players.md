---
layout:     post
title: "Player and roster similarity in the NBA"
date:       2016-03-17
author:     "Thomas Vincent"
header-img: "img/galaxy.jpg"
---

<style>
.center-image
{
    margin: 0 auto;
    display: block;
}
</style>

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>


___


Recently, professional sports associations and teams have made big strides towards leveraging data to inform both personel and on-the-field decision making. While the four major leagues (NBA, NFL, MLB, NHL) vary in terms of where they are in that process, most people would argue that the NBA is at the forefront of this movement. If you have never heard of SportVu before, they are a company that has partnered with the NBA to "utilize a six-camera system installed in basketball arenas to track the real-time positions of players and the ball 25 times per second. Utilizing this tracking data, SportVu is able to create a wealth of innovative statistics based on speed, distance, player separation and ball possession." As stated, the release of aggregated SportsVu data has offered brand new insights into how the game of basketball is played, and more importantly, how each individual plays the game.

In this post, I looked at the SportVu data available for all NBA players active during the 2014-2015 season. More particularly, I was interested in finding out whether SportVu data could be leveraged to discover players with similar playing styles, but also to discover teams with similar rosters. To begin, I started off by writing a quick Python script to scrape SportsVu data from [http://www.stats.com/sportvu/sportvu-basketball-media/](stats.nba.com)

### Collections and scraping the data

{% highlight python %}
# import required libraries for scraping and analysis

import urllib2
import json
import pandas as pd
from time import time
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import offsetbox
from sklearn.decomposition import TruncatedSVD
from sklearn import (manifold, datasets, decomposition, ensemble, lda,
                     random_projection)
{% endhighlight %}


{% highlight python %}
# define endpoints from stats.nba.com that we wish to scrape

addressList = {
    "pullup_address": "http://stats.nba.com/js/data/sportvu/2014/pullUpShootData.json",
    "drives_address": "http://stats.nba.com/js/data/sportvu/2014/drivesData.json",
    "defense_address": "http://stats.nba.com/js/data/sportvu/2014/defenseData.json",
    "passing_address": "http://stats.nba.com/js/data/sportvu/2014/passingData.json",
    "touches_address": "http://stats.nba.com/js/data/sportvu/2014/touchesData.json",
    "speed_address": "http://stats.nba.com/js/data/sportvu/2014/speedData.json",
    "rebounding_address": "http://stats.nba.com/js/data/sportvu/2014/reboundingData.json",
    "catchshoot_address": "http://stats.nba.com/js/data/sportvu/2014/catchShootData.json",
    "shooting_address": "http://stats.nba.com/js/data/sportvu/2014/shootingData.json"
    }

# download and format SportVu data, data for each endpoint is placed in a dictionnary

sportsvu_data = {}
for key, val in addressList.items():
    print key
    url = val
    response = urllib2.urlopen(url)
    raw = response.read()
    data = json.loads(raw)
    headers = data['resultSets'][0]['headers']
    table = data['resultSets'][0]['rowSet']
    df = pd.DataFrame(table)
    df.columns = headers
    df = df.set_index(['PLAYER_ID', 'TEAM_ABBREVIATION'])
    df.columns = ['{}_{}'.format(key, x) for x in df.columns]
    sportsvu_data[key] = df
{% endhighlight %}

{% highlight python %}
# concatenate all sportsvu data

sportvu = None
for suffix, input_df in sportsvu_data.items():
    print suffix
    if sportvu is None:
        sportvu = input_df
    else:
        sportvu = pd.merge(sportvu,
                           input_df,
                           how='inner',
                           left_index=True,
                           right_index=True)
{% endhighlight %}

{% highlight python %}
# filter out dataframe to players who have had relevant playing time

df = sportvu
df = df[df.pullup_address_GP >= 41]
df = df[df.pullup_address_MIN >= 15]

# set index of final dataframe

df_final = df.T.drop_duplicates().T.reset_index()
df_final = df_final.set_index(['PLAYER_ID', 'TEAM_ABBREVIATION', 'pullup_address_PLAYER'])

# remove those columns because they are redundant

cols_to_remove = ['pullup_address_FIRST_NAME', 'pullup_address_LAST_NAME',
                  'pullup_address_GP', 'pullup_address_MIN']
df_final.drop(cols_to_remove, axis=1, inplace=True)

# remove duplicate columns

df_final = df.T.drop_duplicates().T
{% endhighlight %}

There is nothing extraordinary in the code above, I essentially scraped some data from publicly (I hope) available data, and after a small clean-up, concatenated all the data into a single Pandas dataframe. The only thing of note is that I restricted the data collection to players who averaged at least 15 minutes per game and played in at least half the games in the season. In total, this leaves us with 329 NBA players, each  with 80 unique Sportvu data points.

### Inferring and visualizing similarities between NBA players
With the data now in our hands (or RAM), we can proceed to the original intent of this blog post, which is finding players with the most similar playing styles. After some trial and error, I obtained the best results when computing the correlation matrix between Sportvu metrics for all players, and then applying the t-SNE dimensionality reduction algorithm. Roughly, t-SNE is considered to be useful because of its property to conserve the overall topology of the data, so that neighboring (i.e. similar) players are mapped to neighboring locations in a two-dimensional space (It is this property that makes it so amenable to image analysis). Other well-known clustering techniques such as k-means or MDS would also be adequate for this exercise, but I’ve had good fortune when using t-SNE, so am perhaps unwisely sticking to it here.

{% highlight python %}
# compute the correlation matrix between SportsVu metrics for all NBA players
# note: Pearson or Spearman showed little differences in results

df_final = df_final.convert_objects(convert_numeric=True)
df_final = df_final.astype(float)
corr = df_final.T.corr()
X = corr.as_matrix()
{% endhighlight %}

{% highlight python %}
# perform t-SNE dimensionality reduction and print to file

X_reduced = TruncatedSVD(n_components=10, random_state=0).fit_transform(X)
tsne = manifold.TSNE(n_components=2, perplexity=40, verbose=2)
X_tsne = tsne.fit_transform(X_reduced)

df = pd.DataFrame(X_tsne)
df['player'] = [x[2] for x in df_final.index]
df['team'] = [x[1] for x in df_final.index]
df.columns = ['x', 'y', 'player', 'team']
df.to_csv('tsne_clusters.csv', index=False)
{% endhighlight %}

The advantage of using t-SNE in this context is that we are effectively taking an unsupervised approach, with the hopes that we can infer natural groupings of players based on their Sportvu statistics. Now that the data has been processed, we can start to visualize it. From there on, I will proceed to some nasty context switching and use R (I love both R and Python but hate using both in a single projetc. However, I am justifying my decision on the fact that, despite recent progress for Python, R still currently has far better wrappers around JS/D3).


{% highlight R %}
# import required R libraries

library(data.table)
library(scatterD3)
library(d3heatmap)
library(ggplot2)
library(ggrepel)
library(htmlwidgets)

# read in tsne representations

dt <- fread('tsne_clusters.csv', header=TRUE)
dt <- dt[team!='TOTAL']
{% endhighlight %}

{% highlight R %}
# Use k-means to uncover natural clusters of players
# Compute sum of squares for different values of k

wss <- c()
for (i in 1:15) {
wss <- c(wss, sum(kmeans(dt[, list(x, y)], centers=i)$withinss))
}

# plot sum of squares as a function of cluster count in order
# to find the "elbow". Optimal cluster count was found to be 7

plot(1:15, wss, type="b",
     xlab="Number of Clusters",
     ylab="Within groups sum of squares")

# add cluster assigments to data.table object

cl <- kmeans(dt[, list(x, y)], centers=7)
positions <- c('Shooting Guard', 'Dynamic C/PF',
               'Slow C/PF', 'Dynamic SF/PF',
               'Slow SF/PF', 'Point Guard', 'Stretch C/PF')
dt[, 'cluster' := cl$cluster]
dt[, 'position' := positions[dt$cluster]]
{% endhighlight %}

{% highlight R %}
# plot and save interactive D3 scatter plot

tooltips <- paste("<strong>", dt$player,"</strong><br /><strong>", dt$team, "</strong><br />")
p <- scatterD3(x = dt$x,
          y = dt$y,
          lab = dt$player,
          col_var=dt$team,
          symbol_var=dt$position,
          point_opacity = 0.7,
          tooltip_text = tooltips,
          col_lab = "Team",
          symbol_lab = "Position Group",
          width=1000,
          height=1000)

saveWidget(p, file='nba_player_similarity.html')
{% endhighlight %}

The plot below shows the natural groupings of players, where the shape represents the cluster they belong to and the color represent their respective teams. Feel free to zoom, highlight certain teams or clusters (by hovering over the legends) and generally just playing around it. 

<center>
   <h4>NBA player similarity</h4>
  <iframe width="1200" height="1000" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/nba_player_similarity.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>


Upon investigation, we can see that this approach makes a lot of sense. For example, players such as Damian Lillard, Mario Chalmers, Eric Bledsoe or Derick Rose are very to each other in space. There are many other examples like this (Serge Ibaka and Lamarcus Aldridge; Jimmy Butler and Andre Iguodola) but it is interesting to note how the shooting and point guard have well-defined positions, whereas the Center, Power Forward and Small Forward positions show a lot more heterogeneity and complexity. There are some mis-assignments here and there but these tend to be on the boundary of clusters, which could be probably be fixed after some further optimization and tinkering of the cluster assignments. 

## Team roster similarity
We can also leverage the results obtained from the dimensionality reduction part to discover teams that share the most similar roster of players. Given two teams `X` and `Y` with players `[x1,..., xn]` and `[y1,...,yn]` respectively, one way of achieving this is through the following steps:

1. Select player from team X (say x1)
2. Compute point-to-point distance between player x1 and all players [y1,...,yn] in team Y
3. Select and record the minimum value between player x1 and all players [y1,...,yn] in team Y. We are effectively finding the player in team Y that is most similar to player x1
4. Repeat step 1 to 3 for all remaining players in team X and sum the total point distance to get a "distance" value between team X and Y

By summing distances between pairs of players that are most similar in each teams, we can then assume that pairs of teams with low total distance between one another have more similar rosters than pairs of teams with high total distance.

{% highlight R %}
# get point-point distance between teams

'point_distance' <- function(p1, p2) {
    dx = (p1[1] - p2[1])^2
    dy = (p1[2] - p2[2])^2
    return(sqrt(dx+dy))
}
{% endhighlight %}

{% highlight R %}
unique.teams <- sort(unique(dt$team))
roster_distance <- mat.or.vec(length(unique.teams), length(unique.teams))
row.names(roster_distance) <- unique.teams
colnames(roster_distance) <- unique.teams
for(i in 1:length(unique.teams)) {
  print(unique.teams[i])
  for(j in 1:length(unique.teams)) {
    t1 <- dt[dt$team == unique.teams[i]]
    t2 <- dt[dt$team == unique.teams[j]]
    sim <- apply(t1, 1, function(x) 
                            apply(t2, 1,
                              function(y) point_distance(as.numeric(x[1:2]), as.numeric(y[1:2]))))
    roster_distance[i, j] <- mean(apply(sim, 2, min))
  }
}
{% endhighlight %}

{% highlight R %}
p <- d3heatmap(roster_distance, colors = "Spectral", dendrogram="none")
saveWidget(p, file='nba_team_similarity.html')

{% endhighlight %}


<center>
   <h4>NBA team roster similarity</h4>
  <iframe width="800" height="800" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/nba_team_similarity.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>

