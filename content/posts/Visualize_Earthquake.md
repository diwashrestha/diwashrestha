---
title: "Visualizing Earthquake"
date: 2019-07-19T23:19:18+05:45
showdate: false
author: false
draft: false
featuredImage: /images/visual_earthquake/preview.png
featuredImagePreview: /images/visual_earthquake/preview.png
---

According to [USGS](https://www.usgs.gov/about/about-us/who-we-are):

> An earthquake is caused by a sudden slip on a fault. The tectonic plates are always slowly moving, but they get stuck at their edges due to friction. When the stress on the edge overcomes the friction, there is an earthquake that releases energy in waves that travel through the earth's crust and cause the shaking that we feel.

Earthquake size can be very weak which can’t be felt to those which can destroy cities. The size of the earthquake is determind by the magnitude of the earthquake in seismic scale.

{{<image src="/images/visual_earthquake/earthquake.jpg">}}


In this blog, We will create an interactive visualization of the 20 largest earthquakes in the world. We will use the data from [Earthquake.usgs.gov](https://www.usgs.gov/natural-hazards/earthquake-hazards/earthquakes), US government website about geological research. The data is extracted using rvest package(web scraping package). We will use [highcharter](http://jkunst.com/highcharter/index.html) package to create the visualization.

```R
# loading library
library(rvest)
library(tidyverse)
library(highcharter)
library(geojsonio)
library(geojsonlint)
library(sp)
library(jsonlite)

# loading data
# webscraping using rvest 
url <- "https://earthquake.usgs.gov/earthquakes/browse/largest-world.php"
earthquake <- url %>%
  html() %>%
  html_nodes(xpath = "/html/body/main/div/div/table") %>%
  html_table()
earthquake <- earthquake[[1]]

earthquake <- rename(earthquake, lat = Latitude, lon = Longitude)
earthquake <- select(earthquake, -"")

# convert gis cordinates to longitude and latitudes
earthquake$lat <- as.numeric(char2dms(earthquake$lat, chd = "°"))
earthquake$lon <- as.numeric(char2dms(earthquake$lon, chd = "°"))
```

Here is the look of data that i will use to create the visualization of the earthquake.It has Name of the earthquake,magnitude,location,time,date,logitude and latitude of the earthquake.


| Mag |	Location |	Alternative Name |	Date (UTC) | 	Time (UTC) |	lat |	lon |	References |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| 9.5 |	Bio-Bio, Chile |	Valdivia Earthquake |	1960-05-22 |	19:11 |	-38 |	-73 |	Kanamori & Anderson, 1975 |
| 9.2	| Southern Alaska |	1964 Great Alaska Earthquake, Prince William Sound Earthquake, Good Friday Earthquake |	1964-03-28 |	03:36	| 60	| -147 |	Kanamori & Anderson, 1975 |
| 9.1 | Off the West Coast of Northern Sumatra |	Sumatra-Andaman Islands Earthquake, 2004 Sumatra Earthquake and Tsunami, Indian Ocean Earthquake |	2004-12-26 |	00:58 |	3 |	95 |	Duputel et al., 2012 |
|9.1 |	Near the East Coast of Honshu, Japan |	Tohoku Earthquake |	2011-03-11 |	05:46 |	38 |	142 |	Duputel et al., 2012 |
|9.0 |	Off the East Coast of the Kamchatka Peninsula, Russia| 	Kamchatka, Russia |	1952-11-04 |16:58	 | 52 |	159 |	Kanamori, 1976 |
|8.8 |	Offshore Bio-Bio, Chile |	Maule Earthquake |	2010-02-27 |	06:34	|-36 |	-72 |	Duputel et al., 2012 |


In this visualization we will also use data of the tectonic plates of the earth. We will be  using geojson of the earthmap and the tectonic to create the map.The data about the tectonic plates and the world map is taken  from [highcharter](http://jkunst.com/highcharter/highmaps.html) example.

```R
getContent <- function(url) {
  library(httr)
  content(GET(url))
}

world <- getContent("https://raw.githubusercontent.com/johan/world.geo.json/master/countries.geo.json")
# is text
world <- jsonlite::fromJSON(world, simplifyVector = FALSE)

plates <- getContent("http://cedeusdata.geosteiniger.cl/geoserver/wfs?srsName=EPSG%3A4326&typename=geonode%3Amundo_limites_placas&outputFormat=json&version=1.0.0&service=WFS&request=GetFeature")
```

The earthquake dataframe also need to be changed to geojson which is done using the geojson_json() from geojsonio package.

Finally we will use the follwing code to create interactive visualization of the 20 largest earthquake in the world.

```R
highchart(type = "map") %>%
  hc_chart(backgroundColor = "#ccc") %>%
  hc_add_series(
    mapData = world, showInLegend = FALSE, nullColor = "#eee",
    borderWidth = 0
  ) %>%
  hc_add_series(
    data = plates, type = "mapline", lineWidth = 1, zIndex = -1, geojson = TRUE,
    color = "blue", name = "Plates",
    tooltip = list(pointFormat = "{point.properties.TIPO}")
  ) %>%
  hc_add_series(type = "mappoint", data = earthquake_geojson,
  color = hex_to_rgba("#ff4444", 0.6), name = "Earthquake", 
  size = "110%", tooltip = list(pointFormat = "Location : {point.properties.Location} <br>
                Magnitude : {point.properties.Mag} <br>
                Date : {point.properties.Date (UTC)},
                {point.properties.Time (UTC)}")) %>%
  hc_tooltip(
    backgroundColor = "#f2e3b8",
    borderWidth = 2
  ) %>%
  hc_title(text = "20 Largest Earthquakes in the World") %>%
  hc_mapNavigation(enabled = TRUE)
```

![Earthquake](/images/visual_earthquake/earthquake.gif)

In this visualization we can see almost all of the earthquake occurs on the intersection of two tectonic plates. We can use mouse to hover over the point and get details about the earthquake through tooltips.