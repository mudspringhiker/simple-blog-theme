---
layout: post
title:  "Exploratory Analysis of the Nobel Laureates Dataset"
date:   2017-03-01
tags: ["pandas", "word cloud", "exploratory data analysis"]
---

Despite creating an account eight months ago in Kaggle, I've yet to get in on the fun over there. So I found one dataset that isn't too difficult-looking and explored it using Python. The [Nobel Laureates dataset](https://www.kaggle.com/nobelfoundation/nobel-laureates) was scraped from the Nobel Prize website and includes data from 1901 to present. Initially, I was only interested in the chemistry category, but I went ahead and looked at the whole data. The file available was a csv file so it wasn't very hard loading it using Python using the Jupyter Notebook.

There were 969 entires giving information on the year of award, category, motivation, prize share, full name, birth date, birth city, birth country, organization country, death date and death country, among others. Some questions I had were what were the categories (as I didn't really know what they were), what is the distribution of sex among the laureates, what is the distribution of awards by country and at what is the usual age when laureates get the awards. I obtained the following plots using seaborn and matplotlib.

**There are six categories with the most awards given in medicine.**
![](./assets/img/2017-03-01-nobel-laureates/category.png)

**If you're a man, there's a high chance you are a nobel laureate.**
![](./assets/img/2017-03-01-nobel-laureates/sex.png)

**Most of the Nobel laureates are from the US.**
![](./assets/img/2017-03-01-nobel-laureates/countries.png)

***Update:***
This plot excludes numbers from Literature Nobel laureates since the data does not include their organization countries. By comparison, here is a plot of Nobel laureates by country of birth (top 25 only):

![](./assets./assets/img/2017-03-01-nobel-laureates/birthtop25.png)

**The US dominates in all categories.**
![](./assets/img/2017-03-01-nobel-laureates/catdistcountry.png)

***Update:***
The categories above does not include "Literature", which is weird. After looking at the pandas DataFrame I created, I found that there are missing values under "Organization Country" for "Literature" as category. I have no idea why, but maybe this is because of some political reasons. It is probably better to look at "Birth Country" instead of "Organization Country".

Here is the plot of all countries that bore Nobel laureates in all categories, however.
![](./assets/img/2017-03-01-nobel-laureates/birthbycountryallcat.png)

**Most of the Nobel laureates are aged around 60 years old.**
![](./assets/img/2017-03-01-nobel-laureates/age.png)

*And finally, since I am a chemist, I decided to look at the most frequently used words in the motivations for awarding the Nobel in Chemistry. The wordcloud below shows this.*
![](./assets/img/2017-03-01-nobel-laureates/wordcloudnobelchem.png)

In conclusion, one has a high chance of getting a Nobel Prize if one is a male, works in medicine, around 60 years old, and resides in the United States.

Cheers!
