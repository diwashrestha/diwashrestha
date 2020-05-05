---
title: "Interactive_Visualization_Plotly_ggplot"
date: 2020-05-03T23:30:16+05:45
draft: false
---

I love to work with data while analysing the data, I like to explore it and for that best thing is to visualize the data. I create a visualization to show the information or finding from the analysis. For this task, I use the ggplot2 package from the Tidyverse Universe.



GGplot2 is powerfull visualization package based on The grammar of graphics and part of tidyverse. It is easy to use and powerful to create any type of visualization. The problem is it can only make beautiful static visualization.

I write blogs, create dashboard and reports. The interactive graph allows users to interact with a graph which is more interactive than a static graph. It is not possible to create an interactive graph with ggplot2.

Plotly can convert the high-quality ggplot graphs to interactive using the ggploty function in plotly package. plotly is a free opensource library for interactive visualization. In this blog, I will create a static visualization with ggplot2 and then make the ggplot graph interactive with plotly package. I will use data about Nobel prize winner to create the visualization. Lets get visualizing

### Load Packages

lets load the required packages in our R environment.


```R
library(readr) # read csv file
library(ggplot2) # for visualization
library(dplyr) # data manipulation
library(plotly) # interactive data visualization
```

### Load Data

I am using the nobel prize winner data which is sourced from tidytuesday github which release every week new data project.

```R
data <- read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-05-14/nobel_winners.csv")
```

Here is the data that i will use to create visualization.

prize_year
<dbl>
category
<chr>
prize
<chr>
1901	Chemistry	The Nobel Prize in Chemistry 1901	
1901	Literature	The Nobel Prize in Literature 1901	
1901	Medicine	The Nobel Prize in Physiology or Medicine 1901	
1901	Peace	The Nobel Peace Prize 1901	
1901	Peace	The Nobel Peace Prize 1901	
1901	Physics	The Nobel Prize in Physics 1901	
6 rows | 1-3 of 18 columns

### Create a barplot

I created a barplot showing the number of the nobel prize winner and the prize category. At first i create a static plot with ggplot2 .

```R
nobelbar <- ggplot(data) + geom_bar(aes(category, fill = category)) +
  theme_minimal() +
  theme(legend.position = "none", axis.title.x = element_blank()) + 
  labs(title = "Number of Nobel prize winner by category") +
  scale_fill_brewer(palette = "Set2")

nobelbar
```

### Make ggplot interactive


I will pass the ggplot to the ggplotly function from plotly package ,which creates interactive graph.

```R
ggplotly(nobelbar, tooltip = c("fill", "count"))
```


I like to keep the plot clean , So I hide the Modebar of the plotly.

```R
nobel_plot <- ggplotly(nobelbar, tooltip = c("fill", "count")) %>%
  config(displayModeBar = F)
```



### Save the plot

I can use this interactive plot in website,reports and also share with others . Lets save it locally and we can share the html file.

```R
htmlwidgets::saveWidget(as_widget(nobel_plot), "nobelprize.html")
```

### Wrapping up

Atlast we made the ggplot2 charts interactive using a single ggplotly() function.You can find lots of data in Tidytuesday repo and create great visualization to show the insights.


