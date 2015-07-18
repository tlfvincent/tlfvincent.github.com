---
layout:     post
title: "Flow and Dynamics in NBA games - Part III"
subtitle:   "A look from 2001 to now"
date:       2015-06-29
author:     "Thomas Vincent"
header-img: "img/galaxy.jpg"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

<style type="text/css">
/*body{font-family: Arial, sans-serif;font-size:10px;}*/
  .axis path,.axis line {
    fill: none;
    stroke:#b6b6b6;
    shape-rendering: crispEdges;
  }
  /*.tick line{fill:none;stroke:none;}*/
  .tick text{
    fill:#999;
    font-size:14px;
  }

  g.journal.active{
    cursor:pointer;
  }

  text.label{
    font-size:12px;
    font-weight:bold;
    cursor:pointer;
  }

  text.value{
    font-size:12px;
    font-weight:bold;
  }
</style>


In part I and II of this post series on NBA game dynamics, I explored both the average score differential and proportion of time spent in the lead for each NBA team. Interestingly, this proved to not be a very reliable proxy for overall performance and number of wins accrued over an entire season. To continue, I decided to look at *scoring streaks* patterns. Here, I simply extracted and averaged the maximal scoring streaks that teams achieved during the course of a full season. Given that my data was stored in a datatable in the following format:

{% highlight css %} 
GAME_ID      YEAR HOME    AWAY    QUARTER TIME   SCORE seconds away  home   score
                  TEAM    TEAM                         (in s)  score score  diff
201310290IND 2014 Indiana Orlando 1       11:39.0 0-0   21     0     0      0
201310290IND 2014 Indiana Orlando 1       11:30.0 0-2   30     0     2      2
201310290IND 2014 Indiana Orlando 1       11:15.0 0-2   45     0     2      2
201310290IND 2014 Indiana Orlando 1       11:14.0 0-2   46     0     2      2
201310290IND 2014 Indiana Orlando 1       11:03.0 0-2   57     0     2      2
{% endhighlight %}

The actual function that I used to find the longest streak was:

{% highlight R linenos %} 
'find_longest_streak' <- function(vec) {
  streaks <- c()
  current <- 0
  for(i in 2:length(vec)) {
    if(abs(vec[i]) >= abs(vec[i-1])) {
      current <- current + (abs(vec[i]) - abs(vec[i-1]))
    }
    else{
      streaks <- c(streaks, current)
      current <- 0
    }
  }
  return(max(streaks))
{% endhighlight %}


<p align="justify">
Using the above, I looked at scoring streaks for teams playing at home and away over the course of the 2001-2014 seasons. The heatmap below, generated using the <a href="https://github.com/rstudio/d3heatmap">d3heatmap R library</a> shows the average maximal scoring streak per game for teams playing at home. This shows that the LA Clippers is the team that goes on the longest scoring streaks. What is most intriguing is that scoring streaks appear to be longer now than they were perhaps 10 years ago.
</p>

<center>
   <h4> Average maximal scoring streak per game for teams playing at home</h4>
  <iframe width="900" height="600" src="https://dl.dropboxusercontent.com/s/wemn7zcx1r4wtno/home_scoring_streak.html?dl=0" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>

<p align="justify">
The same observation holds for lead propensity scores of teams playing away: scoring streaks appear to be longer now than they were perhaps 10 years ago.
</p>

<center>
  <h4> Average maximal scoring streak per game teams playing away</h4>
    <iframe width="900" height="600" src="https://dl.dropboxusercontent.com/s/1yz2s7mhp7qctn4/away_scoring_streak.html?dl=0" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>

Finally, we can look at the differential in average maximal scoring streak per game when teams play at home or away.

<center>
  <h4> Difference in maximal scoring streaks per game when playing at home and away</h4>
    <iframe width="900" height="600" src="https://dl.dropboxusercontent.com/s/gz108h9hmndeten/scoring_streak_diff.html?dl=0" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>
