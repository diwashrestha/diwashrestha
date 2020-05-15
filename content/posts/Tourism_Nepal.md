---
title: "Tourism in Nepal"
date: 2019-11-22T23:29:14+05:45
author: false
draft: false
featuredImage: /images/tourism_in_nepal/preview.png
featuredImagePreview: /images/tourism_in_nepal/preview.png
---

### Introduction

 Nepal is celebrating year 2020 as “Tourism Year” targeting 2 million international tourist arrivals. You can learn more about the #VisitNepal2020. I want to see the trend/history of nepal tourism and extracted the data from the Wikipedia using rvest package in last blog.

In this section, I will work on the same data from the last blog try to analyse, create some visualization and understand the trend of Tourism in Nepal.

### Lets Start

I will load the required package for this blog.

```R
library(ggplot2)
library(dplyr)
library(tidyr)
library(purrr)
library(stringr)
library(lubridate)
library(gganimate)
library(plotly)
```

```
year    tourist_number   per_change
<int>      <int>           <dbl>
1993	  293567	      -12.2
1994	  326531	       11.2
1995	  363395	       11.3
1996	  393613	       8.3
1997	  421857	       7.2
1998	  463684	       9.9
1999	  491504	       6.0
2000	  463646	      -5.7
2001	  361237	      -22.1
2002	  275468	      -23.7
```

This is the data frame which I got after the extraction and cleaning process in the last blog. Now I will create a visualization from this data using ggplot2 and plotly.

### Visuals

```R
gplot <- ggplot(tourist_df, aes(
  x = year,
  y = tourist_number, fill = tourist_number
)) +
  geom_col() +
  labs(
    y = "Number of Tourist",
    title = "Number of Tourist Arrival from 1993 to 2018"
  ) +
  scale_fill_gradient(low="#fedb81", high="red")+
  theme_minimal()
ggplotly(gplot)
```

<iframe max-width: "100%" width="700" height = "600" frameborder="0" scrolling="no" src="//plotly.com/~Diwashrestha/4.embed?showlink=false"></iframe>

This barplot shows the number of arrival of international tourist from 1993 to 2017. The trend of tourist number was good from 1993 to 2000. Then from 2001, the flow of the international tourist decreased as it was the period where civil war was at its height. The country was in the emergency period.

In 2015 there was an earthquake which destroyed most of the historic sites in Kathmandu valley and with large physical and human casualties. This decreased the number of tourist in Nepal. I can see that 2016 onward the number of tourists arrival increased every year. In 2018 the number of tourist arrival crossed 1 million.

```R
gplot <- ggplot(tourist_df, aes(
  x = year,
  y = per_change, fill = per_change
)) +
  geom_col() +
  labs(
    y = "Percent Change of Tourist",
    title = "Percentage Change  of Tourist Arrival from 1993 to 2018"
  ) +
  scale_fill_gradient(low="#fedb81", high="red")+
  theme_minimal()
ggplotly(gplot)
```

<iframe max-width: "100%" width="700" height = "400" frameborder="0" scrolling="no" src="//plotly.com/~Diwashrestha/8.embed?showlink=false"></iframe>

This plot shows the percentage change in the flow of the tourist arrival every year.

### Top 10 Country
In this section, I will find the top 10 countries with must tourist to Nepal.



|  Rank  |  Country | 2013  | 2014 |   2015  |  2016 |  2017 |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| *chr*  |   *chr* |  *chr* | *chr* |   *chr* |  *chr* | *chr* |
| 1	 | India	|160832 |	118249	|75124 |	135343|	180974|
| 2	| China	|104664	| 104005 |	66984 |	123805 |	113173|
| 3	| United States |	79146 |	53645	| 42687	|49830	|47355|
| 4	| United Kingdom |	51058	|46295|	29730|	36759	|35688|
| 5	| Sri Lanka	|45361|	57521|	44367|	37546|	32736|
| 6	| Thailand	|39154	|26722	|32338	|33422	|40969|
| 7	| South Korea|	34301|	25171|	18112	|23205|	19714|
| 8	| Australia	|33371	|25507|	16619|	24516|	20469|
| 9	| Myanmar	|30852	|25769|	21631|	N/A	| N/A |
| 10| Germany	|29918	|23812	|16405	|18028	|22263|

This is the data I got from the last blog and now I need to extract the top 10 countries from this dataframe.

con_tour_df<- head(con_tour_df,10)
This dataframe has the years in a different column. I keep all the years in year column along with there tourist arrival value.

```R
con_tour_df <- pivot_longer(con_tour_df,-c(Country,Rank),names_to = "year")
con_tour_df$year <- as.numeric(con_tour_df$year)
con_tour_df$value <- as.numeric(con_tour_df$value)
```
Now, I need to rank the country based on the number of tourist arrival from that country. I use mutate() to create a new column “rank”.

```R
con_tour_df <- con_tour_df%>%
  group_by(year) %>%
  mutate(rank = rank(-value),Value_lbl = paste0(" ",value))%>% filter(rank <= 10)

con_tour_df$value <- as.integer(con_tour_df$value)
```


|Rank | Country | year | value | rank | Value_lbl|
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| <chr> | <chr> | <dbl> | <int> | <dbl> |<chr>|
| 1 | India | 2013 | 	160832 |	1 |	160832 |
| 1 | India	| 2014 |	118249 |	1 |	118249 |
| 1 | India	| 2015 |	75124  |	1 |	75124 |
| 1 | India	| 2016 |	135343 |	1 |	135343 |
| 1	| India	| 2017 |	180974 |	1 |	180974 |
| 2	| China	| 2013 |	104664 |	2 |	104664 |
| 2	| China	| 2014 |	104005 |	2 |	104005 |
| 2	| China	| 2015 |	66984  |	2 |	66984 |
| 2	| China	| 2016 |	123805 |	2 |	123805 |
| 2	| China	| 2017 |	113173 |	2 |	113173 |


This the final data frame that I got after cleaning. Now, it is time to create some visualization/animation.





I am using the ggplot2 package to create static visualization and gganimate to create beautiful animations.

```R
colors <- c("#a6cee3", "#e31a1c", "#b2df8a", "#33a02c",
            "#fb9a99", "orange", "#fdbf6f", "#ff7f00", 
            "#cab2d6", "#6a3d9a", "#b15928", "#fb8072",
            "#80b1d3", "#fdb462", "#b3de69")
```

In this visualization, i need 10 different colours to show a different country in the animation. I created a colour palette and named colors which will be used while creating visualization below.

```R
ggplot(con_tour_df, aes(x = rank, y = value, group = Country)) +
  geom_bar(stat = "identity", aes(fill = Country)) +
  geom_text(aes(y = 0, label = paste(Country, " ")), vjust = 0.2, hjust = 1) +
  geom_text(aes(y = value, label = Value_lbl, hjust = 0)) +
  scale_y_continuous(labels = scales::comma) +
  scale_x_reverse() +
  coord_flip(clip = "off", expand = FALSE) +
  scale_fill_manual(values = colors) +
  theme_minimal() +
  theme(
    axis.line = element_blank(),
    axis.text.x = element_blank(),
    axis.text.y = element_blank(),
    axis.ticks = element_blank(),
    axis.title.x = element_blank(),
    axis.title.y = element_blank(),
    legend.position = "none",
    panel.background = element_blank(),
    panel.border = element_blank(),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    panel.grid.major.x = element_line(size = .1, color = "grey"),
    panel.grid.minor.x = element_line(size = .1, color = "grey"),
    plot.title = element_text(size = 25, hjust = 0.5, face = "bold", vjust = 1),
    plot.subtitle = element_text(size = 18, hjust = 0.5, face = "italic"),
    plot.caption = element_text(size = 8, hjust = 0.5, face = "italic", color = "grey"),
    plot.background = element_blank(),
    plot.margin = margin(2, 2, 2, 4, "cm")) +
  transition_states(year, transition_length = 3, state_length = 1) +
  enter_drift(x_mod = -2) +
  exit_drift(x_mod = 2) +
  ease_aes("cubic-in") +
  view_follow(fixed_x = TRUE) +
  labs(title = "Tourist Arrival by country : {closest_state}", 
       subtitle = "Top 10 Countries")
```

![](/images/tourist_arrival.gif)
### Conclusion

In this blog I showed the interactive visualization made with ggplot2 and plotly. I also made the animation showing the number of tourist arrival based on country from 2013 to 2017.

Feel free to send to me your feedback and suggestions regarding this post!

###### Reference
[AbdulMajedRaja Rs.](https://towardsdatascience.com/create-animated-bar-charts-using-r-31d09e5841da) [gganimate](https://gganimate.com/index.html) [ggplot2](https://ggplot2.tidyverse.org/index.html)