---
title: "Maps using R"
modified: 2021-08-15 10:10:36 -0500
category: English
tags:   
  - R
  - map
  - GIS
  - code
header:
  teaser: /images/mammal/map4.png
  overlay_image: "/images/texture-feature-20.jpg"
  caption: "[Cerro Yupati, La Pedrera, Amazonas, Colombia](https://www.flickr.com/photos/diegolizcano/14734613538)"
comments: true
share: true
show_date: true
toc: true
last_modified_at: 2021-08-15T01:24:36-0400
---

## R as mapping tool

R has added more tools to make beautiful maps and has evolved from a [simple GIS tool](https://github.com/dlizcano/R_as_GIS) to a very capable software to make complex spatial analysis and beautiful maps. New packages such as sf, mapview, and tmap are game changers, extending R capabilities to make those excellent maps.

This is one example from the papper [Elevation as an occupancy determinant of the little red brocket deer (Mazama rufina) in the Central Andes of Colombia. https://doi.org/10.15446/caldasia.v43n2.85449](https://doi.org/10.15446/caldasia.v43n2.85449)


![image](https://github.com/dlizcano/Mazama_rufina/raw/main/figs/README-unnamed-chunk-3-1.png)


## How to make it?

The background is a terrain raster image from Open Street Map of a bounding box window. Later we plot the country and states limits on top using the tmap package.

~~~ R
cams_loc_QR <- read.csv("data/cams_location.csv") # read data
cams_loc_QR_sf <- st_as_sf(cams_loc_QR, coords = c("Longitud", "Latitud"), crs = "+proj=longlat +datum=WGS84 +no_defs") # make data sf

bb <- c(-75.60, 4.59, -75.39,  4.81) # bounding box
andes_osm1 <- read_osm(bb, zoom = NULL, type="stamen-terrain", mergeTiles = TRUE) # type can be also bing and osm 
collimit <- gadm_sf_loadCountries("COL", level=0, basefile="./")
deptos <- gadm_subset(colombia, regions=c("Risaralda", "Quindío"))

depto_window <- qtm(andes_osm1)  +  # on osm add..
  tm_shape(cams_loc_QR_sf) + # add points
  tm_dots(col = "red", size = 0.2,  
          shape = 16, title = "Sampling point", legend.show = TRUE,
          legend.is.portrait = TRUE,
          legend.z = NA) + 
  tm_layout(scale = .9) + # legend
  tm_legend(position = c("left", "bottom"), frame = TRUE,
            bg.color="white") + 
  tm_layout(frame=F) + tm_scale_bar() + tm_compass(position = c(.75, .82), color.light = "grey90") # aditionals

dep_map <-  tm_shape(deptos$sf) + tm_polygons() + # state box
  tm_shape(data_box) + tm_symbols(shape = 0, col = "red", size = 0.25)
col_map <- tm_shape(collimit$sf) + tm_polygons() + tm_shape(deptos$sf) + tm_polygons() # country box

##### print all
depto_window
print(dep_map, vp = viewport(0.73, 0.40, width = 0.25, height = 0.25))
print(col_map, vp = viewport(0.73, 0.65, width = 0.25, height = 0.25))

~~~

## What is next...

The tmap package team has anounced a major update and several improvements for this package. [https://mtennekes.github.io/tmap4/index.html](https://mtennekes.github.io/tmap4/index.html)  

<p>lets see...  <br>
<br>
</p>

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.


