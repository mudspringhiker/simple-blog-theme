---
layout: post
title:  "d3.js visualization of Nobel Laureates"
tags: ["d3.js", "data visualization"]
---

At last, after tinkering with [d3.js](https://d3js.org/) for the longest time, I was able to create a visualization that involves maps. As I was practicing on the [Kaggle Nobel Laureates dataset](https://www.kaggle.com/nobelfoundation/nobel-laureates), I thought about using the data on the distribution of Nobel laureates in the world. This dataset, however, I found out at the last minute, does not include Literature Nobel laureates. It didn't inlude the country of affiliation for Literature winners. But the point is that I was able to at least modify [Scott Murray](http://alignedleft.com/tutorials/d3)'s d3.js code to create this map.

I should mention that the d3.js version is 3 and not the latest. Also, it can be hard to see the points with the lowest number of Nobel laureates. I probably have to change this.


<iframe src="http://alonavarshal.com/nobel_prize/nobel_map.html" width="820" height="550"></iframe>

Update, March 23:

I edited the radius of the circles on the map using `d3.scale.sqrt()` so the smaller number of nobel laurates won't be too small. The code for this viz can be found on my GitHub [repo](https://github.com/mudspringhiker/nobel_prize/blob/master/nobel_map.html).