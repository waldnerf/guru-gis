---
ID: 973
post_title: Download daymet data with R
author: Guru-Blard
post_excerpt: ""
layout: post
permalink: >
  http://www.guru-gis.net/download-daymet-data-with-r/
published: true
post_date: 2016-05-12 16:49:59
---
Hey folks ! It's been a while !

Interested in meteorological data over US ? Here is a way to easily download Daymet data inspired from <a href="http://www.khufkens.com/2014/03/18/daymetr-a-daymet-single-pixel-subset-tool-for-r/">DaymetR</a> package.

<em>Daymet is a collection of algorithms and computer software designed to interpolate and extrapolate from daily meteorological observations to produce gridded estimates of daily weather parameters. Weather parameters generated include daily surfaces of minimum and maximum temperature, precipitation, humidity, and radiation produced on a 1 km x 1 km gridded surface over the conterminous United States, Mexico, and Southern Canada.</em>

Download the "tiles" shapefile <a href="https://github.com/khufkens/daymetr/tree/master/tiles">here</a>.
 
<pre lang='rsplus'>
setwd("/guru-blard/DAYMET")

param = c("vp", "tmin", "tmax", "swe", "srad", "prcp", "dayl") %see here https://daymet.ornl.gov/overview.html for the available variable
tiles=c(11922:11925, 11742:11745) %id of the tiles of interest (cfr shapefile)
year_range=1985:2015

for (i in year_range) {
  for (j in tiles) {
    for (k in param) {
      download_string = sprintf("https://daymet.ornl.gov/thredds/fileServer/ornldaac/1219/tiles/%s/%s_%s/%s.nc", 
                                i, j, i, k)
      daymet_file = paste(k, "_", i, "_", j, ".nc", 
                          sep = "")
      cat(paste("Downloading DAYMET data for tile: ", 
                j, "; year: ", i, "; product: ", k, "\n", 
                sep = ""))
      try(download(download_string, daymet_file, quiet = TRUE, 
                   mode = "wb"), silent = FALSE)
    }
  }
}
</pre>