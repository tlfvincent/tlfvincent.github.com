---
layout:     post
title: "Flow and Dynamics in NBA games - Part I"
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


<p align="justify">
I have been interested in the game flow and dynamics of NBA games for a while, and so have decided to start a series of blog posts that will hopefully culminate in a web app that processes and displays game flows for present and historical games. Before that, however, I wanted to sink my teeth into the data and run a few analysis that may be of interest.
</p>

<p align="justify">
To start of with, I wrote a Python script that scraped all available play-by-play data for all games played between the seasons 2001-2014. The output was stored in a SQL database of ~450Mb, which translate to a 7,364,787 x 8 matrix when read in memory. With the data now available for analysis, we can proceed toowards producing some vizualizations of the data. First, I set out to explore the score differential for each team, which I figured would be an interesting proxy for overall performance. In this blog post, I looked at the average number of points that teams were ahead/behind when they were playing at home or away.
</p>

Here, the *score differential* attributed to a team during any given season was simply calculated as the average score difference observed at every scoring event. More formally, we can write this as:

$$ScoreDiff = \frac{\sum_{n=1}^{N} S_{n} - O_{n}}{N}$$

where $$N$$ is the total number of scoring events played by a team at home or away, $$S_{i}$$ and $$O_{i}$$ are the scores for the home and away team at scoring event $$n$$, respectively. Therefore, a positive $$ScoreDiff$$ indicates that a team tends to be in the lead, while a negative $$ScoreDiff$$ indicates that teams tend to be behind. The first chart below shows the average score differential for teams playing on their home-court.

<center>
  <h4> Average score differential for teams when playing at home</h4>
  <div id="home_performance_diff" style="text-align:left"> </div>
</center>

<p align="justify">
 One cool thing about these plots is that they allow to quickly see which teams have been consistently excellent at home. For example, we can see that the San Antonio Spurs and Dallas Mavericks are two teams that have achieved positive score differentials all the way from 2000 to 2014 when playing at home. Could it be a Texas thing?
</p>

<center>
  <h4> Strong performance at home for the Spurs and Mavericks </h4>
  <div id="home_performance_diff_best" style="text-align:left"> </div>
</center>


<p align="justify">
Next, we can look at the score differentials achieved by teams playing away. In this case, we see that San Antonio is again the team that plays the best when away from home. Of the historically worst teams out there, we have New York, Toronto, Golden State and Utah.
</p>

<center>
  <h4> Average score differential for teams playing away</h4>
  <div id="away_performance_diff" style="text-align:left"> </div>
</center>

<p></p>

<center>
  <h4> Worst performing teams away </h4>
  <div id="away_performance_diff_worst" style="text-align:left"> </div>
</center>



<p align="justify">
The charts below show the advantages that steams from teams playing on their home-court. Surisingly, the average score differential per season (both away and at home) was not correlated to how many wins were achieved by each team. This is clearly shown in the two plots below. I would guess that this is the due to the fact that many teams go on signifcant runs that may actually superseed the fact that they are not necessarily in the lead all the time.
</p>

<br>

<center>
  <h4> Correlation between home score differential and win shares </h4>
  <iframe width="900" height="600" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/win_share_home_score_diff.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>

<center>
  <h4> Correlation between away score differential and win shares </h4>
  <iframe width="900" height="600" src="https://statofmind-blog.nyc3.digitaloceanspaces.com/images/win_share_away_score_diff.html" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
</center>


<p align="justify">
In this first part, I have looked at the average number of points that teams lead or trail across all games played in a season. I have actually seen other examples online of gameflow charts that typically display score differential during the course of a game. While this method of looking a game flow is interesting, it is somewhat limited. In the upcoming part of this blog series, I will look at scoring streaks, proportion of time in the lead, and largest leads.
<p>

<script src="http://d3js.org/d3.v3.min.js"></script>

<script>

var data = {
     "Atlanta": [
     {
     "2001": -0.26564,
    "2002": -0.63928,
    "2003": 0.94595,
    "2004": 1.2014,
    "2005": -3.4304,
    "2006": -0.77673,
    "2007": 0.46483,
    "2008": 1.5843,
    "2009": 1.4461,
    "2010": 4.9457,
    "2011": -0.82805,
    "2012": 2.8803,
    "2013": 0.7723,
    "2014": 0.4438 
    } 
    ],
    "Boston": [
     {
     "2001": 0.18049,
    "2002":  2.344,
    "2003": 1.8546,
    "2004":  1.161,
    "2005": 1.6127,
    "2006": 0.54516,
    "2007": -0.95298,
    "2008": 8.3661,
    "2009": 5.4739,
    "2010": 3.2664,
    "2011": 4.6449,
    "2012": 4.6785,
    "2013": 3.3728,
    "2014": 0.32041 
    } 
    ],
    "Brooklyn": [
     {
     "2001":      0,
    "2002":      0,
    "2003":      0,
    "2004":      0,
    "2005":      0,
    "2006":      0,
    "2007":      0,
    "2008":      0,
    "2009":      0,
    "2010":      0,
    "2011":      0,
    "2012":      0,
    "2013": 3.1962,
    "2014":  3.101 
    } 
    ],
    "Charlotte": [
     {
     "2001": 2.0742,
    "2002": 1.2219,
    "2003":      0,
    "2004":      0,
    "2005": -1.2372,
    "2006": -1.8558,
    "2007": 0.28925,
    "2008": -0.41828,
    "2009": 0.75044,
    "2010": 3.1491,
    "2011": 1.0351,
    "2012": -6.4556,
    "2013": -2.901,
    "2014": 2.7033 
    } 
    ],
    "Chicago": [
     {
     "2001": -2.2721,
    "2002": -3.8983,
    "2003": 0.57644,
    "2004": -1.3278,
    "2005": 2.7702,
    "2006": 1.0145,
    "2007": 6.5983,
    "2008": 1.3453,
    "2009": 0.50177,
    "2010": 0.30935,
    "2011": 4.5846,
    "2012": 3.9405,
    "2013": 0.62501,
    "2014": 2.9539 
    } 
    ],
    "Cleveland": [
     {
     "2001": -0.20432,
    "2002": -0.94846,
    "2003": -3.2661,
    "2004": 0.72241,
    "2005": 3.9086,
    "2006": 3.7839,
    "2007": 4.3962,
    "2008": 1.1813,
    "2009": 8.9303,
    "2010":  5.306,
    "2011": -3.6485,
    "2012": -5.2364,
    "2013": -1.5885,
    "2014": 0.46758 
    } 
    ],
    "Dallas": [
     {
     "2001": 3.4838,
    "2002": 3.0447,
    "2003": 6.6566,
    "2004": 5.0374,
    "2005": 4.1149,
    "2006":  5.639,
    "2007": 6.3717,
    "2008": 5.6607,
    "2009": 3.5123,
    "2010": 1.3395,
    "2011": 3.8508,
    "2012": 2.7928,
    "2013": 0.9314,
    "2014": 2.5114 
    } 
    ],
    "Denver": [
     {
     "2001": 2.3021,
    "2002": -1.5465,
    "2003": -2.4471,
    "2004": 3.5163,
    "2005": 3.7967,
    "2006": 2.3859,
    "2007":  3.587,
    "2008": 5.9001,
    "2009": 4.3735,
    "2010": 4.2932,
    "2011": 5.4632,
    "2012": 2.5523,
    "2013": 5.2038,
    "2014": 0.5662 
    } 
    ],
    "Detroit": [
     {
     "2001": 0.46706,
    "2002":  1.391,
    "2003": 3.1324,
    "2004": 4.0781,
    "2005": 3.3866,
    "2006": 5.6005,
    "2007": 1.8557,
    "2008": 6.8075,
    "2009": 1.2808,
    "2010": -2.2561,
    "2011": -0.10759,
    "2012": -0.26014,
    "2013": 0.84789,
    "2014": 0.99883 
    } 
    ],
    "Golden State": [
     {
     "2001": -3.2349,
    "2002": -1.8075,
    "2003":  1.216,
    "2004": 3.0505,
    "2005": -0.29099,
    "2006": 1.1455,
    "2007": 4.4257,
    "2008": 2.0604,
    "2009": 0.76395,
    "2010": -1.3772,
    "2011": 1.1319,
    "2012": -1.2525,
    "2013": 2.5296,
    "2014": 4.4266 
    } 
    ],
    "Houston": [
     {
     "2001": 1.3436,
    "2002": -1.3636,
    "2003": 3.5091,
    "2004": 3.0212,
    "2005": 2.4681,
    "2006": -0.086248,
    "2007": 5.6646,
    "2008": 6.4458,
    "2009": 5.0651,
    "2010": 2.0835,
    "2011": 3.4496,
    "2012": 2.8439,
    "2013":  3.476,
    "2014": 6.1252 
    } 
    ],
    "Indiana": [
     {
     "2001": 2.1488,
    "2002": 1.9171,
    "2003": 5.6203,
    "2004": 5.3885,
    "2005": 2.0517,
    "2006": 2.9882,
    "2007": 1.3252,
    "2008": 1.4673,
    "2009":  2.461,
    "2010": 0.4947,
    "2011": 2.9014,
    "2012": 3.2608,
    "2013": 4.0851,
    "2014":  4.003 
    } 
    ],
    "LA Clippers": [
     {
     "2001": -0.66586,
    "2002": 2.7273,
    "2003": -1.4388,
    "2004": -2.2918,
    "2005": 3.0404,
    "2006": 1.4282,
    "2007": 2.0594,
    "2008": -3.6282,
    "2009": -3.0485,
    "2010":  -1.35,
    "2011": 1.4289,
    "2012":  3.878,
    "2013": 5.6473,
    "2014": 5.8239 
    } 
    ],
    "LA Lakers": [
     {
     "2001":  4.512,
    "2002": 4.2256,
    "2003": 3.8161,
    "2004": 5.1605,
    "2005": 0.37145,
    "2006": 3.2253,
    "2007": 1.5342,
    "2008": 3.9324,
    "2009": 6.1375,
    "2010": 5.4532,
    "2011": 5.5155,
    "2012":  3.942,
    "2013":  2.587,
    "2014": -3.1736 
    } 
    ],
    "Memphis": [
     {
     "2001":      0,
    "2002": -2.3162,
    "2003": 1.4987,
    "2004": 2.7643,
    "2005": 3.3294,
    "2006": 4.2366,
    "2007": -1.9632,
    "2008": -2.2942,
    "2009": -1.1014,
    "2010": 2.0404,
    "2011": 3.3577,
    "2012":   4.03,
    "2013": 2.9581,
    "2014": 1.1555 
    } 
    ],
    "Miami": [
     {
     "2001": 2.7973,
    "2002": 0.19928,
    "2003": -0.62235,
    "2004": 2.8903,
    "2005": 4.7649,
    "2006": 3.4339,
    "2007": 1.5706,
    "2008": -4.208,
    "2009": 1.0751,
    "2010": 2.4039,
    "2011": 5.3285,
    "2012": 4.6088,
    "2013": 4.4347,
    "2014": 3.1585 
    } 
    ],
    "Milwaukee": [
     {
     "2001": 4.1481,
    "2002": 1.8504,
    "2003":  1.049,
    "2004": 1.7803,
    "2005": 1.3226,
    "2006": -0.070211,
    "2007": 0.25158,
    "2008": -1.1565,
    "2009":   1.79,
    "2010": 1.8686,
    "2011": 1.8129,
    "2012": -0.4389,
    "2013": 0.26572,
    "2014": -4.2088 
    } 
    ],
    "Minnesota": [
     {
     "2001": 4.1206,
    "2002": 5.9067,
    "2003":  4.272,
    "2004":  4.684,
    "2005": 1.6949,
    "2006": 2.4726,
    "2007": -0.59233,
    "2008": -1.3643,
    "2009": -3.7579,
    "2010": -4.4614,
    "2011": -1.6284,
    "2012": -1.3038,
    "2013": 0.76498,
    "2014":  4.946 
    } 
    ],
    "New Jersey": [
     {
     "2001": -0.61092,
    "2002": 5.6308,
    "2003": 6.2349,
    "2004": 4.1824,
    "2005": 1.9505,
    "2006": 3.2056,
    "2007": 1.3396,
    "2008": -1.2901,
    "2009": -1.3757,
    "2010": -4.1259,
    "2011": -2.5866,
    "2012": -3.8963,
    "2013":      0,
    "2014":      0 
    } 
    ],
    "New Orleans": [
     {
     "2001":      0,
    "2002":      0,
    "2003":  3.936,
    "2004": 2.0288,
    "2005": -4.0522,
    "2006":      0,
    "2007":      0,
    "2008":  3.294,
    "2009": 2.1572,
    "2010": -0.44129,
    "2011": 1.7558,
    "2012": -0.76487,
    "2013": -0.87334,
    "2014": 1.0214 
    } 
    ],
    "New Orleans/Oklahoma City": [
     {
     "2001":      0,
    "2002":      0,
    "2003":      0,
    "2004":      0,
    "2005":      0,
    "2006": 1.6388,
    "2007": -0.55942,
    "2008":      0,
    "2009":      0,
    "2010":      0,
    "2011":      0,
    "2012":      0,
    "2013":      0,
    "2014":      0 
    } 
    ],
    "New York": [
     {
     "2001": 3.7026,
    "2002": -0.23552,
    "2003": 1.0513,
    "2004": 0.31043,
    "2005": 0.76123,
    "2006": -3.6966,
    "2007": -1.4407,
    "2008": -2.0551,
    "2009": -0.35886,
    "2010": 0.21088,
    "2011": 0.87658,
    "2012": 4.3604,
    "2013": 4.9759,
    "2014": 0.62393 
    } 
    ],
    "Oklahoma City": [
     {
     "2001":      0,
    "2002":      0,
    "2003":      0,
    "2004":      0,
    "2005":      0,
    "2006":      0,
    "2007":      0,
    "2008":      0,
    "2009": -2.0377,
    "2010": 4.5588,
    "2011": 3.7341,
    "2012": 6.0354,
    "2013": 7.6407,
    "2014":  5.466 
    } 
    ],
    "Orlando": [
     {
     "2001": 1.8108,
    "2002": 4.4567,
    "2003": 3.1133,
    "2004": -2.6331,
    "2005": 1.0649,
    "2006": 2.0744,
    "2007": 2.9922,
    "2008": 4.2573,
    "2009": 6.6278,
    "2010": 6.6589,
    "2011": 4.5876,
    "2012": 4.0019,
    "2013": -3.974,
    "2014": -1.0177 
    } 
    ],
    "Philadelphia": [
     {
     "2001": 3.7164,
    "2002": 2.0909,
    "2003": 2.0906,
    "2004": -0.97926,
    "2005": 1.8697,
    "2006":  1.064,
    "2007": 0.32204,
    "2008":  2.042,
    "2009": 2.4661,
    "2010": -1.1503,
    "2011": 2.8155,
    "2012": 4.2025,
    "2013": -1.0217,
    "2014": -6.1873 
    } 
    ],
    "Phoenix": [
     {
     "2001":    1.9,
    "2002": 1.5266,
    "2003": 1.4162,
    "2004": -0.54378,
    "2005": 5.7328,
    "2006": 6.8134,
    "2007": 5.7144,
    "2008":  4.743,
    "2009": 3.8769,
    "2010": 5.3365,
    "2011": 0.62158,
    "2012": 1.4115,
    "2013": -2.4709,
    "2014": 3.0045 
    } 
    ],
    "Portland": [
     {
     "2001": 4.6453,
    "2002": 4.7767,
    "2003": 4.6444,
    "2004": 0.55549,
    "2005": -1.696,
    "2006": -3.2738,
    "2007": -1.3386,
    "2008":  1.777,
    "2009": 5.6956,
    "2010": 2.8011,
    "2011": 3.1449,
    "2012": 2.5659,
    "2013": -1.6862,
    "2014":  2.953 
    } 
    ],
    "Sacramento": [
     {
     "2001": 4.8232,
    "2002": 8.2723,
    "2003": 7.0801,
    "2004": 5.8166,
    "2005": 3.8502,
    "2006": 3.0566,
    "2007": 1.8959,
    "2008": 1.4103,
    "2009": -3.2842,
    "2010": 0.027994,
    "2011": -1.5157,
    "2012": -2.4935,
    "2013": -0.11836,
    "2014": -0.16395 
    } 
    ],
    "San Antonio": [
     {
     "2001": 7.5205,
    "2002": 7.1265,
    "2003": 6.1498,
    "2004":  7.533,
    "2005": 7.8189,
    "2006": 4.0508,
    "2007": 5.4129,
    "2008":  4.772,
    "2009": 3.2531,
    "2010": 5.3993,
    "2011": 5.6683,
    "2012": 6.3732,
    "2013": 6.0511,
    "2014": 6.0916 
    } 
    ],
    "Seattle": [
     {
     "2001": 2.2754,
    "2002": 3.2117,
    "2003": 2.6115,
    "2004": 0.22675,
    "2005": 1.9997,
    "2006": 1.5434,
    "2007": -0.20088,
    "2008": -3.0684,
    "2009":      0,
    "2010":      0,
    "2011":      0,
    "2012":      0,
    "2013":      0,
    "2014":      0 
    } 
    ],
    "Toronto": [
     {
     "2001": 1.5707,
    "2002":  2.515,
    "2003": -1.4301,
    "2004": -0.8679,
    "2005": -0.64048,
    "2006": -1.6079,
    "2007": 3.3587,
    "2008": 2.9544,
    "2009": 0.89179,
    "2010": 0.42737,
    "2011": -1.246,
    "2012": 0.80119,
    "2013": 1.6541,
    "2014": 2.4324 
    } 
    ],
    "Utah": [
     {
     "2001": 3.8782,
    "2002": 2.3887,
    "2003": 4.3547,
    "2004": 1.5621,
    "2005": -0.11415,
    "2006": -0.6908,
    "2007": 2.9033,
    "2008": 7.5152,
    "2009": 5.1758,
    "2010": 5.3265,
    "2011": 1.2948,
    "2012": 2.5319,
    "2013": 3.1573,
    "2014": -0.30944 
    } 
    ],
    "Vancouver": [
     {
     "2001": -1.4177,
    "2002":      0,
    "2003":      0,
    "2004":      0,
    "2005":      0,
    "2006":      0,
    "2007":      0,
    "2008":      0,
    "2009":      0,
    "2010":      0,
    "2011":      0,
    "2012":      0,
    "2013":      0,
    "2014":      0 
    } 
    ],
    "Washington": [
     {
     "2001": -2.6609,
    "2002": 1.9297,
    "2003": 2.3859,
    "2004": -0.23762,
    "2005": 2.5912,
    "2006": 3.8197,
    "2007": 1.9732,
    "2008": 2.1746,
    "2009": -2.3173,
    "2010": -1.7926,
    "2011": -1.7123,
    "2012": -1.345,
    "2013": 1.3574,
    "2014":  1.488 
    } 
  ]
};

var dataset = [];
for (var key in data) {
  if (data.hasOwnProperty(key)) {
    tmp = {};
    tmp['name'] = key;
    tmp['diff'] = [];
    //tmp['diff_abs'] = [];
    for(var subkey in data[key][0]) {
      tmp['diff'].push([subkey,
                        Math.abs(data[key][0][subkey]).toFixed(1),
                        data[key][0][subkey].toFixed(1)
                        ]);
      //tmp['diff'].push([subkey, data[key][0][subkey].toFixed(1)]);
    }
    dataset.push(tmp);
  }
}

function truncate(str, maxLength, suffix) {
  if(str.length > maxLength) {
    str = str.substring(0, maxLength + 1); 
    str = str.substring(0, Math.min(str.length, str.lastIndexOf(" ")));
    str = str + suffix;
  }
  return str;
}

var margin = {top: 20, right: 200, bottom: 0, left: 20},
  width = 650,
  height = 650;

var start_year = 2001,
  end_year = 2014;

var c = d3.scale.category20c();

var x = d3.scale.linear()
  .range([0, width]);

var xAxis = d3.svg.axis()
  .scale(x)
  .orient("top");

var formatYears = d3.format("0000");
xAxis.tickFormat(formatYears);

var svg = d3.select("#home_performance_diff").append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .style("margin-left", margin.left + "px")
  .append("g")
  .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

x.domain([start_year, end_year]);
var xScale = d3.scale.linear()
  .domain([start_year, end_year])
  .range([0, width]);

  svg.append("g")
    .attr("class", "x axis")
    .attr("transform", "translate(0," + 0 + ")")
    .call(xAxis);

  for (var j = 0; j < dataset.length; j++) {
    var g = svg.append("g").attr("class","journal");

    var circles = g.selectAll("circle")
      .data(dataset[j]['diff'])
      .enter()
      .append("circle");

    var text = g.selectAll("text")
      .data(dataset[j]['diff'])
      .enter()
      .append("text");

    var rScale = d3.scale.linear()
      .domain([0, d3.max(dataset[j]['diff'], function(d) { return d[1]; })])
      .range([2, 9]);

    circles
      .attr("cx", function(d, i) { return xScale(d[0]); })
      .attr("cy", j*20+20)
      .attr("r", function(d) { return rScale(d[1]); })
      // .style("stroke", function(d) { 
      //       if(d[2] < 0){return "red"}
      //       else {return "blue"};
      // })
      // .style("stroke-width", function(d) { return 3; })
      //.style("opacity", .8)
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      });
      //.style("fill", function(d) { return c(j); });

    text
      .attr("y", j*20+25)
      .attr("x",function(d, i) { return xScale(d[0])-5; })
      .attr("class","value")
      .text(function(d){ return d[2]; })
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      })
      //.style("fill", function(d) { return c(j); })
      .style("display","none");

    g.append("text")
      .attr("y", j*20+25)
      .attr("x",width+20)
      .attr("class","label")
      .text(truncate(dataset[j]['name'],30,"..."))
      .style("fill", function(d) { return c(j); })
      .on("mouseover", mouseover)
      .on("mouseout", mouseout);
  };

  function mouseover(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","none");
    d3.select(g).selectAll("text.value").style("display","block");
  }

  function mouseout(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","block");
    d3.select(g).selectAll("text.value").style("display","none");
  }

</script>





<script>

var data = {
    "Dallas": [
     {
     "2001": 3.4838,
    "2002": 3.0447,
    "2003": 6.6566,
    "2004": 5.0374,
    "2005": 4.1149,
    "2006":  5.639,
    "2007": 6.3717,
    "2008": 5.6607,
    "2009": 3.5123,
    "2010": 1.3395,
    "2011": 3.8508,
    "2012": 2.7928,
    "2013": 0.9314,
    "2014": 2.5114 
    } 
    ],
    "San Antonio": [
     {
     "2001": 7.5205,
    "2002": 7.1265,
    "2003": 6.1498,
    "2004":  7.533,
    "2005": 7.8189,
    "2006": 4.0508,
    "2007": 5.4129,
    "2008":  4.772,
    "2009": 3.2531,
    "2010": 5.3993,
    "2011": 5.6683,
    "2012": 6.3732,
    "2013": 6.0511,
    "2014": 6.0916 
    } 
    ]
};

var dataset = [];
for (var key in data) {
  if (data.hasOwnProperty(key)) {
    tmp = {};
    tmp['name'] = key;
    tmp['diff'] = [];
    //tmp['diff_abs'] = [];
    for(var subkey in data[key][0]) {
      tmp['diff'].push([subkey,
                        Math.abs(data[key][0][subkey]).toFixed(1),
                        data[key][0][subkey].toFixed(1)
                        ]);
      //tmp['diff'].push([subkey, data[key][0][subkey].toFixed(1)]);
    }
    dataset.push(tmp);
  }
}

function truncate(str, maxLength, suffix) {
  if(str.length > maxLength) {
    str = str.substring(0, maxLength + 1); 
    str = str.substring(0, Math.min(str.length, str.lastIndexOf(" ")));
    str = str + suffix;
  }
  return str;
}

var margin = {top: 20, right: 200, bottom: 0, left: 20},
  width = 650,
  height = 80;

var start_year = 2001,
  end_year = 2014;

var c = d3.scale.category20c();

var x = d3.scale.linear()
  .range([0, width]);

var xAxis = d3.svg.axis()
  .scale(x)
  .orient("top");

var formatYears = d3.format("0000");
xAxis.tickFormat(formatYears);

var svg = d3.select("#home_performance_diff_best").append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .style("margin-left", margin.left + "px")
  .append("g")
  .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

x.domain([start_year, end_year]);
var xScale = d3.scale.linear()
  .domain([start_year, end_year])
  .range([0, width]);

  svg.append("g")
    .attr("class", "x axis")
    .attr("transform", "translate(0," + 0 + ")")
    .call(xAxis);

  for (var j = 0; j < dataset.length; j++) {
    var g = svg.append("g").attr("class","journal");

    var circles = g.selectAll("circle")
      .data(dataset[j]['diff'])
      .enter()
      .append("circle");

    var text = g.selectAll("text")
      .data(dataset[j]['diff'])
      .enter()
      .append("text");

    var rScale = d3.scale.linear()
      .domain([0, d3.max(dataset[j]['diff'], function(d) { return d[1]; })])
      .range([2, 9]);

    circles
      .attr("cx", function(d, i) { return xScale(d[0]); })
      .attr("cy", j*20+20)
      .attr("r", function(d) { return rScale(d[1]); })
      // .style("stroke", function(d) { 
      //       if(d[2] < 0){return "red"}
      //       else {return "blue"};
      // })
      // .style("stroke-width", function(d) { return 3; })
      //.style("opacity", .8)
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      });
      //.style("fill", function(d) { return c(j); });

    text
      .attr("y", j*20+25)
      .attr("x",function(d, i) { return xScale(d[0])-5; })
      .attr("class","value")
      .text(function(d){ return d[2]; })
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      })
      //.style("fill", function(d) { return c(j); })
      .style("display","none");

    g.append("text")
      .attr("y", j*20+25)
      .attr("x",width+20)
      .attr("class","label")
      .text(truncate(dataset[j]['name'],30,"..."))
      .style("fill", function(d) { return c(j); })
      .on("mouseover", mouseover)
      .on("mouseout", mouseout);
  };

  function mouseover(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","none");
    d3.select(g).selectAll("text.value").style("display","block");
  }

  function mouseout(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","block");
    d3.select(g).selectAll("text.value").style("display","none");
  }

</script>






<script>

var data = 
{
 "Atlanta": [
 {
 "2001":  5.457,
"2002": 4.7178,
"2003": 4.0056,
"2004": 5.0922,
"2005": 6.6706,
"2006": 5.1063,
"2007": 5.8566,
"2008": 2.8736,
"2009": 3.0524,
"2010": 0.19465,
"2011": -0.75457,
"2012": -0.7692,
"2013": 1.2903,
"2014": 0.95591 
} 
],
"Boston": [
 {
 "2001": 2.3196,
"2002": -0.046343,
"2003": 1.5681,
"2004": 2.2438,
"2005": 1.1861,
"2006":  1.837,
"2007": 3.5833,
"2008": -4.0304,
"2009": -3.6392,
"2010": -2.8467,
"2011": -1.6659,
"2012": 1.9278,
"2013":  1.734,
"2014": 3.6656 
} 
],
"Brooklyn": [
 {
 "2001":      0,
"2002":      0,
"2003":      0,
"2004":      0,
"2005":      0,
"2006":      0,
"2007":      0,
"2008":      0,
"2009":      0,
"2010":      0,
"2011":      0,
"2012":      0,
"2013": -0.79962,
"2014": 2.3208 
} 
],
"Charlotte": [
 {
 "2001": -0.80363,
"2002": -1.1024,
"2003":      0,
"2004":      0,
"2005": 5.2074,
"2006": 3.3744,
"2007": 4.5994,
"2008": 4.2057,
"2009": 2.3281,
"2010": 2.0405,
"2011": 5.6656,
"2012": 8.6695,
"2013": 7.7141,
"2014": 1.2708 
} 
],
"Chicago": [
 {
 "2001": 5.1184,
"2002":  7.309,
"2003":  7.125,
"2004": 4.1579,
"2005": 2.1447,
"2006": 1.2575,
"2007": 0.92943,
"2008": 3.5304,
"2009": 2.7245,
"2010": 1.7393,
"2011": -1.6469,
"2012": -3.8356,
"2013": -0.57726,
"2014": 0.40573 
} 
],
"Cleveland": [
 {
 "2001": 5.1218,
"2002": 3.7176,
"2003": 8.7706,
"2004": 2.6206,
"2005": 2.9277,
"2006": 2.0754,
"2007": 0.62729,
"2008": 3.0153,
"2009": -2.0802,
"2010": -1.8672,
"2011": 7.6703,
"2012": 3.9193,
"2013": 2.6787,
"2014": 4.6009 
} 
],
"Dallas": [
 {
 "2001": -0.96342,
"2002": -0.43145,
"2003": -2.3781,
"2004": 0.97617,
"2005": -2.6002,
"2006": -0.81791,
"2007": -1.8508,
"2008": -1.0976,
"2009": 0.74321,
"2010": -1.8841,
"2011": -0.21705,
"2012": 0.27101,
"2013": 0.84017,
"2014": -0.96034 
} 
],
"Denver": [
 {
 "2001": 4.6501,
"2002": 7.9833,
"2003": 7.7985,
"2004": 1.0656,
"2005": 2.2923,
"2006": 1.5442,
"2007": 0.6751,
"2008": 2.4401,
"2009": -0.61724,
"2010": 0.94018,
"2011": 0.41541,
"2012": 0.055755,
"2013": 0.64875,
"2014": 4.3716 
} 
],
"Detroit": [
 {
 "2001": 2.2207,
"2002": 0.034431,
"2003":  1.504,
"2004": -1.7244,
"2005": -0.34613,
"2006": -2.0436,
"2007": -0.88841,
"2008": -1.4379,
"2009": 0.8795,
"2010": 3.8166,
"2011": 2.7556,
"2012": 5.0938,
"2013": 4.2638,
"2014": 3.6538 
} 
],
"Golden State": [
 {
 "2001": 7.7329,
"2002": 4.9375,
"2003": 2.8427,
"2004": 2.7589,
"2005": 1.6683,
"2006": 3.4501,
"2007": 3.5304,
"2008": 0.22123,
"2009": 4.3605,
"2010": 4.0907,
"2011": 4.9861,
"2012": 2.1427,
"2013": 1.6024,
"2014": -1.1038 
} 
],
"Houston": [
 {
 "2001": 0.27244,
"2002": 5.7121,
"2003": 2.3964,
"2004": -0.14414,
"2005": -1.2788,
"2006": 2.1922,
"2007": -3.2054,
"2008": -0.18894,
"2009": -0.49031,
"2010": 1.8833,
"2011": -0.21407,
"2012": 0.89903,
"2013": 1.1221,
"2014": -0.60092 
} 
],
"Indiana": [
 {
 "2001": 1.9551,
"2002":   0.74,
"2003": 0.86701,
"2004": -1.3056,
"2005": 1.1809,
"2006": 0.89723,
"2007": 5.4868,
"2008": 2.4728,
"2009":  1.047,
"2010": 5.3446,
"2011": 3.1075,
"2012": 1.5399,
"2013": -0.14244,
"2014":  1.886 
} 
],
"LA Clippers": [
 {
 "2001": 3.2924,
"2002": 2.5615,
"2003": 3.3959,
"2004":  4.412,
"2005": 2.4466,
"2006":  1.444,
"2007": 1.9369,
"2008": 5.5519,
"2009": 6.7604,
"2010": 4.4864,
"2011": 2.2195,
"2012": 0.91574,
"2013": -0.74677,
"2014": -0.64967 
} 
],
"LA Lakers": [
 {
 "2001": -1.0706,
"2002": -1.7049,
"2003":  1.478,
"2004": 1.6784,
"2005": 4.9835,
"2006": 0.28632,
"2007": 0.88292,
"2008": -4.1599,
"2009": -2.2575,
"2010": -0.25654,
"2011": -2.9646,
"2012": 1.1709,
"2013": 2.3664,
"2014": 5.7972 
} 
],
"Memphis": [
 {
 "2001":      0,
"2002": 5.7798,
"2003": 5.2841,
"2004": 1.2661,
"2005": 0.11749,
"2006": 0.1011,
"2007": 6.8569,
"2008": 5.3562,
"2009": 4.3633,
"2010": 0.94938,
"2011": 2.0071,
"2012": 1.3106,
"2013": -1.425,
"2014": 0.52185 
} 
],
"Miami": [
 {
 "2001": -0.18544,
"2002": 1.4025,
"2003": 5.4175,
"2004": 2.9211,
"2005": -0.77717,
"2006": -0.41261,
"2007": 2.7298,
"2008": 5.1919,
"2009": 2.2098,
"2010": -0.56307,
"2011": -2.7803,
"2012": -3.6796,
"2013": -2.5614,
"2014": -1.4478 
} 
],
"Milwaukee": [
 {
 "2001": -0.62177,
"2002": 1.9843,
"2003": 2.0637,
"2004": 2.5968,
"2005": 4.6476,
"2006": 1.7998,
"2007": 4.4159,
"2008":  5.958,
"2009": 2.0293,
"2010": 0.68851,
"2011":  2.966,
"2012": -0.33517,
"2013": 1.3007,
"2014": 6.1299 
} 
],
"Minnesota": [
 {
 "2001":  1.968,
"2002": 0.85599,
"2003": 0.5029,
"2004": -0.53322,
"2005": -0.16159,
"2006": 2.6334,
"2007": 2.7855,
"2008": 5.2509,
"2009": 1.8067,
"2010": 8.2905,
"2011":  5.412,
"2012":  2.371,
"2013": 1.3672,
"2014": -1.3866 
} 
],
"New Jersey": [
 {
 "2001": 3.8644,
"2002": 1.0598,
"2003": -0.61862,
"2004": 1.8874,
"2005": 0.59712,
"2006": 0.86847,
"2007":  1.435,
"2008": 4.1596,
"2009": 2.3522,
"2010": 6.0971,
"2011": 4.2847,
"2012": 4.0225,
"2013":      0,
"2014":      0 
} 
],
"New Orleans": [
 {
 "2001":      0,
"2002":      0,
"2003":   2.53,
"2004": 3.2549,
"2005": 5.6315,
"2006":      0,
"2007":      0,
"2008": -1.1045,
"2009": 1.4567,
"2010": 4.4595,
"2011":  1.382,
"2012": 2.5045,
"2013": 3.4606,
"2014": 3.7397 
} 
],
"New Orleans/Oklahoma City": [
 {
 "2001":      0,
"2002":      0,
"2003":      0,
"2004":      0,
"2005":      0,
"2006": 4.0591,
"2007": 2.4734,
"2008":      0,
"2009":      0,
"2010":      0,
"2011":      0,
"2012":      0,
"2013":      0,
"2014":      0 
} 
],
"New York": [
 {
 "2001": 1.5421,
"2002":  2.935,
"2003":  1.763,
"2004": 2.2129,
"2005": 2.8243,
"2006": 5.4158,
"2007": 3.7706,
"2008": 5.7037,
"2009": 2.0548,
"2010": 6.7945,
"2011": 0.50562,
"2012":  1.127,
"2013": 1.2231,
"2014": 0.54186 
} 
],
"Oklahoma City": [
 {
 "2001":      0,
"2002":      0,
"2003":      0,
"2004":      0,
"2005":      0,
"2006":      0,
"2007":      0,
"2008":      0,
"2009": 4.6282,
"2010": -0.52864,
"2011": -0.16533,
"2012": -1.1962,
"2013": -3.5841,
"2014": -1.4407 
} 
],
"Orlando": [
 {
 "2001": 0.60476,
"2002": 0.97051,
"2003": 2.4918,
"2004": 3.6578,
"2005": 4.1386,
"2006": 3.2961,
"2007": 0.89403,
"2008": -1.0618,
"2009": -2.3254,
"2010": -2.0749,
"2011": -1.6698,
"2012": 0.50092,
"2013": 4.4177,
"2014": 6.1686 
} 
],
"Philadelphia": [
 {
 "2001": -2.0366,
"2002": -0.55181,
"2003": 0.52445,
"2004": 2.9874,
"2005":  1.371,
"2006":  1.903,
"2007": 3.7257,
"2008": 2.0866,
"2009": 1.9543,
"2010": 1.5657,
"2011": 0.27196,
"2012": -0.85631,
"2013": 2.8791,
"2014": 6.5527 
} 
],
"Phoenix": [
 {
 "2001": 0.74088,
"2002": 2.5317,
"2003": 2.9331,
"2004": 3.1084,
"2005": -3.691,
"2006": -0.58027,
"2007": -2.0109,
"2008": -1.8561,
"2009": 2.5728,
"2010": -0.48296,
"2011": 2.0104,
"2012": 1.9856,
"2013": 3.9402,
"2014": 0.057488 
} 
],
"Portland": [
 {
 "2001": -0.65139,
"2002":  1.457,
"2003": -0.16541,
"2004": 0.3724,
"2005": 2.9767,
"2006": 6.8469,
"2007": 3.9572,
"2008": 2.7435,
"2009": 1.8416,
"2010": -1.4186,
"2011": 0.42215,
"2012": 5.3523,
"2013": 3.9505,
"2014": -0.19062 
} 
],
"Sacramento": [
 {
 "2001": -0.28032,
"2002": -1.4345,
"2003": -0.67925,
"2004": 0.49045,
"2005": 1.3663,
"2006": 0.41917,
"2007": 2.8956,
"2008": 3.8501,
"2009": 6.1794,
"2010": 4.6618,
"2011": 3.3928,
"2012": 4.5396,
"2013": 5.8496,
"2014": 3.5641 
} 
],
"San Antonio": [
 {
 "2001": -2.3563,
"2002": -2.2836,
"2003": -3.0769,
"2004": -2.2259,
"2005": -1.3794,
"2006": -4.4162,
"2007": -4.8325,
"2008": 0.69645,
"2009": -0.64207,
"2010": -1.4243,
"2011": -0.90809,
"2012": -1.9635,
"2013": -1.3751,
"2014": -3.1837 
} 
],
"Seattle": [
 {
 "2001": 2.1683,
"2002": 0.39036,
"2003": 2.3123,
"2004": 2.0334,
"2005": 0.51815,
"2006": 3.3699,
"2007": 2.7546,
"2008": 5.7564,
"2009":      0,
"2010":      0,
"2011":      0,
"2012":      0,
"2013":      0,
"2014":      0 
} 
],
"Toronto": [
 {
 "2001": -0.16744,
"2002": 2.1296,
"2003": 4.3699,
"2004": 3.5958,
"2005": 3.3948,
"2006": 2.7745,
"2007": 2.0714,
"2008": 0.39461,
"2009": 3.4359,
"2010": 1.6439,
"2011": 5.9239,
"2012": 3.6005,
"2013": 1.8078,
"2014": -0.14248 
} 
],
"Utah": [
 {
 "2001": 0.027564,
"2002": 1.6352,
"2003": 2.0157,
"2004": 3.0913,
"2005": 3.9013,
"2006": 3.3136,
"2007": 0.76312,
"2008": 0.64692,
"2009": 2.8708,
"2010": -0.069633,
"2011":   5.67,
"2012": 1.9202,
"2013": 3.6472,
"2014": 6.8565 
} 
],
"Vancouver": [
 {
 "2001": 5.7328,
"2002":      0,
"2003":      0,
"2004":      0,
"2005":      0,
"2006":      0,
"2007":      0,
"2008":      0,
"2009":      0,
"2010":      0,
"2011":      0,
"2012":      0,
"2013":      0,
"2014":      0 
} 
],
"Washington": [
 {
 "2001": 5.7526,
"2002": 3.4106,
"2003":   2.71,
"2004": 4.9286,
"2005": 3.0206,
"2006": 0.78274,
"2007": 1.4652,
"2008": 2.1606,
"2009": 5.6599,
"2010": 3.2857,
"2011": 6.3825,
"2012": 4.9671,
"2013": 5.3379,
"2014": -0.52538 
} 
] 
};


var dataset = [];
for (var key in data) {
  if (data.hasOwnProperty(key)) {
    tmp = {};
    tmp['name'] = key;
    tmp['diff'] = [];
    //tmp['diff_abs'] = [];
    for(var subkey in data[key][0]) {
      tmp['diff'].push([subkey,
                        Math.abs(data[key][0][subkey]).toFixed(1),
                        -data[key][0][subkey].toFixed(1)
                        ]);
      //tmp['diff'].push([subkey, data[key][0][subkey].toFixed(1)]);
    }
    dataset.push(tmp);
  }
}

function truncate(str, maxLength, suffix) {
  if(str.length > maxLength) {
    str = str.substring(0, maxLength + 1); 
    str = str.substring(0, Math.min(str.length, str.lastIndexOf(" ")));
    str = str + suffix;
  }
  return str;
}

var margin = {top: 20, right: 200, bottom: 0, left: 20},
  width = 650,
  height = 650;

var start_year = 2001,
  end_year = 2014;

var c = d3.scale.category20c();

var x = d3.scale.linear()
  .range([0, width]);

var xAxis = d3.svg.axis()
  .scale(x)
  .orient("top");

var formatYears = d3.format("0000");
xAxis.tickFormat(formatYears);

var svg = d3.select("#away_performance_diff").append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .style("margin-left", margin.left + "px")
  .append("g")
  .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

x.domain([start_year, end_year]);
var xScale = d3.scale.linear()
  .domain([start_year, end_year])
  .range([0, width]);

  svg.append("g")
    .attr("class", "x axis")
    .attr("transform", "translate(0," + 0 + ")")
    .call(xAxis);

  for (var j = 0; j < dataset.length; j++) {
    var g = svg.append("g").attr("class","journal");

    var circles = g.selectAll("circle")
      .data(dataset[j]['diff'])
      .enter()
      .append("circle");

    var text = g.selectAll("text")
      .data(dataset[j]['diff'])
      .enter()
      .append("text");

    var rScale = d3.scale.linear()
      .domain([0, d3.max(dataset[j]['diff'], function(d) { return d[1]; })])
      .range([2, 9]);

    circles
      .attr("cx", function(d, i) { return xScale(d[0]); })
      .attr("cy", j*20+20)
      .attr("r", function(d) { return rScale(d[1]); })
      // .style("stroke", function(d) { 
      //       if(d[2] < 0){return "red"}
      //       else {return "blue"};
      // })
      // .style("stroke-width", function(d) { return 1; })
      // .style("opacity", .5)
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      });
      //.style("fill", function(d) { return c(j); });

    text
      .attr("y", j*20+25)
      .attr("x",function(d, i) { return xScale(d[0])-5; })
      .attr("class","value")
      .text(function(d){ return d[2]; })
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      })
      //.style("fill", function(d) { return c(j); })
      .style("display","none");

    g.append("text")
      .attr("y", j*20+25)
      .attr("x",width+20)
      .attr("class","label")
      .text(truncate(dataset[j]['name'],30,"..."))
      .style("fill", function(d) { return c(j); })
      .on("mouseover", mouseover)
      .on("mouseout", mouseout);
  };

  function mouseover(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","none");
    d3.select(g).selectAll("text.value").style("display","block");
  }

  function mouseout(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","block");
    d3.select(g).selectAll("text.value").style("display","none");
  }

</script>





<script>

var data = 
{
"Golden State": [
 {
 "2001": 7.7329,
"2002": 4.9375,
"2003": 2.8427,
"2004": 2.7589,
"2005": 1.6683,
"2006": 3.4501,
"2007": 3.5304,
"2008": 0.22123,
"2009": 4.3605,
"2010": 4.0907,
"2011": 4.9861,
"2012": 2.1427,
"2013": 1.6024,
"2014": -1.1038 
} 
],
"New York": [
 {
 "2001": 1.5421,
"2002":  2.935,
"2003":  1.763,
"2004": 2.2129,
"2005": 2.8243,
"2006": 5.4158,
"2007": 3.7706,
"2008": 5.7037,
"2009": 2.0548,
"2010": 6.7945,
"2011": 0.50562,
"2012":  1.127,
"2013": 1.2231,
"2014": 0.54186 
} 
],
"Sacramento": [
 {
 "2001": -0.28032,
"2002": -1.4345,
"2003": -0.67925,
"2004": 0.49045,
"2005": 1.3663,
"2006": 0.41917,
"2007": 2.8956,
"2008": 3.8501,
"2009": 6.1794,
"2010": 4.6618,
"2011": 3.3928,
"2012": 4.5396,
"2013": 5.8496,
"2014": 3.5641 
} 
],
"Toronto": [
 {
 "2001": -0.16744,
"2002": 2.1296,
"2003": 4.3699,
"2004": 3.5958,
"2005": 3.3948,
"2006": 2.7745,
"2007": 2.0714,
"2008": 0.39461,
"2009": 3.4359,
"2010": 1.6439,
"2011": 5.9239,
"2012": 3.6005,
"2013": 1.8078,
"2014": -0.14248 
} 
],
"Utah": [
 {
 "2001": 0.027564,
"2002": 1.6352,
"2003": 2.0157,
"2004": 3.0913,
"2005": 3.9013,
"2006": 3.3136,
"2007": 0.76312,
"2008": 0.64692,
"2009": 2.8708,
"2010": -0.069633,
"2011":   5.67,
"2012": 1.9202,
"2013": 3.6472,
"2014": 6.8565 
} 
]
};


var dataset = [];
for (var key in data) {
  if (data.hasOwnProperty(key)) {
    tmp = {};
    tmp['name'] = key;
    tmp['diff'] = [];
    //tmp['diff_abs'] = [];
    for(var subkey in data[key][0]) {
      tmp['diff'].push([subkey,
                        Math.abs(data[key][0][subkey]).toFixed(1),
                        -data[key][0][subkey].toFixed(1)
                        ]);
      //tmp['diff'].push([subkey, data[key][0][subkey].toFixed(1)]);
    }
    dataset.push(tmp);
  }
}

function truncate(str, maxLength, suffix) {
  if(str.length > maxLength) {
    str = str.substring(0, maxLength + 1); 
    str = str.substring(0, Math.min(str.length, str.lastIndexOf(" ")));
    str = str + suffix;
  }
  return str;
}

var margin = {top: 20, right: 200, bottom: 0, left: 20},
  width = 650,
  height = 140;

var start_year = 2001,
  end_year = 2014;

var c = d3.scale.category20c();

var x = d3.scale.linear()
  .range([0, width]);

var xAxis = d3.svg.axis()
  .scale(x)
  .orient("top");

var formatYears = d3.format("0000");
xAxis.tickFormat(formatYears);

var svg = d3.select("#away_performance_diff_worst").append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .style("margin-left", margin.left + "px")
  .append("g")
  .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

x.domain([start_year, end_year]);
var xScale = d3.scale.linear()
  .domain([start_year, end_year])
  .range([0, width]);

  svg.append("g")
    .attr("class", "x axis")
    .attr("transform", "translate(0," + 0 + ")")
    .call(xAxis);

  for (var j = 0; j < dataset.length; j++) {
    var g = svg.append("g").attr("class","journal");

    var circles = g.selectAll("circle")
      .data(dataset[j]['diff'])
      .enter()
      .append("circle");

    var text = g.selectAll("text")
      .data(dataset[j]['diff'])
      .enter()
      .append("text");

    var rScale = d3.scale.linear()
      .domain([0, d3.max(dataset[j]['diff'], function(d) { return d[1]; })])
      .range([2, 9]);

    circles
      .attr("cx", function(d, i) { return xScale(d[0]); })
      .attr("cy", j*20+20)
      .attr("r", function(d) { return rScale(d[1]); })
      // .style("stroke", function(d) { 
      //       if(d[2] < 0){return "red"}
      //       else {return "blue"};
      // })
      // .style("stroke-width", function(d) { return 1; })
      // .style("opacity", .5)
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      });
      //.style("fill", function(d) { return c(j); });

    text
      .attr("y", j*20+25)
      .attr("x",function(d, i) { return xScale(d[0])-5; })
      .attr("class","value")
      .text(function(d){ return d[2]; })
      .style("fill", function(d) { 
            if(d[2] < 0){return "#b24747"}
            else {return "#000099"};
      })
      //.style("fill", function(d) { return c(j); })
      .style("display","none");

    g.append("text")
      .attr("y", j*20+25)
      .attr("x",width+20)
      .attr("class","label")
      .text(truncate(dataset[j]['name'],30,"..."))
      .style("fill", function(d) { return c(j); })
      .on("mouseover", mouseover)
      .on("mouseout", mouseout);
  };

  function mouseover(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","none");
    d3.select(g).selectAll("text.value").style("display","block");
  }

  function mouseout(p) {
    var g = d3.select(this).node().parentNode;
    d3.select(g).selectAll("circle").style("display","block");
    d3.select(g).selectAll("text.value").style("display","none");
  }

</script>
