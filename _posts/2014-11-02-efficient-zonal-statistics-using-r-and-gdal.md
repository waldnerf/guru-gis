---
ID: 398
post_title: >
  Efficient Zonal Statistics using R and
  gdal
author: Guru-Blard
post_excerpt: ""
layout: post
permalink: >
  http://www.guru-gis.net/efficient-zonal-statistics-using-r-and-gdal/
published: true
post_date: 2014-11-02 15:37:22
---
Holà crazy GIS lovers,

This function allows to compute <a href="http://resources.arcgis.com/en/help/main/10.1/index.html#//009z000000w7000000">zonal statistics</a> of a raster using a 'zonal shapefile' and to add an attribute to this shapefile with the result of the statistics for each zone. 

UPDATE: The function has been implemented in the <a href="https://www.rdocumentation.org/packages/Rnightlights/versions/0.1.2/topics/ZonalPipe">Rnightlight</a> R package.

<pre lang="rsplus">
library(raster)

# source: https://stat.ethz.ch/pipermail/r-sig-geo/2013-February/017475.html
myZonal <- function (x, z, stat, digits = 0, na.rm = TRUE, 
                      ...) { 
  library(data.table)
  fun <- match.fun(stat) 
  vals <- getValues(x) 
  zones <- round(getValues(z), digits = digits) 
  rDT <- data.table(vals, z=zones) 
  setkey(rDT, z) 
  rDT[, lapply(.SD, fun, na.rm = TRUE), by=z] 
} 

ZonalPipe<- function (path.in.shp, path.in.r, path.out.r, path.out.shp, zone.attribute, stat){
  
  # 1/ Rasterize using GDAL
  
  #Initiate parameter
  r<-stack(path.in.r)
  
  ext<-extent(r)
  ext<-paste(ext[1], ext[3], ext[2], ext[4])
  
  res<-paste(res(r)[1], res(r)[2])
  
  #Gdal_rasterize
  command<-'gdal_rasterize'
  command<-paste(command, "--config GDAL_CACHEMAX 2000") #Speed-up with more cache (avice: max 1/3 of your total RAM)
  command<-paste(command, "-a", zone.attribute) #Identifies an attribute field on the features to be used for a burn in value. The value will be burned into all output bands.
  command<-paste(command, "-te", as.character(ext)) #(GDAL >= 1.8.0) set georeferenced extents. The values must be expressed in georeferenced units. If not specified, the extent of the output file will be the extent of the vector layers.
  command<-paste(command, "-tr", res) #(GDAL >= 1.8.0) set target resolution. The values must be expressed in georeferenced units. Both must be positive values.
  command<-paste(command, path.in.shp)
  command<-paste(command, path.out.r)
  
  system(command)
  
  # 2/ Zonal Stat using myZonal function
  zone<-raster(path.out.r)
  
  Zstat<-data.frame(myZonal(r, zone, stat))
  colnames(Zstat)[2:length(Zstat)]<-paste0("B", c(1:(length(Zstat)-1)), "_",stat)
  
  # 3/ Merge data in the shapefile and write it
  shp<-readOGR(path.in.shp, layer= sub("^([^.]*).*", "\\1", basename(path.in.shp)))
  
  shp@data <- data.frame(shp@data, Zstat[match(shp@data[,zone.attribute], Zstat[, "z"]),])
  
  writeOGR(shp, path.out.shp, layer= sub("^([^.]*).*", "\\1", basename(path.in.shp)), driver="ESRI Shapefile")
}

path.in.shp<-"/home/zone.shp"
path.in.r<-"/home/NDVI.tif" #or path.in.r<-list.files("/home/, pattern=".tif$")
path.out.r<-"/home/zone.tif"
path.out.shp<-"/home/zone_withZstat.shp"
zone.attribute<-"ID"

ZonalPipe(path.in.shp, path.in.r, path.out.r, path.out.shp, zone.attribute, stat="mean")

#With
#path.in.shp: Shapefile with zone (INPUT)
#path.in.r: Raster from which the stats have to be computed (INPUT)
#path.out.r: Path of path.in.shp converted in raster (intermediate OUTPUT)
#path.out.shp: Path of path.in.shp with stat value (OUTPUT)
#zone.attribute: Attribute name of path.in.shp corresponding to the zones (ID, Country...)
#stat: function to summary path.in.r values ("mean", "sum"...)

</pre>