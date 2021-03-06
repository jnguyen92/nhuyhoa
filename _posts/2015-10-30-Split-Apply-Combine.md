---
layout: post
title: "Split Apply Combine"
date: "October 30, 2015"
categories: Software
tags: Wrangling
---

* TOC
{:toc}



Obtaining summary statistics for a given group can be done using the general process:

1. Split: split by the variable
2. Apply: apply the function to each split
3. Combine: combine the results back into a single data structure

# In R

The package `dplyr` is a great option for this in R.


{% highlight r %}
data %>%
  group_by(col1, col2) %>%
  apply_f(newcol = f(x))
{% endhighlight %}


**Splitting**

The `dplyr::group_by()` function can be used to group by any column in the data frame. 

* `tidyr::nest()` can be used to bin split groups into a column list

**Applying**

* `dplyr::slice()` obtains records by row index, useful with functions like `which.max()` or `which.min()`
* `dplyr::summarise()` summarises a vector into a single row 
* `dplyr::mutate()` applies a function on a vector and appends results to data frame
* `dplyr::do()` or `purrrlyr::by_slice()` applies a function on a dataframe and returns a list of objects that are appended to the data frame

There are a few extensions on the `dplyr::summarise()` and `dplyr::mutate()` functions. 

* to apply a function to all variables with `summarise_all()` and `mutate_all()` 
* to apply a function to select variables with `summarise_at()` and `mutate_at()`
* to apply a function to variables that satisfy certain conditions functions `summarise_if()` and `mutate_if()`

* to apply multiple functions, wrap functions in `funs()`
* for functions with additional arguments, do `funs(my_func(., addnl_args))`

**Combining**

By default, `dplyr` will combine the separate groups back together. 

* `tidyr::unnest()` can be used to expand upon a dataframe that has a column consisting of dataframe objects

# In SAS

Split-apply-combine in SAS is implemented using `proc sql` statements. These statements would group and apply the same way that it is done in SQL. 

Alternatively, this can be done in SAS with the `proc` statements. There is `by` or `class` clause that allows users to specify what they would like the data to be split by.

Here are a few examples.


{% highlight r %}
proc sort; by GROUP.VAR;

proc rank ASCENDING/DESCENDING;
by GROUP.VAR;
var ORDER.VAR;
ranks NEW.RANK.COL;
run;
{% endhighlight %}

Proc means has a number of different options. See SAS documentation for more information.

{% highlight r %}
proc means OPTIONS;
class GROUP.VAR;
var MEANS.VAR;
output out = OUTNAME OPTIONS = VARNAME;
run;
{% endhighlight %}


{% highlight r %}
proc standard OPTIONS;
by GROUP.VAR;
var VARNAMES;
run;
{% endhighlight %}

To combine the grouped summaries back into the main data, run a merge/join.

# In SQL

SQL allows split apply combine using the following phrases

* `group by`
* `having`

Aggregate functions tend to be used along with the `group by` command. These functions include

* `count( * )`
* `count( distinct A )`
* `sum( distinct A )`
* `avg( distinct A )`
* `max( A )`
* `min( A )`
* `first( A )`
* `last( A )`

Below is an example of this type of SQL statement.
{% highlight sql %}
SELECT COUNT(DISTINCT COL1) as C
from TAB1
group by COLG
having COL1 > 5
order by C
;
{% endhighlight %}

To combine the grouped summaries back into the main data, run a merge/join.
