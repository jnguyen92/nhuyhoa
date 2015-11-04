---
layout: post
title: "Mapping Values"
date: "October 10, 2015"
categories: R data_wrangling
---

* TOC
{:toc}



# ifelse
The function `ifelse()` provides an easy way to do this. The format is `ifelse(CONDITION, TRUE_VALUE, FALSE_VALUE)`.

Here's an example:

{% highlight r %}
# change the vowels to "vowel" and non-vowels to "consonant"
ifelse(letters %in% c("a", "e", "i", "o", "u"), "vowel", "consonant")
{% endhighlight %}



{% highlight text %}
##  [1] "vowel"     "consonant" "consonant" "consonant" "vowel"    
##  [6] "consonant" "consonant" "consonant" "vowel"     "consonant"
## [11] "consonant" "consonant" "consonant" "consonant" "vowel"    
## [16] "consonant" "consonant" "consonant" "consonant" "consonant"
## [21] "vowel"     "consonant" "consonant" "consonant" "consonant"
## [26] "consonant"
{% endhighlight %}



{% highlight r %}
# change the vowels to "vowel" and keep non-vowels as they are
ifelse(letters %in% c("a", "e", "i", "o", "u"), "vowel", letters)
{% endhighlight %}



{% highlight text %}
##  [1] "vowel" "b"     "c"     "d"     "vowel" "f"     "g"     "h"    
##  [9] "vowel" "j"     "k"     "l"     "m"     "n"     "vowel" "p"    
## [17] "q"     "r"     "s"     "t"     "vowel" "v"     "w"     "x"    
## [25] "y"     "z"
{% endhighlight %}

# mapvalues
The function `plyr::mapvalues()` is a more versatile version of `ifelse()`. The format is `mapvalues(VECTOR, FROM_VALUES, TO_VALUES)`, where the $$i^{th}$$ value in FROM_VALUES is changed to the $$i^{th}$$ value in TO_VALUES.

Here's an example:

{% highlight r %}
# start with this vector
subjects
{% endhighlight %}



{% highlight text %}
## [1] "reading"       "writing"       "language arts" "english"      
## [5] "algebra"       "geometry"      "calculus"      "statistics"   
## [9] "science"
{% endhighlight %}



{% highlight r %}
# simplify subjects to reading and math
mapvalues(subjects, 
          c("reading", "writing", "language arts", "english",
            "algebra", "geometry", "calculus", "statistics"),
          c(rep("read", 4), rep("math", 4)))
{% endhighlight %}



{% highlight text %}
## [1] "read"    "read"    "read"    "read"    "math"    "math"   
## [7] "math"    "math"    "science"
{% endhighlight %}

Here the reading-related subjects were simplified to `"read"` and the mathematics-related subjects to were simplified to `"math"`. Notice how values not listed, such as `"science"`, was not affected. 

# setnames
The function `data.table::setnames()` is equivalent to `mapvalues()` but is used solely for renaming data frame columns.

Here's an example:

<div class = "dftab">
<table>
 <thead>
  <tr>
   <th style="text-align:center;"> Sepal.Length </th>
   <th style="text-align:center;"> Sepal.Width </th>
   <th style="text-align:center;"> Petal.Length </th>
   <th style="text-align:center;"> Petal.Width </th>
   <th style="text-align:center;"> Species </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:center;"> 5.1 </td>
   <td style="text-align:center;"> 3.5 </td>
   <td style="text-align:center;"> 1.4 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 4.9 </td>
   <td style="text-align:center;"> 3.0 </td>
   <td style="text-align:center;"> 1.4 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 4.7 </td>
   <td style="text-align:center;"> 3.2 </td>
   <td style="text-align:center;"> 1.3 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 4.6 </td>
   <td style="text-align:center;"> 3.1 </td>
   <td style="text-align:center;"> 1.5 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 5.0 </td>
   <td style="text-align:center;"> 3.6 </td>
   <td style="text-align:center;"> 1.4 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
</tbody>
</table>
</div> <p></p>


{% highlight r %}
# make the column names lower case
setnames(iris, colnames(iris), tolower(colnames(iris)))
{% endhighlight %}

<div class = "dftab">
<table>
 <thead>
  <tr>
   <th style="text-align:center;"> sepal.length </th>
   <th style="text-align:center;"> sepal.width </th>
   <th style="text-align:center;"> petal.length </th>
   <th style="text-align:center;"> petal.width </th>
   <th style="text-align:center;"> species </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:center;"> 5.1 </td>
   <td style="text-align:center;"> 3.5 </td>
   <td style="text-align:center;"> 1.4 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 4.9 </td>
   <td style="text-align:center;"> 3.0 </td>
   <td style="text-align:center;"> 1.4 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 4.7 </td>
   <td style="text-align:center;"> 3.2 </td>
   <td style="text-align:center;"> 1.3 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 4.6 </td>
   <td style="text-align:center;"> 3.1 </td>
   <td style="text-align:center;"> 1.5 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
  <tr>
   <td style="text-align:center;"> 5.0 </td>
   <td style="text-align:center;"> 3.6 </td>
   <td style="text-align:center;"> 1.4 </td>
   <td style="text-align:center;"> 0.2 </td>
   <td style="text-align:center;"> setosa </td>
  </tr>
</tbody>
</table>
</div> <p></p>