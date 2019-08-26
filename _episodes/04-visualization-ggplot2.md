---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 04-visualization-ggplot2.md in _episodes_rmd/
source: Rmd
title: Data visualization with ggplot2
author: Data Carpentry contributors
minutes: 60
---



------------

> ### Learning Objectives
>
> * Produce scatter plots, boxplots, and time series plots using ggplot.
> * Set universal plot settings.
> * Describe what faceting is and apply faceting in ggplot.
> * Modify the aesthetics of an existing ggplot plot (including axis labels and color).
> * Build complex and customized plots from data in a data frame.

--------------

We start by loading the required packages. **`ggplot2`** is included in the **`tidyverse`** package.


```r
library(tidyverse)
```

If not still in the workspace, load the data we saved in the previous lesson.


```r
iris <- read_csv(file = "data/iris.csv")
plant_phys<-read_csv("data_output/phys_dates.csv")
```

## Plotting with **`ggplot2`**

**`ggplot2`** is a plotting package that makes it simple to create complex plots
from data in a data frame. It provides a more programmatic interface for
specifying what variables to plot, how they are displayed, and general visual
properties. Therefore, we only need minimal changes if the underlying data change
or if we decide to change from a bar plot to a scatter plot. This helps in creating
publication quality plots with minimal amounts of adjustments and tweaking.

**`ggplot2`** functions like data in the 'long' format, i.e., a column for every dimension,
and a row for every observation. Well-structured data will save you lots of time
when making figures with **`ggplot2`**

ggplot graphics are built step by step by adding new elements. Adding layers in
this fashion allows for extensive flexibility and customization of plots.

To build a ggplot, we will use the following basic template that can be used for different types of plots:

```
ggplot(data = <DATA>, mapping = aes(<MAPPINGS>)) +  <GEOM_FUNCTION>()
```

- use the `ggplot()` function and bind the plot to a specific data frame using the
      `data` argument


```r
ggplot(data = iris)
```

- define a mapping (using the aesthetic (`aes`) function), by selecting the variables to be plotted and specifying how to present them in the graph, e.g. as x/y positions or characteristics such as size, shape, color, etc.


```r
ggplot(data = iris, 
       mapping = aes(x = sepal.length, 
                     y = sepal.width))
```

- add 'geoms' – graphical representations of the data in the plot (points,
  lines, bars). **`ggplot2`** offers many different geoms; we will use some 
  common ones today, including:
  
      * `geom_point()` for scatter plots, dot plots, etc.
      * `geom_boxplot()` for, well, boxplots!
      * `geom_line()` for trend lines, time series, etc.  

To add a geom to the plot use the `+` operator. Because we have two continuous variables,
let's use `geom_point()` first:


```r
ggplot(data = iris, 
       mapping = aes(x = sepal_length, 
                     y = sepal_width))+
  geom_point()
```

![plot of chunk first-ggplot](figure/first-ggplot-1.png)

The `+` in the **`ggplot2`** package is particularly useful because it allows you
to modify existing `ggplot` objects. This means you can easily set up plot
templates and conveniently explore different types of plots, so the above
plot can also be generated with code like this:


```r
# Assign plot to a variable
iris_plot <- ggplot(data = iris, 
       mapping = aes(x = sepal_length, 
                     y = sepal_width))

# Draw the plot
iris_plot + 
    geom_point()
```

**Notes**

- Anything you put in the `ggplot()` function can be seen by any geom layers
  that you add (i.e., these are universal plot settings). This includes the x- and
  y-axis mapping you set up in `aes()`.
- You can also specify mappings for a given geom independently of the
  mappings defined globally in the `ggplot()` function.
- The `+` sign used to add new layers must be placed at the end of the line containing
the *previous* layer. If, instead, the `+` sign is added at the beginning of the line
containing the new layer, **`ggplot2`** will not add the new layer and will return an 
error message.


```r
# This is the correct syntax for adding layers
iris_plot +
  geom_point()

# This will not add the new layer and will return an error message
iris_plot
  + geom_point()
```

## Building your plots iteratively

Building plots with **`ggplot2`** is typically an iterative process. We start by
defining the dataset we'll use, lay out the axes, and choose a geom:


```r
ggplot(data = iris, 
       mapping = aes(x = sepal_width, 
                     y = sepal_length))+
    geom_point()
```

![plot of chunk create-ggplot-object](figure/create-ggplot-object-1.png)

We can also add colors for all the points:


```r
ggplot(data = iris, 
       mapping = aes(x = sepal_width, 
                     y = sepal_length))+
    geom_point(color = "blue")
```

![plot of chunk adding-colors](figure/adding-colors-1.png)

Or to color each species in the plot differently, you could use a vector as an input to the argument **color**. **`ggplot2`** will provide a different color corresponding to different values in the vector. Here is an example where we color with **`species`**:



```r
ggplot(data = iris, 
       mapping = aes(x = sepal_width, 
                     y = sepal_length))+
    geom_point(aes(color = species))
```

![plot of chunk color-by-species-1](figure/color-by-species-1-1.png)

We can also specify the colors directly inside the mapping provided in the `ggplot()` function. This will be seen by any geom layers and the mapping will be determined by the x- and y-axis set up in `aes()`.


```r
ggplot(data = iris, 
       mapping = aes(x = sepal_width, 
                     y = sepal_length, 
                     color = species))+
    geom_point()
```

![plot of chunk color-by-species-2](figure/color-by-species-2-1.png)

Notice that we can change the geom layer and colors will be still determined by **`species`**


```r
ggplot(data = iris, 
       mapping = aes(x = sepal_width, 
                     y = sepal_length, 
                     color = species))+
    geom_jitter()
```

![plot of chunk color-by-species-3](figure/color-by-species-3-1.png)

> ### Challenge
>
> Use what you just learned to create a scatter plot of the `plant_phys` dataset. Plot `Cond_day` over
> `Cond_night` with the species showing in different colors. Is this a good
> way to show this type of data?
> 
> ```r
> ggplot(data = plant_phys, mapping = aes(x = Cond_day, y = Cond_night)) +
>    geom_point(aes(color = species))
> ```
> 
> ```
> ## Error in FUN(X[[i]], ...): object 'species' not found
> ```
> 
> ![plot of chunk scatter-challenge](figure/scatter-challenge-1.png)

## Boxplot

We can use boxplots to visualize the distribution of sepal length within each species:


```r
ggplot(data = iris, mapping = aes(x = species, y = sepal_length)) +
    geom_boxplot()
```

![plot of chunk boxplot](figure/boxplot-1.png)

By adding points to boxplot, we can have a better idea of the number of
measurements and of their distribution:


```r
ggplot(data = iris, 
       mapping = aes(x = species, 
                     y = sepal_length)) +
    geom_boxplot()+
    geom_jitter(color = "tomato")
```

![plot of chunk boxplot-with-points](figure/boxplot-with-points-1.png)

Notice how the boxplot layer is behind the jitter layer? What do you need to
change in the code to put the boxplot in front of the points such that it's not
hidden?

> ### Challenges
>
> Boxplots are useful summaries, but hide the *shape* of the distribution. For
> example, if the distribution is bimodal, we would not see it in a
> boxplot. An alternative to the boxplot is the violin plot, where the shape 
(of the density of points) is drawn.
>
> - Replace the box plot with a violin plot; see `geom_violin()`.
>
> In many types of data, it is important to consider the *scale* of the
> observations.  For example, it may be worth changing the scale of the axis to
> better distribute the observations in the space of the plot.  Changing the scale
> of the axes is done similarly to adding/modifying other components (i.e., by
> incrementally adding commands). Try making these modifications:
>
> - Represent weight on the log~10~ scale; see `scale_y_log10()`.
>
> So far, we've looked at the distribution of weight within species. Try making a
> new plot to explore the distribution of another variable within each species.
>
> - Create a boxplot for `petal_length`. Overlay the boxplot layer on a jitter
>   layer to show actual measurements.
>
> - Add color to the data points on your boxplot according to `species`.
>



## Plotting time series data

Let's calculate the average photosynthesis rate per month for each Functional group (`Fgroup`). First we need to group the data and count records within each group:


```r
monthly_photo_fg <- plant_phys %>%
  group_by(Month, Fgroup)%>%
  summarize(mean_phot = mean(Photo))
```

Time series data can be visualized as a line plot with years on the x axis and counts
on the y axis:


```r
ggplot(data = monthly_photo_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot)) +
     geom_line()
```

![plot of chunk first-time-series](figure/first-time-series-1.png)

Unfortunately, this does not work because we plotted data for all the functional
groups together. We need to tell ggplot to draw a line for each species by
modifying the aesthetic function to include `group = Species`:


```r
ggplot(data = monthly_photo_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     group = Fgroup)) +
     geom_line()
```

![plot of chunk time-series-by-species](figure/time-series-by-species-1.png)

We will be able to distinguish species in the plot if we add colors (using `color` also automatically groups the data):


```r
ggplot(data = monthly_photo_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Fgroup)) +
     geom_line()
```

![plot of chunk time-series-with-colors](figure/time-series-with-colors-1.png)

## Faceting

**`ggplot2`** has a special technique called *faceting* that allows the user to split one
plot into multiple plots based on a factor included in the dataset. We will use it to
make a time series plot for each species:


```r
ggplot(data = monthly_photo_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot)) +
     geom_line()+
    facet_wrap(~ Fgroup)
```

![plot of chunk first-facet](figure/first-facet-1.png)

Now we would like to split the line in each plot by the functional group 
of each individual measured. To do that we need to make counts in the data frame grouped by `month`, `Species`, and `Fgroup`:


```r
mo_sp_fg <- plant_phys %>%
  group_by(Month, Species, Fgroup)%>%
  summarize(mean_phot = mean(Photo))
```

We can now make the faceted plot by splitting further by sex using `color` (within a single plot):


```r
ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
     geom_line()+
    facet_wrap(~ Fgroup) 
```

![plot of chunk facet-by-species-and-sex](figure/facet-by-species-and-sex-1.png)

Usually plots with white background look more readable when printed.  We can set
the background to white using the function `theme_bw()`. Additionally, you can remove
the grid:


```r
ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
     geom_line()+
    facet_wrap(~ Fgroup) +
     theme_bw() +
     theme(panel.grid = element_blank())
```

![plot of chunk facet-by-species-and-sex-white-bg](figure/facet-by-species-and-sex-white-bg-1.png)

## **`ggplot2`** themes

In addition to `theme_bw()`, which changes the plot background to white, **`ggplot2`**
comes with several other themes which can be useful to quickly change the look
of your visualization. The complete list of themes is available
at <https://ggplot2.tidyverse.org/reference/ggtheme.html>. `theme_minimal()` and
`theme_light()` are popular, and `theme_void()` can be useful as a starting
point to create a new hand-crafted theme.

The
[ggthemes](https://jrnold.github.io/ggthemes/reference/index.html) package
provides a wide variety of options (including an Excel 2003 theme).
The [**`ggplot2`** extensions website](https://www.ggplot2-exts.org) provides a list
of packages that extend the capabilities of **`ggplot2`**, including additional
themes.


> ### Challenge

> Use what you just learned to create a plot that depicts how the average `PD`
> of each species changes over a season. Color the lines by functional group
> 
> 
> ```r
> seasonPD <- plant_phys %>%
>                 group_by(Month, Species, Fgroup) %>%
>                  summarize(avg_PD = mean(PD))
> ggplot(data = seasonPD, 
>    mapping = aes(x=Month, 
>                  y=avg_PD, 
>                  color = Fgroup)) +
>    geom_line() +
>    facet_wrap(~ Species) +
>    theme_bw()
> 
> The `facet_wrap` geometry extracts plots into an arbitrary number of dimensions
> to allow them to cleanly fit on one page. On the other hand, the `facet_grid`
> geometry allows you to explicitly specify how you want your plots to be
> arranged via formula notation (`rows ~ columns`; a `.` can be used as
> a placeholder that indicates only one row or column).
> 
> Let's modify the previous plot to compare how the photosynthesis rate of functional
> groups have changed through time:
> ```
> 
> ```
> ## Error: <text>:12:5: unexpected symbol
> ## 11: 
> ## 12: The `facet_wrap`
> ##         ^
> ```

```r
ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
    geom_line() +
    facet_grid(Fgroup ~ .)
```

![plot of chunk average-weight-time-facet-sex-rows](figure/average-weight-time-facet-sex-rows-1.png)


```r
# One row, facet by column
ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
    geom_line() +
    facet_grid(.~ Fgroup)
```

![plot of chunk average-weight-time-facet-sex-columns](figure/average-weight-time-facet-sex-columns-1.png)

## Customization

Take a look at the [**`ggplot2`** cheat sheet](https://github.com/rstudio/cheatsheets/raw/master/data-visualization-2.1.pdf), and
think of ways you could improve the plot.

Now, let's change names of axes to something more informative than 'year'
and 'n' and add a title to the figure:


```r
ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
     geom_line()+
    facet_wrap(~ Fgroup) +
    labs(title = "Average photosynthesis over a season",
         x = "Month of observation",
         y = "Mean photosynthesis") +
    theme_bw()
```

![plot of chunk number-species-year-with-right-labels](figure/number-species-year-with-right-labels-1.png)

The axes have more informative names, but their readability can be improved by
increasing the font size:


```r
ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
     geom_line()+
    facet_wrap(~ Fgroup) +
    labs(title = "Average photosynthesis over a season",
         x = "Month of observation",
         y = "Mean photosynthesis") +
    theme_bw() +
    theme(text=element_text(size = 16))
```

![plot of chunk number-species-year-with-right-labels-xfont-size](figure/number-species-year-with-right-labels-xfont-size-1.png)

Note that it is also possible to change the fonts of your plots. If you are on
Windows, you may have to install
the [**`extrafont`** package](https://github.com/wch/extrafont), and follow the
instructions included in the README for this package.

After our manipulations, you may notice that the values on the x-axis are still not
properly readable. Let's change the orientation of the labels and adjust them
vertically and horizontally so they don't overlap. You can use a 90-degree
angle, or experiment to find the appropriate angle for diagonally oriented
labels:


```r
ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
     geom_line()+
    facet_wrap(~ Fgroup) +
    labs(title = "Average photosynthesis over a season",
         x = "Month of observation",
         y = "Mean photosynthesis") +
    theme_bw() +
    theme(axis.text.x = element_text(color = "grey20", 
                                     size = 12, angle = 90, 
                                     hjust = 0.5, vjust = 0.5),
                        axis.text.y = element_text(colour = "grey20", size = 12),
          text = element_text(size = 16))
```

![plot of chunk number-species-year-with-theme](figure/number-species-year-with-theme-1.png)

If you like the changes you created better than the default theme, you can save them as
an object to be able to easily apply them to other plots you may create:



```r
grey_theme <- theme(axis.text.x = element_text(colour = "grey20", size = 12, angle = 90, hjust = 0.5, vjust = 0.5),
                          axis.text.y = element_text(colour = "grey20", size = 12),
                          text = element_text(size = 16))
ggplot(plant_phys, 
       aes(x = Species, 
           y = Photo)) +
    geom_boxplot() +
    grey_theme
```

![plot of chunk number-species-year-with-right-labels-xfont-orientation](figure/number-species-year-with-right-labels-xfont-orientation-1.png)

> ### Challenge
> 
> With all of this information in hand, please take another five minutes to
> either improve one of the plots generated in this exercise or create a
> beautiful graph of your own. Use the RStudio [**`ggplot2`** cheat sheet](https://github.com/rstudio/cheatsheets/raw/master/data-visualization-2.1.pdf)
> for inspiration. Here are some ideas:
>
> * See if you can change the thickness of the lines.
> * Can you find a way to change the name of the legend? What about its labels?
> * Try using a different color palette (see
>   http://www.cookbook-r.com/Graphs/Colors_(ggplot2)/).

## Arranging and exporting plots

Faceting is a great tool for splitting one plot into multiple plots, but sometimes you may want to produce a single figure that contains multiple plots using different variables or even different data frames. The **`gridExtra`** package allows us to combine separate ggplots into a single figure using `grid.arrange()`:


```r
install.packages("gridExtra")
```


```r
library(gridExtra)

sp_photo_boxplot <- ggplot(plant_phys, 
       aes(x = Species, 
           y = Photo)) +
    geom_boxplot() +
    grey_theme

sp_photo_line<-ggplot(data = mo_sp_fg, 
       mapping = aes(x = Month, 
                     y = mean_phot, 
                     color = Species)) +
     geom_line()+
    facet_wrap(~ Fgroup) +
    labs(title = "Average photosynthesis over a season",
         x = "Month of observation",
         y = "Mean photosynthesis") +
    grey_theme

grid.arrange(sp_photo_boxplot, sp_photo_line, ncol = 2, widths = c(4, 6))
```

![plot of chunk gridarrange-example](figure/gridarrange-example-1.png)

In addition to the `ncol` and `nrow` arguments, used to make simple arrangements, there are tools for [constructing more complex layouts](https://cran.r-project.org/web/packages/gridExtra/vignettes/arrangeGrob.html). 

After creating your plot, you can save it to a file in your favorite format. The Export tab in the **Plot** pane in RStudio will save your plots at low resolution, which will not be accepted by many journals and will not scale well for posters. 

Instead, use the `ggsave()` function, which allows you easily change the dimension and resolution of your plot by adjusting the appropriate arguments (`width`, `height` and `dpi`). 

Make sure you have the `fig_output/` folder in your working directory.


```r
my_plot <- ggplot(plant_phys, 
       aes(x = Species, 
           y = Photo)) +
    geom_boxplot() +
    grey_theme
  
ggsave("fig_output/boxplot.png", my_plot, width = 15, height = 10)

# This also works for grid.arrange() plots
combo_plot <- 
grid.arrange(sp_photo_boxplot, sp_photo_line, ncol = 2, widths = c(4, 6))

ggsave("fig_output/combo_plot.png", combo_plot, width = 10, dpi = 300)
```

Note: The parameters `width` and `height` also determine the font size in the saved plot.


