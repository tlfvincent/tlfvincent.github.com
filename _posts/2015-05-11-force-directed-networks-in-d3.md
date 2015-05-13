---
layout: post
title: "Force directed networks in D3"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Recently, I have been involved in a number of projects that have required the use for neat interactive vizualization of networks. After strying out a few options, D3 quickly came out as the most logical and appealling choice. To paraphrase D3's home website *D3.js is a JavaScript library for manipulating documents based on data. D3 helps you bring data to life using HTML, SVG, and CSS*. While D3 can be a tad wordy, and may have a realtively steep learning curve at the start, it becomes a dream to work with once you familiarize with it. If you run into a hurdle (and we always do), D3 has a vibrant community of users that have generously shared their knowledge (especially on StackOverflow). I feel like I have abused this generosity, so I figured I would try and share some snippets of the knowledge that I have accumulated during my short time with D3. For most of this post, I will be working with the standard miserables dataset.

### A basic D3 force-directed network
For the sake of clarity, I wil omit the usual HTML wrappers, and get straight to the meaty D3 bit! First and foremost, we need to make sure we include the d3.js library:

{% highlight html %}
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
{% endhighlight %}

We can also add some CSS styling to the nodes and links that will be displayed in our network
{% highlight css %}
<style>
.node {
  stroke: #fff;
  stroke-width: 1.5px;
}

.link {
  stroke: #999;
  stroke-opacity: .6;
}
</style>
{% endhighlight %}

Finally, we can add the code chunk below to display the most basic D3 force directed network:
{% highlight javascript %}
<script>
var width = 500,
    height = 500;

var color = d3.scale.category20();

var force = d3.layout.force()
    .charge(-120)
    .linkDistance(30)
    .size([width, height]);


d3.json("miserables.json", function(error, graph) {

  var svg = d3.select("body").append("svg")
    .attr("width", width)
    .attr("height", height);

  force
      .nodes(graph.nodes)
      .links(graph.links)
      .start();

  var link = svg.selectAll(".link")
      .data(graph.links)
    .enter().append("line")
      .attr("class", "link")
      .style("stroke-width", function(d) { return Math.sqrt(d.value); });

  var node = svg.selectAll(".node")
      .data(graph.nodes)
    .enter().append("circle")
      .attr("class", "node")
      .attr("r", 5)
      .style("fill", function(d) { return color(d.group); })
      .call(force.drag);

  node.append("title")
      .text(function(d) { return d.name; });

  force.on("tick", function() {
    link.attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

    node.attr("cx", function(d) { return d.x; })
        .attr("cy", function(d) { return d.y; });
  });
});
</script>
{% endhighlight %}





<style>
.node {
  stroke: #fff;
  stroke-width: 1.5px;
}

.link {
  stroke: #999;
  stroke-opacity: .6;
}

.node text {
  font: 9px helvetica;
}

d3-tip {
    line-height: 1;
    color: black;
}
</style>
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>

<script>
var width = 500,
    height = 500;

var color = d3.scale.category20();

var basic_force = d3.layout.force()
    .charge(-120)
    .linkDistance(30)
    .size([width, height]);


d3.json("/miserables.json", function(error, graph) {

  var basic_svg = d3.select("div#basic_network").append("svg")
    .attr("width", width)
    .attr("height", height);

  basic_force
      .nodes(graph.nodes)
      .links(graph.links)
      .start();

  var basic_link = basic_svg.selectAll(".link")
      .data(graph.links)
    .enter().append("line")
      .attr("class", "link")
      .style("stroke-width", function(d) { return Math.sqrt(d.value); });

  var basic_node = basic_svg.selectAll(".node")
      .data(graph.nodes)
      .enter().append("circle")
      .attr("class", "node")
      .attr("r", 5)
      .style("fill", function(d) { return color(d.group); })
      .call(basic_force.drag);

  basic_force.on("tick", function() {
    basic_link.attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

    basic_node.attr("cx", function(d) { return d.x; })
        .attr("cy", function(d) { return d.y; });
  });
});
</script>

<div id="basic_network"></div>


### Adding text labels to nodes
Usually, it is desirable to display attributes on the nodes, so people often like to add text information. This can be achieved by adding a few changes to the code shown above. Below is the new code chunk that needs to be modified:

{% highlight javascript %} 
  var node = svg.selectAll(".node")
      .data(graph.nodes)
      .enter().append("g")
      .attr("class", "node")
      .call(force.drag);

    node.append("circle")
        .attr("r", 5)
        .style("fill", function (d) {
        return color(d.group);
    })

    node.append("text")
          .attr("dx", 10)
          .attr("dy", ".35em")
          .text(function(d) { return d.name });
          .style("stroke", "gray");

  force.on("tick", function() {
    link.attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

    d3.selectAll("circle")
      .attr("cx", function (d) {return d.x;})
      .attr("cy", function (d) {return d.y;});

    d3.selectAll("text")
      .attr("x", function (d) {return d.x;})
      .attr("y", function (d) {return d.y;});
  });
{% endhighlight %}

We can also add some styling to the displayed text by adding the following CSS:

{% highlight css %}
.node text {
  font: 9px helvetica;
}
{% endhighlight %}


<script>
var width = 500,
    height = 500;

var color = d3.scale.category20();

var labelled_force = d3.layout.force()
    .charge(-120)
    .linkDistance(30)
    .size([width, height]);


d3.json("/miserables.json", function(error, graph) {

	var labelled_svg = d3.select("div#labelled_network").append("svg")
    .attr("width", width)
    .attr("height", height);

  labelled_force
      .nodes(graph.nodes)
      .links(graph.links)
      .start();

  var labelled_link = labelled_svg.selectAll(".link")
      .data(graph.links)
    .enter().append("line")
      .attr("class", "link")
      .style("stroke-width", function(d) { return Math.sqrt(d.value); });

  var labelled_node = labelled_svg.selectAll(".node")
      .data(graph.nodes)
      .enter().append("g")
      .attr("class", "node")
      .call(labelled_force.drag);

    labelled_node.append("circle")
        .attr("r", 5)
        .style("fill", function (d) {
        return color(d.group);
    })

    labelled_node.append("text")
          .attr("dx", 10)
          .attr("dy", ".35em")
          .text(function(d) { return d.name })
          .style("stroke", "gray");

  labelled_force.on("tick", function() {
    labelled_link.attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

    d3.selectAll("circle")
      .attr("cx", function (d) {return d.x;})
      .attr("cy", function (d) {return d.y;});

    d3.selectAll("text")
      .attr("x", function (d) {return d.x;})
      .attr("y", function (d) {return d.y;});
  });
});
</script>

<div id="labelled_network"></div>

Obviously, a network with too many nodes becomes quickly unreadable once we add labels, so in this case it is preferable to resort to D3's tooltip functionnality, which allows us to display the desired properties whenever a user hovers over any given node.

### Adding hover text over nodes

{% highlight javascript %}
<script type='text/javascript' src="http://labratrevenge.com/d3-tip/javascripts/d3.tip.v0.6.3.js"> </script>
{% endhighlight %}


{% highlight css %}
d3-tip {
    line-height: 1;
    color: black;
}
{% endhighlight %}

<script type='text/javascript' src="http://labratrevenge.com/d3-tip/javascripts/d3.tip.v0.6.3.js"> </script>
<script>
var width = 500,
    height = 500;

var color = d3.scale.category20();

var hover_force = d3.layout.force()
    .charge(-120)
    .linkDistance(30)
    .size([width, height]);


d3.json("/miserables.json", function(error, graph) {

  var hover_svg = d3.select("div#hover_network").append("svg")
    .attr("width", width)
    .attr("height", height);

  //Set up tooltip
var tip = d3.tip()
    .attr('class', 'd3-tip')
    .offset([-10, 0])
    .html(function (d) {
    return  d.name + "";
})
hover_svg.call(tip);

  hover_force
      .nodes(graph.nodes)
      .links(graph.links)
      .start();

  var hover_link = hover_svg.selectAll(".link")
      .data(graph.links)
    .enter().append("line")
      .attr("class", "link")
      .style("stroke-width", function(d) { return Math.sqrt(d.value); });

  var hover_node = hover_svg.selectAll(".node")
      .data(graph.nodes)
      .enter().append("g")
      .attr("class", "node")
      .call(labelled_force.drag);

    hover_node.append("circle")
        .attr("r", 5)
        .style("fill", function (d) {
        return color(d.group);
    })
    .on('mouseover', tip.show) //Added
    .on('mouseout', tip.hide); //Added 

    // hover_node.append("text")
    //       .attr("dx", 10)
    //       .attr("dy", ".35em")
    //       .text(function(d) { return d.name })
    //       .style("stroke", "gray");

  hover_force.on("tick", function() {
    hover_link.attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

    d3.selectAll("circle")
      .attr("cx", function (d) {return d.x;})
      .attr("cy", function (d) {return d.y;});

    // d3.selectAll("text")
    //   .attr("x", function (d) {return d.x;})
    //   .attr("y", function (d) {return d.y;});
  });
});
</script>

<div id="hover_network"></div>




### Adding FontAwesome icons instead of nodes

FontAwesome icons and associated CSS codes can be found [here](http://fortawesome.github.io/Font-Awesome/icons/)

<script>
var width = 500,
    height = 500;

//var color = d3.scale.category20();

var fontawesome_force = d3.layout.force()
    .charge(-120)
    .linkDistance(30)
    .size([width, height]);


d3.json("/miserables.json", function(error, graph) {

  var fontawesome_svg = d3.select("div#fontawesome_network").append("svg")
      .attr("width", width)
      .attr("height", height);

  fontawesome_force
      .nodes(graph.nodes)
      .links(graph.links)
      .start();

  var fontawesome_link = fontawesome_svg.selectAll(".link")
      .data(graph.links)
      .enter().append("line")
      .attr("class", "link")
      .style("stroke-width", function(d) { return Math.sqrt(d.value); });

  var fontawesome_node = fontawesome_svg.selectAll(".node")
      .data(graph.nodes)
      .enter().append("g")
      .attr("class", "node")
      .call(fontawesome_force.drag);

    fontawesome_node.append("circle");
    //     .attr("r", 5)
    //     .style("fill", function (d) {
    //     return color(d.group);
    // });

    fontawesome_node.append("text")
      .attr('text-anchor', 'middle')
      .attr('dominant-baseline', 'central')
      .attr('font-family', 'FontAwesome')
      .attr('size', '100px')
      .text(function(d) { return '\uf007' });



  fontawesome_force.on("tick", function() {
    fontawesome_link.attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

    // fontawesome_node.attr("cx", function(d) { return d.x; })
    //     .attr("cy", function(d) { return d.y; });
    d3.selectAll("circle")
      .attr("cx", function (d) {return d.x;})
      .attr("cy", function (d) {return d.y;});

    d3.selectAll("text")
      .attr("x", function (d) {return d.x;})
      .attr("y", function (d) {return d.y;}); 
  });
});
</script>

<div id="fontawesome_network"></div>


#### Other helpful pages

- This page gives [a number of additional features](http://www.coppelia.io/2014/07/an-a-to-z-of-extra-features-for-the-d3-force-layout/) that range in increasing degree in complexity.
- This page shows a [really neat example](http://bl.ocks.org/GerHobbelt/3071239) of convex hulls applied to clusters of nodes in a D3 network.


