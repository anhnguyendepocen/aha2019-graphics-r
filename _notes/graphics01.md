---
title: "Graphics in R I"
author: "Taylor Arnold"
output: html_notebook
---


## A Brief History of Statistical Graphics

### Early History

When most people think about statistical graphics, they
assume I am either talking about the purely aesthetic
properties of creating visual representations of data or
a list of specific types of plots (i.e., histograms,
box plots, bar plots). The history of the latter case,
essentially akin to graphic design in general, dates
can be traced back to as early as the 6th century B.C.E.
with the earliest known maps:

![](../assets/img/early-map.jpg)

The golden age of beautiful, hand-drawn graphics more
closely resembling those of today was centred around
19th Century France. Charles Minard's (1781-1870)
visualization of Napoleon's defeat in Russia is perhaps
the most well-known example of work from this period:

![](../assets/img/minard_lg.gif)

Personally, I am partial to the work of André-Michel Guerry
(1802 - 1866), who made amazingly modern visual arguments
from French census data.

![](../assets/img/guerry.jpg)

In the 20th Century, this tradition has been carried on
with a plethora of hand designed infographics scattered
across the web and in print media such as this example
on Randall Munroe's *xkcd* web-comic:

![](../assets/img/xkcd-681-gravity-wells_large.png)

Edward Tufte is likely the most well-known modern figure
in data-based graphical design. He has written a number
of influential books such as *The Visual Display of
Quantitative Information* (1983), *Envisioning Information*
(1990) and *Visual Explanations: Images and Quantities,
Evidence and Narrative* (1997).

![](../assets/img/tufte.jpg)

I have all of his (stunningly beautiful) books in my office
if you would ever like to thumb through them.

### Theorizing Graphics

Making visually pleasing graphics is important. Our focus
today, however, is on understanding and theorizing exactly
how graphics encode knowledge about data. The history of
this begins, at least to us, with the work of Jacques Bertin
(1918-2010), a cartographer and philosopher by training.
In his 1967 work *Semiologie Graphique* he argued that:

> Graphic representation constitutes one of the basic sign-systems
> conceived by the human mind for the purposes of storing, understanding,
> and communicating essential information. As a "language" for the eye,
> graphics benefits from the ubiquitous properties of visual perception.
> As a monosemic system, it forms the rational part of the world of images.

To the best of my knowledge, this is the earliest explicit
description of graphics as a form of knowledge and evidence.
Bertin's work was republished in English in 1983 as
*Semiology of graphics: diagrams, networks, maps*:

![](../assets/img/bertin.jpg)

With the translation, Bertin quickly became known by scholars
of statistical graphics. Bill Cleveland, in *Visualizing Data*
(1985), built on Bertin’s work by theorizing a distinction between
graphing, the process of visualizing  raw data, and fitting,
the process of visualizing transformed data and statistical models.

![](../assets/img/cleveland.png)

His parallel text, *The Elements of Graphing* (1993),
describes an actual system to implement many of his ideas.

In *The Grammar of Graphics* (1999), Wilkinson extended
Cleveland’s theory by drawing a distinction between the
mathematical abstraction of a graph and the physical
manifestation of a rendered graphic. He then set out
to describe the fundamental units that comprised a
visualization.

![](../assets/img/gg.jpg)

Wilkinson constructed a formal language for describing
statistical visualizations by separating out the
mathematical specification of a graphics system from
the aesthetic details of its assembly and display.
Wilkinson named each component of the visualization,
moving from the original data to the output, a layer
in his formal Grammar of Graphics system.

Examples of layers include:

- picking the scale of the plot
- choosing the size of points
- computing summary statistics

Wilkinson’s formal language explicates what assumptions
are being made, where these assumptions are being
made, and how the original data has been modified to
create the output.

Finally, Hadley Wickham implemented and described a
graphical system built on the ideas of Wilkinson in
*ggplot2: Elegant Graphics for Data Analysis* (2009).
His object-oriented system, the **ggplot2** R library,
constructs rules for fitting together a small number
of classes and relationships between them to create an
"almost unlimited world of graphical forms". For Wickham's
interpretation, see the (very readable and open access) paper
["The Layered Grammar of Graphics"](http://vita.had.co.nz/papers/layered-grammar.pdf).
It is the usage of the **gplot2** package that we now turn to.

## Hans Rosling

Hans Rosling's 200 Countries, 200 Years, 4 Minutes:

- [https://www.youtube.com/watch?v=jbkSRLYSojo](https://www.youtube.com/watch?v=jbkSRLYSojo)

I have shown this to nearly all of my statistics courses, and while
a bit dated it is still the best representation of what data visualization is
all about.

## What is 'data'?

In this workshop, we will explicitly work with data in a tabular format. It is
important in understanding the grammar of graphics to have some basic
terminology about tabular data.

Tables of data have **observations** stored in rows and
**variables** stored in columns. The individual elements are
called **values**. So, each row represents a
particular object in our dataset and each column represents
some feature of the objects.

![](../assets/img/tidy-1.png)

Let's look at a dataset of Roman Catholic dioceses in the United States:


{% highlight r %}
library(historydata)

catholic_dioceses <- as_data_frame(catholic_dioceses)
catholic_dioceses
{% endhighlight %}



{% highlight text %}
## # A tibble: 425 x 6
##    diocese                    rite    lat  long event  date
##    <chr>                      <chr> <dbl> <dbl> <fct>  <dttm>
##  1 Baltimore, Maryland        Latin  39.3 -76.6 erect… 1789-04-06 00:00:00
##  2 New Orleans, Louisiana     Latin  30.0 -90.1 erect… 1793-04-25 00:00:00
##  3 Boston, Massachusetts      Latin  42.4 -71.1 erect… 1808-04-08 00:00:00
##  4 Louisville, Kentucky       Latin  38.3 -85.8 erect… 1808-04-08 00:00:00
##  5 New York, New York         Latin  40.7 -74.0 erect… 1808-04-08 00:00:00
##  6 Philadelphia, Pennsylvania Latin  40.0 -75.2 erect… 1808-04-08 00:00:00
##  7 Richmond, Virginia         Latin  37.5 -77.4 erect… 1820-06-19 00:00:00
##  8 Charleston, South Carolina Latin  32.8 -79.9 erect… 1820-07-11 00:00:00
##  9 Cincinnati, Ohio           Latin  39.1 -84.5 erect… 1821-06-19 00:00:00
## 10 St. Louis, Missouri        Latin  38.6 -90.2 erect… 1826-07-18 00:00:00
## # ... with 415 more rows
{% endhighlight %}

The observations here are *dioceses* and the variables are: `diocese`,
`rite`, `lat`, `long`, `event`, and  date`. Each variable
measures something about a given observation. What exactly
constitutes a row of the data is called a **unit of analysis**.
Keeping in mind what the unit of analysis is will be very
important as we think about how data is being used.

## Graphics in R: ggplot2

### Example with Hans Roslin's data

To illustrate how to actually use the grammar of graphcs in R points, let's
look at a subset of the data that Hans Roslin used in the video. It contains
just a single year of the data (2007).


{% highlight r %}
gapminder_2007 <- read_csv("https://statsmaths.github.io/stat_data/gapminder_2007.csv")
gapminder_2007
{% endhighlight %}



{% highlight text %}
## # A tibble: 142 x 5
##    country     continent life_exp       pop gdp_per_cap
##    <chr>       <chr>        <dbl>     <int>       <dbl>
##  1 Afghanistan Asia          43.8  31889923        975.
##  2 Albania     Europe        76.4   3600523       5937.
##  3 Algeria     Africa        72.3  33333216       6223.
##  4 Angola      Africa        42.7  12420476       4797.
##  5 Argentina   Americas      75.3  40301927      12779.
##  6 Australia   Oceania       81.2  20434176      34435.
##  7 Austria     Europe        79.8   8199783      36126.
##  8 Bahrain     Asia          75.6    708573      29796.
##  9 Bangladesh  Asia          64.1 150448339       1391.
## 10 Belgium     Europe        79.4  10392226      33693.
## # ... with 132 more rows
{% endhighlight %}

Here is a plot similar to the one that Roslin used without all of the fancy
colors and moving elements.


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point()
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-4-1.png" title="plot of chunk unnamed-chunk-4" alt="plot of chunk unnamed-chunk-4" width="100%" />

Here there are four specific elements that we had to choose to create the plot:

1. selecting the dataset name to use, `gapminder_2007`
2. selecting the variable `gdp_per_cap` to appear on the x-axis
3. selecting the variable `life_exp` to appear on the y-axis
4. choosing to represent the dataset with points by using the `geom_point` layer.

The specific syntax of how to put these elements together is just something that
you need to learn and memorize. Note that the plus sign goes at the end of the
first line and the second line is indented by two spaces.

## Layers

The beauty of the grammar of graphics is that we can construct many types of
plots by combining together simple layers. There is another geometry called
`geom_line` that draws a line between data observations instead of points
(note: this does not actually make much sense here, but we will try it just
to illustrate the idea):


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_line()
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" width="100%" />

But let's say we want the points **and** the lines, how does that work? Well, we just
add the two layers together:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_line() +
  geom_point()
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-6-1.png" title="plot of chunk unnamed-chunk-6" alt="plot of chunk unnamed-chunk-6" width="100%" />

Or, we could add a "best fit line" through the data using the `geom_smooth`
layer with a particular option:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_smooth(method = "lm") +
  geom_point()
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-7-1.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" width="100%" />

We will cover these types of modeling lines in more detail in the third section of the
course.

## Other geometry types

The `geom_text` is another layer type that puts a label in place of a point. It requires
a new input called the `label` that describes which variable is used for the text. Here we
see it combined with the points layer:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_text(aes(label = country))
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-8-1.png" title="plot of chunk unnamed-chunk-8" alt="plot of chunk unnamed-chunk-8" width="100%" />

Again, the specific syntax is something you just need to look up or memorize.

We can also have the show a single variable in a plot using a specific type
of geometry. To show the distribution of a categorical variable, use
`geom_bar` to make a bar plot:


{% highlight r %}
ggplot(gapminder_2007, aes(continent)) +
  geom_bar()
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-9-1.png" title="plot of chunk unnamed-chunk-9" alt="plot of chunk unnamed-chunk-9" width="100%" />

Or, use `geom_hist` to build a histogram of a continuous variable:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap)) +
  geom_bar()
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-10-1.png" title="plot of chunk unnamed-chunk-10" alt="plot of chunk unnamed-chunk-10" width="100%" />

## Data Aesthetics

We discussed how in the first line the variable `gdp_per_cap` is mapped to the x-axis
and `life_exp` is mapped to the y-axis. One powerful feature of the grammar of graphics
is the ability to map variables into other graphical parameters. These are called
"aesthetics" (that is what the `aes()` function stands for) and we already saw one example
last time with the `geom_text` function.

For example, we can change the color of the points to correspond to a variable in the
dataset like this:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(color = continent))
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-11-1.png" title="plot of chunk unnamed-chunk-11" alt="plot of chunk unnamed-chunk-11" width="100%" />

We can also map a continuous variable to color, though the default scale
is not very nice (more on this in a bit).


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(color = log(pop)))
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-12-1.png" title="plot of chunk unnamed-chunk-12" alt="plot of chunk unnamed-chunk-12" width="100%" />

We could also change the size of the point to match the population. Note that R
writes the population key in scientific notation (2.5e+08 is the same as 2.5 time
10 to the power of eight).


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(size = pop))
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-13-1.png" title="plot of chunk unnamed-chunk-13" alt="plot of chunk unnamed-chunk-13" width="100%" />

Or, finally, we could change both the size and color.


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(size = pop, color = continent))
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-14-1.png" title="plot of chunk unnamed-chunk-14" alt="plot of chunk unnamed-chunk-14" width="100%" />

Notice that R takes care of the specific colors and sizes. All we do is indicate which
variables are mapped to a given value.

I rarely do this in practice, but it is also  possible to map a variable to a shape:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(shape = continent))
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-15-1.png" title="plot of chunk unnamed-chunk-15" alt="plot of chunk unnamed-chunk-15" width="100%" />

## Fixed aesthetics

A very powerful feature of the grammar of graphics is the ability to map a variable to
a visual aesthetic such as the x- and y-axes or the color and shape. In some cases,
though, you may just want to change an aesthetic to a fixed value for all points.
This can be done as well by specifying the aesthetic **outside** of the `aes()`
function. For example, here I'll change all of the points to be blue:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(color = "blue")
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-16-1.png" title="plot of chunk unnamed-chunk-16" alt="plot of chunk unnamed-chunk-16" width="100%" />

R won't give an error if I put the same code inside of the aes function. Watch
this:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(color = "blue"))
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-17-1.png" title="plot of chunk unnamed-chunk-17" alt="plot of chunk unnamed-chunk-17" width="100%" />

**What's happening here?!**

You can mix fixed and variable aesthetics in the same plot. For example, here I
use color to represent the continent but make all the points larger.


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(color = continent), size = 3)
{% endhighlight %}

<img src="../assets/graphics01/unnamed-chunk-18-1.png" title="plot of chunk unnamed-chunk-18" alt="plot of chunk unnamed-chunk-18" width="100%" />

Note that the `aes()` part **must** go first. Just another rule you need to remember.
