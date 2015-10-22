---
layout:     post
title: "Cloning a graph in Python"
date:       2015-09-09
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

If you have ever played around with Algorithms & Data Structures, then you most likely have heard of Leetcode.com, which contains a number of famous (or infamous) of technical questions. One of my favorite in there is the graph clone question, which can be shortly stated as:

```
Clone an undirected graph. Each node in the graph contains a label and a list of its neighbors.
```

Like most algorithms & data structures problems, there are usually many variants to solving this problem, but here I will go through the solution that is most typically reported. To begin, it is generally good form to define a `Node` class:

{% highlight R linenos %} 
class Node:
    '''
    Define a node class for our undirected graph
    '''
    def __init__(self, node_label, node_neighbors=[]):
        self.label = node_label
        self.neighbors = node_neighbors
{% endhighlight %}

You can test the `Node` class in your Python interpreter as below (although this would never be considered a test per-se if we are talking in engineering terms!)

{% highlight R linenos %} 
>>> test_node = Node('test')
>>> print test_node.label
test
>>> print test_node.neighbors
[]
{% endhighlight %}

Finally, we can define the `Solution` class, which will allow us to clone any graph given that we are provided at least one of its node:

{% highlight R linenos %} 
class Solution:
    '''
    Clone undirected graph
    '''
    def cloneGraph(self, node):
        nodeMap = {}
        return self.cloneNode(node, nodeMap)
    
    def cloneNode(self, node, nodeMap):
        if node == None:
            return None
        if node.label in nodeMap:
            return nodeMap[node.label]
        newNode = Node(node.label)
        nodeMap[node.label] = newNode
        for neighbor in node.neighbors:
            newNode.neighbors.append(self.cloneNode(neighbor, nodeMap))
        return newNode
{% endhighlight %}

So what does the code above do? Quite clearly, the `cloneGraph` function in lines 5-7 takes as input a node belonging to the graph we wish to clone, and then initializes a hash map (or dictionnary if we are talking in Python terms). Finally it invokes the `cloneNode` function that is defined in lines 9-18. First, the `cloneNode` function checks for an empty node and if this statement is met returns a `None` value. The next step (lines 12-13) is to check whether the input node is already in `nodeMap`, which would indicate it has already been processed. If this condition is met, then we simply return label of the node (for reasons that will become clear in the next paragraph).


At this point, we get to the meaty part of our solution. Line 14 with the statement `newNode = Node(node.label)` initiates a new node with the same label as the input node and no neighbors (the default for the `Node` class). Next, we add the newly created `newNode` object to the hash map `nodeMap`. Finally, we iterate through all known neighbors of the input node, and recursively append the results of calling `cloneNode` to each of those neighbors. In doing so, nodes already cloned in `nodeMap` will simply be appended to the neighbor list of `newNode` (because of the conditional statement in lines 12-13), while all others will also be recursively added to the `nodeMap` hash map. This will have the direct effect of cloning the entire original graph to the `nodeMap` hashmap! And we are done!
