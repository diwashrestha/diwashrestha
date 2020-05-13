---
title: "Data Visualization with ggplot2"
date: 2020-05-03T23:08:57+05:45
author: false
draft: false
featuredImage: /images/data_viz_ggplot/preview.jpg
featuredImagePreview: /images/data_viz_ggplot/preview.jpg
---

We have seen lots of visuals in our life like pictures, animations, and some graphical plots. Graphics help or make easy to get an idea or understand anything. Visualization is an important part of the Data science or Data Analysis. In this blog, we will learn about visualization in R by creating different type of plots. 

### Lets Visualize

We will use ggplot2 package which follows the grammar of graphics. Its very powerful tool for visualization and very famous in R community.We will use the mpg datasets in ggplot2 package to create the visual graphs.

```
Classes 'tbl_df', 'tbl' and 'data.frame':   234 obs. of  11 variables:
 $ manufacturer: chr  "audi" "audi" "audi" "audi" ...
 $ model       : chr  "a4" "a4" "a4" "a4" ...
 $ displ       : num  1.8 1.8 2 2 2.8 2.8 3.1 1.8 1.8 2 ...
 $ year        : int  1999 1999 2008 2008 1999 1999 2008 1999 1999 2008 ...
 $ cyl         : int  4 4 4 4 6 6 6 4 4 4 ...
 $ trans       : chr  "auto(l5)" "manual(m5)" "manual(m6)" "auto(av)" ...
 $ drv         : chr  "f" "f" "f" "f" ...
 $ cty         : int  18 21 20 21 16 18 18 18 16 20 ...
 $ hwy         : int  29 29 31 30 26 26 27 26 25 28 ...
 $ fl          : chr  "p" "p" "p" "p" ...
 $ class       : chr  "compact" "compact" "compact" "compact" ...
 ```

From above code, we found out that mpg has 234 rows and 11 columns. We will create different type of visualization using ggplot2 package.

### Scatterplot

A scatterplot uses the value of two variables to plot on the graph. A scatterplot shows the correlation between two variables in the graph. In this scatterplot, we used color to define the distribution of data using the third variable.

```R
ggplot(data = mpg) + 
  geom_point(aes(x = cty, y = displ, colour = cyl)) +
  labs(title = "Scatterplot with ggplot2", x = "CTY", y = "displ") + 
  theme_minimal()
```

The ggplot() takes data frame input then we can define the required graph type using geom_graphtype(). Here I am using geom_point() to show scatterplot. Then we need to assign the x and y value from the data frame that is going to be used in creating graph. We can assign a title, labels to x-axis and y-axis of the graph using the labs().ggplot2 has multiple themes which are built in and I am using theme_minimal.

### Bar Chart

Barchart uses 2 variable to show its plot using rectangular shape. Barchart height shows the sum of y axis data on basis of the x-axis variable.

```R
ggplot(data = mpg, aes(x = hwy, y = displ)) +
  geom_col(aes(fill = cyl)) + 
  labs(title = "Barchart with ggplot2") +
  theme_minimal()
```

### Count Chart

The Count chart uses only one variable and plots the presence count of variable.

```R
ggplot(data = mpg) + geom_bar(aes(manufacturer)) +
  coord_flip() +
  labs(title = "Count Chart with ggplot2") + 
  theme_minimal()
```

### Bubble Chart

The Bubble chart is same as a scatterplot only difference is it uses an extra third variable to show the size of the points.

```R
ggplot(data = mpg) + 
  geom_point(aes(x = cty, y = displ, size = hwy, 
  colour = "red", alpha = 0.5),show.legend = F) + 
  labs(title = "Bubble Chart with ggplot2") +
  theme_minimal()
```

### Line Chart

The line chart is plot which shows information by connecting series of points with straight lines.In above picture, we can see the line plot.B ut this dataset is not good to plot a time series so we will create a data frame with a random variable and plot another line plot.

```R
x <- sample(1:100, 20, replace = FALSE)
y <- sample(1:100, 20, replace = FALSE)

# combine x and y create a dataframe
data <- data.frame(x, y)
ggplot(data = data, aes(x = x, y = y)) + 
  geom_point() + 
  geom_line() + 
  labs(title = "Line Plot with ggplot2") + 
  theme_minimal()
```


In this line chart, we can see the series of the data points connected by the lines. Time series and finacial analysis use line plots .

### Pie Chart

Piechart got its name as it looks like a pie or round shape. It plots counts of a variable in a circle and it plots to take the whole circle as 100%. A pie chart is not good for multiple variable visualization.

```R
ggplot(data = mpg, aes(x = " ")) + 
  geom_bar(width = 1, aes(fill = drv)) +
  coord_polar("y", start = 0) + 
  labs(title = "Pie Chart") + 
  theme_minimal()
```

### Histogram

Histogram is a rectangular plot which gives us the frequency of the variable by making different ranges. It shows the distribution of a continuous variable. Histogram is different from the bar chart, bar chart relates to Two variables but histogram relates to One variable. Histogram uses the bin that divides the entire range of values into a series of intervals—and then count how many values fall into each interval.Histogram also shows the distribution of the data.

```R
# default value for bins =30
ggplot(data = mpg, aes(displ)) + 
  geom_histogram(color = "orange") + 
  labs(title = "Histogram with ggplot2") + 
  theme_minimal()
```

### Area Plot

The only difference between Area and line plot is the area plot is filled with color. Combination of two or more area plot form Stacked area plot.

```R
ggplot(data = mpg, aes(x = cty, fill = drv)) + 
  geom_area(stat = "bin", alpha = 0.7) +
  labs(title = "Areaplot with ggplot2") + 
  theme_minimal()
```

### Box Plot

Boxplot plots five number summary in single plot ie minimum, first quartile, median, third quartile, and maximum. The box upper and lower side shows third and the first quartile respectively. Middle line in the box shows the median value. The line above and below the box shows maximum and minimum value and points outside this line are called outliers

```R
ggplot(data = mpg, aes(x = cyl, y = cty, fill = cyl)) + 
  geom_boxplot() +
  labs(title = "Boxplot with ggplot2") + 
  theme_minimal()
```

### Density Plot

Density plot shows the distribution of the data. This plot shows the smooth distribution by smoothing out the noise. The peaks of Density Plot display where values are concentrated over the interval.

```R
ggplot(data = mpg, aes(hwy)) + 
  geom_density(fill = "blue", alpha = 0.3) + 
  labs(title = "Density Plot with ggplot2") + 
  theme_minimal()
```

These are the few of the many visualization types which are used in Data Analysis or Data Science. I hope it this was helpful for you.