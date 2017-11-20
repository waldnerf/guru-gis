---
ID: 1046
post_title: >
  Get angle/orientation/direction of GPS
  track using R
author: Guru-Blard
post_excerpt: ""
layout: post
permalink: >
  http://www.guru-gis.net/get-angleorientationdirection-of-gps-track-using-r/
published: true
post_date: 2017-11-20 14:56:08
---
Hey,

Today a bit of trSIGonometry with this function to compute the angle/orientation/direction between 2 points of a GPX file.

<pre lang='rsplus'>
############################
#Function
############################

angle <- function(pt1, pt2) {
  conv = 180 / pi # conversion radians / degrees
  diff <- pt2 - pt1
  diff.abs <- abs(diff)
  if (diff[1]>=0 & diff[2]>=0){angle <- pi/2 - atan(diff.abs[2] / diff.abs[1])}
  if (diff[1]>=0 & diff[2]<=0){angle <- pi/2 + atan(diff.abs[2] / diff.abs[1])}
  if (diff[1]<=0 & diff[2]>=0){angle <- 3*pi/2 + atan(diff.abs[2] / diff.abs[1])}
  if (diff[1]<=0 & diff[2]<=0){angle <- pi + (pi/2- atan(diff.abs[2] / diff.abs[1]))}
  return(angle * conv)
}

############################
#Script
############################

library(rgdal)
library(raster)

#Set folder
setwd("/home/GuruBlard/gpx/")

# Load file names
filenames <- list.files(pattern=".gpx$")

# Iteration through all gpx files
for (filename in filenames){
  # Load the GPX
  
  #ogrListLayers("export.gpx")
  gpx.trackpoints <- readOGR(dsn = "export.gpx",  layer= "track_points", stringsAsFactors = F)
  
  # Extract the coordinates as a matrix:
  gpx.tp <- coordinates(gpx.trackpoints)

  # #Loop through and add angle - the difference between the of the current point and the following one:
  for (tp in 1:(nrow(gpx.tp)-1)) {
    gpx.trackpoints$angle[tp] <- angle(gpx.tp[tp,], gpx.tp[tp+1,])
  }
  
  gpx.trackpoints@data$Date <- substr(as.character(gpx.trackpoints@data$time), 1,10)
  gpx.trackpoints@data$Hour <- substr(as.character(gpx.trackpoints@data$time),12,19)
  gpx.trackpoints@data$filename <- filename
  writeOGR(gpx.trackpoints, paste0(tools::file_path_sans_ext(filename), ".shp"), layer=tools::file_path_sans_ext(filename), driver="ESRI Shapefile")
}
</pre>
<a href="http://www.guru-gis.net/wp-content/uploads/2017/11/Screenshot-from-2017-11-20-14-53-09.png" rel="attachment wp-att-1048"><img src="http://www.guru-gis.net/wp-content/uploads/2017/11/Screenshot-from-2017-11-20-14-53-09.png" alt="Screenshot from 2017-11-20 14-53-09" width="941" height="639" class="alignnone size-full wp-image-1048" /></a>