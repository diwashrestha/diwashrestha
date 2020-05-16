---
title: "Interactive Visualization with ggplot2 and Plotly"
date: 2020-05-03T23:30:16+05:45
author: false
draft: false
featuredImage: /images/ggplot_plotly/preview.png
featuredImagePreview: /images/ggplot_plotly/preview.png
---

I love to work with data and while analyzing the data visualization helps a lot to show the result of analysis. In this blog i will show how to create interactive visualization with ggplot2 and plotly package in Rstudio Environment. 



[GGplot2](https://ggplot2.tidyverse.org/) is powerfull visualization package based on [The grammar of graphics](https://www.springer.com/gp/book/9780387245447) and part of [Tidyverse](https://www.tidyverse.org/). It is easy to use and powerful to create any type of visualization. The problem is it can only make beautiful static visualization.

[Plotly](https://plotly.com/r/) is a free opensource library for interactive visualization. Plotly can convert the high-quality ggplot graphs to interactive graphs .We will use plotly along with ggplot2 to create beautiful interactive visualization. 


At First we will create a static visualization with ggplot2 and then make the ggplot graph interactive with plotly package. We will use data about [Nobel Prize](https://www.nobelprize.org/) winner to create the visualization.

### Load Packages

lets load the required packages in our R environment.


```R
library(readr) # read csv file
library(ggplot2) # for visualization
library(dplyr) # data manipulation
library(plotly) # interactive data visualization
```

### Load Data

I am using the [Nobel Prize](https://www.nobelprize.org/) winner data which is sourced from [tidytuesday](https://github.com/rfordatascience/tidytuesday) github which release every week new data project.

```R
data <- read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-05-14/nobel_winners.csv")
```

Here is the data that i will use to create visualization.

| prize_year | category | prize |
| ------ | ------ | ------ |
| <dbl> | <chr> | <chr> |
| 1901 |	Chemistry |	The Nobel Prize in Chemistry 1901 |	
| 1901 | Literature	 | The Nobel Prize in Literature 1901 | 	
| 1901	 | Medicine	 | The Nobel Prize in Physiology or Medicine 1901 | 	
| 1901	 | Peace	 | The Nobel Peace Prize 1901	 | 
| 1901	 | Peace	 | The Nobel Peace Prize 1901	 | 
| 1901	 | Physics	 | The Nobel Prize in Physics 1901 | 	
6 rows | 1-3 of 18 columns

### Create a barplot

Now we can create bar plot showing the number of the [Nobel Prize](https://www.nobelprize.org/) winner and the prize category. At first we create a static plot with ggplot2 .

```R
nobelbar <- ggplot(data) + geom_bar(aes(category, fill = category)) +
  theme_minimal() +
  theme(legend.position = "none", axis.title.x = element_blank()) + 
  labs(title = "Number of Nobel prize winner by category") +
  scale_fill_brewer(palette = "Set2")

nobelbar
```

![Nobel Prize](/images/ggplot_plotly/nobel_prize.png)

### Make ggplot interactive


Here we will pass the ggplot to the ggplotly function from plotly package ,which creates interactive graph.

```R
ggplotly(nobelbar, tooltip = c("fill", "count"))
```

<iframe width="750" height="500" frameborder="0" scrolling="no" src="/plotly/nobelprize.html"></iframe>

I like to keep the plot clean , So I will hide the Modebar of the plotly.

```R
nobel_plot <- ggplotly(nobelbar, tooltip = c("fill", "count")) %>%
  config(displayModeBar = F)
```

<iframe width="750" height="500" frameborder="0" scrolling="no" src="/plotly/nobelprize.html"></iframe>


### Save the plot

We can use this interactive plot in website,reports and also share with others . Lets save it locally and we can share the html file.

```R
htmlwidgets::saveWidget(as_widget(nobel_plot), "nobelprize.html")
```

### Wrapping up

Atlast we made the ggplot2 charts interactive using a single ggplotly() function.You can find lots of data in [Tidytuesday repo](https://github.com/rfordatascience/tidytuesday) for analysis  and create great visualization to show the insights.


