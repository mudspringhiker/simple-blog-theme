---
layout: post
title: Pair Programming at a PyLadies MeetUp
tagline: Creating a Python class
tags: ["Python", "classes", "dictionaries", "MeetUp", "PyLadies"]
---

Yesterday was the third time I attended a [PyLadies' MeetUp](https://www.meetup.com/PyLadies-ATX/), but it was the first time for me to go to their Python study group. It was a good time to practice what I've learned from months of self-training. It was the first time I spoke about code to an audience, although it was a small group. Since it was pair programming, I was able to practice how I would explain and learn how others would approach a Python problem.

The Python problem was to write a program that creates a grocery list that has unique items in it. The program must have a function that can add an item to the list and a function to get the list. The added challenge was to have the program able to specify the amount of an item in the list.

At first, my partner and I thought about using a list as a data type for the grocery list object we want to create, which inevitably means we need to use classes by the way. And then to address the challenge of adding the amount of the item, we thought about a list of tuples which consists of a string and an integer data type like so:

```
grocery_list = [("item1", 1), ("item2", 1), ....]
```

However, we eventually came across the problem of not being able to change the quantity in case the item is already in the list and therefore, we just want to increment the amount of the item. This was due to the immutability of the tuple.

So we then resorted to using a dictionary as data type for the grocery list object. Everything went smoother from there. Then, the PyLadies host, Rachel, pointed that using the `defaultdict` class from the Python module `collections`, the code for the grocery list class can even get shorter. 

Here is the [Gist](https://gist.github.com/mudspringhiker/4e24bc8f4398dfe6535ec2e6baa485d5) of our final code. It also contains the original code if not using the `defaultdict` class (this was commented out below). The main function involves using the class we created and testing out the functions under that class. 

<script src="https://gist.github.com/mudspringhiker/4e24bc8f4398dfe6535ec2e6baa485d5.js"></script>

