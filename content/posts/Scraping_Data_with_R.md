---
title: "Scraping_Data_with_R"
date: 2020-05-03T23:23:51+05:45
author: false
draft: false
featuredImage: /images/scrap_data/preview.jpg
featuredImagePreview: /images/scrap_data/preview.jpg
---
In Data Analysis require a data on which we can perform analysis and find insights. We always don’t get clean open data for our analysis so we need to create, find our data. In this post, I describe how to extract data from a website using R.


Nepal is a Himalayan nation with beautiful mountains, ancient cultures, traditions and rich history. Nepal is celebrating year 2020 as “Tourism Year” targeting 2 million international tourist arrivals. You can learn more about the #VisitNepal2020.

I wanted to find out the history of international tourist arrival in Nepal and found data in Wikipedia I wanted to extract the data from Wikipedia and analyse and create some visualization and reports. This post will highlight how I got to scraping out this data using R’s package rvest. rvest is an R package that makes it easy for us to scrape data from the web.

### Lets Start


Above figure shows the website from which I will extract the data.

I will import the rvest package which makes it easy to scrape (or harvest) data from HTML web pages.

```R
library(rvest)
library(stringr)
library(tidyverse)
```

We need the link from which we want to scrape the data. I opened the wiki webpage and I will scrape the table shown below.

I will use the read_html() function which reads the HTML page.

```R
wikipage <- read_html("https://en.wikipedia.org/wiki/Tourism_in_Nepal")
```

### Data Extraction

We need the CSS selector of the table from which we want to scrape the data. I opened the inspect view which can be opened by clicking the right button of the webpage and click the inspect. for the table, the CSS selector is “table.wikitable”.



I passed the CSS selector to the html_nodes() function and result is passed to the html_table() function using pipeline(%>%). The below code extract the arrival table data as data frame.

```R
table <- wikipage %>%
html_nodes("table.wikitable") %>%
html_table(header=T)
table <- table[[1]]
 
#add the table to a dataframe
tourist_df <- as.data.frame(table)
```

I will change the column to a short form so that it will be easy to work on it.

```R
names(tourist_df) <- c("year","tourist_number","per_change")
```

|year  |tourist_number|  per_change |
| -------- | -------- | ------|
|<int>   |   <chr> |         <chr> |
|1993	 | 293,567 | 	-12.2% | 
1994	 | 326,531 |    +11.2% | 
1995	 | 363,395 | 	+11.3% | 
1996	 | 393,613 |    +8.3%  | 
1997	 | 421,857 | 	+7.2%  | 
1998	 | 463,684 | 	+9.9%  | 
1999	 | 491,504 | 	+6.0%  | 
2000	 | 463,646 | 	-5.7%  | 
2001	 | 361,237 | 	-22.1% | 
2002	 | 275,468 | 	-23.7  | 


The tourist_number column has “,” between the number which makes it string. I removed the “,” using str_remove() from stringr package.

```R
tourist_df$tourist_number <- str_remove(tourist_df$tourist_number,",")
tourist_df$per_change <- str_remove(tourist_df$per_change, "%")
tourist_df$tourist_number <- str_remove(tourist_df$tourist_number,",")
```

As the extracted data was string, I convert columns to integer and numeric type.

```R
tourist_df$tourist_number <- as.integer(tourist_df$tourist_number)
tourist_df$per_change <- as.numeric(tourist_df$per_change)
tourist_df$year <- as.integer(tourist_df$year)
```

This is the final dataframe after cleaning.

| year | tourist_number | per_change |
| ------ | ------ | ------ |
| <int>  | <int> | <dbl>   |
| 1993  | 293567 |	-12.2 |
| 1994	| 326531 |	11.2  |
| 1995	| 363395 |	11.3  |
| 1996	| 393613 |	8.3   |
| 1997	| 421857 |	7.2   |
| 1998	| 463684 |	9.9   |
| 1999	| 491504 |	6.0   |
| 2000	| 463646 |	-5.7  |
| 2001	| 361237 |	-22.1 |
| 2002	| 275468 |	-23.7 |

In the same way, I can extract another arrival from the country table. both tables have the same selector “table.wikitable” when the html_nodes extract the tables it keeps data in list form in the table object. We can extract the data for the arrival by country from table list using the index 2.

```R
table <- wikipage %>%
html_nodes("table.wikitable") %>%
html_table(header=T)
table <- table[[2]]
 
#add the table to a dataframe
con_tour_df <- as.data.frame(table)
```

I rename the columns of the dataframe .

```R
names(con_tour_df)<- c("Rank","Country",2013,2014,2015,2016,2017)
```


| Rank | Country|  2013 | 2014|  2015 | 2016|  2017| 
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| <int> | <chr>|  <chr> | <chr> | <chr> | <chr> | <chr>| 
| 1	| India| 	160,832	| 118,249	| 75,124	| 135,343	| 180,974| 
| 2	| China| 	104,664	| 104,005	| 66,984	| 123,805	| 113,173| 
| 3	| United States| 	79,146	| 53,645	| 42,687	| 49,830|	47,355| 
| 4	| United Kingdom| 	51,058	| 46,295	| 29,730	| 36,759|	35,688| 
| 5	| Sri Lanka| 	45,361	| 57,521	| 44,367	| 37,546| 	32,736| 
| 6	| Thailand| 	39,154	| 26,722	| 32,338|	33,422	| 40,969| 
| 7	| South Korea	| 34,301	| 25,171	| 18,112	|23,205	| 19,714| 
| 8	| Australia	| 33,371	| 25,507	| 16,619	| 24,516	| 20,469| 
| 9	| Myanmar	| 30,852	| 25,769	| 21,631	| N/A| 	N/A| 
| 10	| Germany	| 29,918	| 23,812	| 16,405	| 18,028	| 22,263| 


This the data how it look like after extracting using rvest.

I remove the “,” from every column using the purr package map() and str_remove() of the stringr package.

```R
con_tour_df <- con_tour_df%>% map(str_remove,",") %>% as_tibble()
```

This is the final dataframe after some cleaning process.

| Rank | Country|  2013 | 2014|  2015 | 2016|  2017| 
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| <int> | <chr>|  <chr> | <chr> | <chr> | <chr> | <chr>| 
| 1	| India| 	160832	| 118249	| 75124	| 135343	| 180974| 
| 2	| China| 	104,664	| 104005	| 66984	| 123805	| 113173| 
| 3	| United States| 	79146	| 53645	| 42687	| 49830|	47355| 
| 4	| United Kingdom| 	51058	| 46295	| 29730	| 36759	|35688| 
| 5	| Sri Lanka| 	45361	| 57521	| 44367	| 37546| 	32736| 
| 6	| Thailand| 	39154	| 26722	| 32338	|33422	| 40969| 
| 7	| South Korea	| 34301	| 25171	| 18112	|23205	| 19714| 
| 8	| Australia	| 33371	| 25507	| 16619	| 24516	| 20469| 
| 9	| Myanmar	| 30852	| 25769	| 21631	| N/A | 	N/A | 
| 10	| Germany	| 29918	| 23812	| 16405	| 18028	| 22263| 

Finally, I have extracted the data from Wikipedia. I will analyse this data and share the insights in the next post.

Feel free to send to me your feedback and suggestions regarding this post!

###### References

* [An introduction to web scraping using R](https://www.freecodecamp.org/news/an-introduction-to-web-scraping-using-r-40284110c848/)

* [Analytics Vidhya, Beginner’s guide on web scraping](https://www.analyticsvidhya.com/blog/2017/03/beginners-guide-on-web-scraping-in-r-using-rvest-with-hands-on-knowledge/)