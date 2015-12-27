---
layout: post
title: "Merges & Joins"
date: "December 18, 2015"
categories: ['data wrangling']
---

* TOC
{:toc}

Often when you have data from multiple sources, you need to combine the data to effectively analyze the data. The most effective way to do this is with joins or merges. 

# Types of Joins

## Inner Join
Keep only rows that match from the two data frames.
![inner join](http://jnguyen92.github.io/nhuyhoa/figure/images/inner_join.png)

## Outer Join
Keep all rows from both data frames.
![outer join](http://jnguyen92.github.io/nhuyhoa/figure/images/outer_join.png)

## Left Join
Keep all rows from x and all those from y that match.
![left join](http://jnguyen92.github.io/nhuyhoa/figure/images/left_join.png)

## Right Join
Keep all rows from y and all those from x that match.
![right join](http://jnguyen92.github.io/nhuyhoa/figure/images/right_join.png)

# Implementation 

## In R
All merges in R can be done with a simple `merge()` function. The arguments for this function include

* `x`, `y`: data frames to be merged
* `by`: the names of columns on which matches are searched
* `all`: option for inner or outer join; set to `FALSE` for inner join and `TRUE` for outer join
* `all.x`: option for left join
* `all.y`: option for right join

To join multiple data frames in R, you can do successive joins. That is, join two and use the result to join with another data frame. To save time, the function `jn.general::mult_merge()` was created to make merging multiple data frames easier

The `dplyr` package has some additional join functions that are useful depending on the situation. Examples of join types include `dplyr::anti_join()` and set operations such as `dplyr::intersect()`, `dplyr::union()`, and `dplyr::setdiff()`.

## In SQL
Joins in SQL follow a simple format:

{% highlight r %}
select tab1.COLNAMES tab2.COLNAMES
from tab1
___ join tab2
on tab1.id = tab2.id and tab1.id2 = tab2.id2;
{% endhighlight %}

To specify the join, simply replace the blank with `inner`, `full outer`, `left`, or `right`. 

To join multiple data frames in SQL, you can also do successive joins. Simply append additional `___ join ... on` statements after the previous join.

SQL also has operations such as `union`, `intersect`, and `except`, which are quite similar to joins