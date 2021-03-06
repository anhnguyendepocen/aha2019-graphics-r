---
title: "Graphics in R II"
author: "Taylor Arnold"
output: html_notebook
---

## More Graphics Layers

Last time we introduced a brief history of the theory of graphics
and developed the concepts and syntax behind building graphics in
R with the **ggplot2** package. Today we will continue this by
looking at the remaining layer types within the Grammar of Graphics.


### Labels

We can add labels to the plot by adding new layers to the plot:

- `xlab("text for the x-axis")`
- `ylab("text for the y-axis")`
- `ggtitle("text for the title/top of the plot")`
- `labs(size = "label for the size legend")`

Let's add these to the current plot:


{% highlight r %}
ggplot(data=gapminder_2007, aes(x=gdp_per_cap, y=life_exp)) +
  geom_point(aes(size=pop)) +
  xlab("Gross domestic product per capita (USD)") +
  ylab("Life expectancy at birth (years)") +
  labs(size = "Population") +
  ggtitle("Macroeconomic variables by country (2007)")
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-2-1.png" title="plot of chunk unnamed-chunk-2" alt="plot of chunk unnamed-chunk-2" width="100%" />

Do not feel that you need to add complex labels to plots as we are doing
an exploratory analysis. However, when presenting plots in a report these
should certainly be used to clarify the plot to the audience or readers.

### Themes

Another type of layer with a default value is the theme layer. These describe all of the non-data oriented visual aspects of the plot (such as the background color, the size of the tick marks, and the font of the axis labels). The default is `theme_classic`; I prefer `theme_minimal`:


{% highlight r %}
ggplot(data=gapminder_2007, aes(x=gdp_per_cap, y=life_exp)) +
  geom_point(aes(size=pop)) +
  theme_minimal()
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-3-1.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" width="100%" />

### Scales

Another layer type with a default
value are **scales**, which are associated with every
variable-assigned aesthetic. For example, the
`scale_x_continuous` and `scale_y_continuous` functions
define the range of the two dimensions of the plot.
The defaults (just fitting all of the data) usually work well,
but we can modify them when needed:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point() +
  scale_x_continuous(limits = c(-10000, 50000))
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-4-1.png" title="plot of chunk unnamed-chunk-4" alt="plot of chunk unnamed-chunk-4" width="100%" />

There are also alternative scales for the x and y coordinates,
such as `scale_x_log10` and `scale_x_sqrt`.

The color scales determine the exact colors being used. The
**viridis** package provides an alternative scale for the
colors that I prefer:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(color = continent)) +
  scale_color_viridis(discrete = TRUE)
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" width="100%" />

The viridis package is particularly useful when producing
plots for overhead projectors, plots that may need to printed,
or plots that are color-blind friendly.

### Facets

Facets are a graphics layer that allow us to produce a number
of smaller visualizations based on the unique values of one or
more categorical variables. There are only two such layers:
`facet_wrap` and `facet_grid`. The following examples show
how they work. Facet wrapping takes a single variable:


{% highlight r %}
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point() +
  facet_wrap(~continent)
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-6-1.png" title="plot of chunk unnamed-chunk-6" alt="plot of chunk unnamed-chunk-6" width="100%" />

I suggest taking a look at the help pages to see how the
`scales` option modifies these plots; it determines whether
each plot uses the same range of values or not. Often one
will be a good choice and the other a bad choice, but it
may not be clear which is which!

### Annotations

Eventually, you may want to manually add annotations to a
plot. That is, adding fixed points, text, or lines that are
not directly mapped from data elements. To add lines, there
are several "fake" geometry layers that help us do that:


{% highlight r %}
ggplot() +
  geom_vline(xintercept = 5) +
  geom_hline(yintercept = 30) +
  geom_abline(slope = 10, intercept = 0) +
  scale_x_continuous(limit = c(0, 5)) +
  scale_y_continuous(limit = c(0, 50))
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-7-1.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" width="100%" />

Here I added them to a blank plot but you can add any of these
elements to a plot with other data points.

Text and points can be added by the `annotate` function:


{% highlight r %}
ggplot() +
  annotate("point", x = 0.9, y = 0.2) +
  annotate("text",  x = 0.1, y = 0.5, label = "HI!") +
  scale_x_continuous(limit = c(0, 1)) +
  scale_y_continuous(limit = c(0, 1))
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-8-1.png" title="plot of chunk unnamed-chunk-8" alt="plot of chunk unnamed-chunk-8" width="100%" />

Again, these can be added to a plot with data elements as
well. You can also specify the color, size, and other
relevant aesthetics just as you would would any other
layer.

### Coordinates and Statistics

The two final layers we have not yet seen in **ggplot2** are
coordinate systems and statistics.

The default coordinate layer is `coord_cartesian`; the only
other coordinate system I regularly use is `coord_flip`.
It exchanges the x and y axes. For most geoms this is silly
because we should just manually flip the x and y coordinates
ourselves in the aesthetic mapping. However, boxplots must
always have the categorical variable first and the numeric
variable second. The flipped coordinates allow use to make
horizontal boxplots:


{% highlight r %}
ggplot(gapminder_2007, aes(continent, life_exp)) +
  geom_boxplot() +
  coord_flip()
{% endhighlight %}

<img src="../assets/graphics02/unnamed-chunk-9-1.png" title="plot of chunk unnamed-chunk-9" alt="plot of chunk unnamed-chunk-9" width="100%" />

Summary statistics are used internally by plots such as the
histogram and box plot (notice that their y axis is a variable
not in the original data set). Getting into their usage is a
bit beyond what I want to cover this week so that is all I
will say at the moment. I just did not want to leave of at
least a mention of the last and final layer in **ggplot2**.

### The Grammar of Graphics

We have covered all of the layer types in **ggplot2**. Putting
these building blocks together you can make nearly any
visualization you could imagine from one or more data sources.
For review, these are the specific layers (the first two are
special because they exist *attached* to other layers rather
than as their own separate elements; they can be attached to
the main `ggplot` function, though, and inherited throughout):

- data
- aesthetics
- geometries
- facets
- statistics
- scales
- coordinate systems
- labels
- annotations

We covered only a small set of the geometries available within
**ggplot2**. Feel free to take a look at the additional options
on the cheatsheet and work them into your analyses when you
feel they might help your work.

## Interactivity

### The plotly package

The **plotly** package allows us to turn ordinary **ggplot2**
graphics into interactive ones. Simply load the library, run
any standard ggplot graphic:


{% highlight r %}
library(plotly)
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(color = continent)) +
  scale_color_viridis(discrete = TRUE)
{% endhighlight %}

And then call the `ggplotly` function:


{% highlight r %}
ggplotly()
{% endhighlight %}

If you are in RStudio, the interactive plot should show up in
the lower right hand corner. If calling R elsewhere, it should
open ion a browser window.

### Tooltip

By default, **plotly** includes any variables used for the
plot in the tooltip (the box that comes up when you hover
over a point). Sometimes we might want a variable to show
up in the plot but not actually included in the plot itself.
To do this, we simply trick **ggplot2** by adding an
unknown aesthetic; here I use one called "fake":


{% highlight r %}
library(plotly)
ggplot(gapminder_2007, aes(gdp_per_cap, life_exp)) +
  geom_point(aes(color = continent, fake = country)) +
  scale_color_viridis(discrete = TRUE)

ggplotly()
{% endhighlight %}

The interactive plot should now show the manufacturer on
the plot.

### Thoughts on interactivity

- interactive graphics are a fantastic tool in doing data analysis
- use caution with larger datasets; consider sub-sampling or only
    working with a smaller collection
- when possible, try to replicate the most useful interactive views
    with static graphics for reproducibility and publication purposes

