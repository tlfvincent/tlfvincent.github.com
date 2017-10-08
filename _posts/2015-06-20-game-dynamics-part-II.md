---
layout:     post
title: "Flow and Dynamics in NBA games - Part II"
subtitle:   "A look from 2001 to now"
date:       2015-06-12
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


In part I of this post series on NBA game dynamics, I explored the average score differential that teams faced over the course of games. Interestingly, this proved to not be a very reliable proxy for overall performance and number of wins accrued over an entire season. To continue, I decided to look at *lead propensity*, which measures the proportion of time during which a team is in the lead. More formally, this is defined as:

$$LeadPropensity = \frac{\sum_{t=1}^{T} (\mathbf{1}_0 \colon D \to \{ 0,1 \}) }{T}$$

where $$T$$ is the total number of seconds played during NBA game, $$D$$ is the score difference defined as $$D = S_{t} - O_{t}$$, with $$S_{t}$$ and $$O_{t}$$ being the scores at time $$t$$ for the home and away team, respectively, and where $$\mathbf{1}_0(D)$$ is the indicator function such that:

$$
\mathbf{1}_0(D) :=
\begin{cases} 
1 \text{ if } D = S_{t} - O_{t} > 0, \\
0 \text{ if } D = S_{t} - O_{t} < 0.
\end{cases}
$$

<p align="justify">
Using the definition above, I looked at lead propensity scores for teams playing at home and away over the course of 2001 to 2014. The heatmap below, generated using the newly-released (and awesome) <a href="https://github.com/rstudio/d3heatmap">d3heatmap R library</a>, shows the percentage of time during which each was in the lead when playing at home. Again, we see that the Antonio Spurs is the most consistent team between 2001-2014. It is also interesting to observe how the amount of time in a game that teams spend in the lead is directly related to how well they performed during the season. Most teams with lead propensity scores at home ended doing very long runs in the playoffs during that year.
</p>

<center>
   <h4> Lead propensity scores for teams playing at home</h4>
  <iframe width="900" height="600" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/home_lead_prop.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>

<p align="justify">
The same observation holds for lead propensity scores of teams playing away. Again, San Anotnio remains a consistently excellent team, and strong lead propensity scores when playing away is synonymous with deep playoff runs during that given season.
</p>

<center>
  <h4> Lead propensity scores teams playing away</h4>
    <iframe width="900" height="600" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/away_lead_prop.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>

Finally, we can look at the differences in lead propensity scores when playing at home and away.

<center>
  <h4> Difference in lead propensity scores when playing at home and away</h4>
    <iframe width="900" height="600" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/lead_prop_diff.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>

