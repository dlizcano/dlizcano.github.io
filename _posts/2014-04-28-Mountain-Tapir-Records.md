---
title: "Mountain Tapir locations in rMaps"
comments: true
show_date: true
share: true
toc: true
category: 
  - Photo
tags: 
  - picture
  - DC
header:
  teaser: /images/cherry.jpg
  overlay_image: "/images/texture-feature-07.jpg"
  caption: "[Andes, Peru. Diego J. Lizcano](https://www.instagram.com/walking_tapir/)"
comments: true
share: true
last_modified_at: 2014-04-28T01:24:36-0400
---

## Mountain Tapir Distribution

Taking as excuse the new package [rMaps](https://github.com/ramnathv/rMaps). I wanted to plot my old points for the Mountain tapir. I hope this could serve as a starting point for a new collaborative project.

### Why to Update the Mountain Tapir Distribution?

- The information available as map in the red list has more than 10 years old.
  [Just take a look to the red list info for mountain tapir](http://maps.iucnredlist.org/map.html?id=21473).
- In the last 10 years researchers have studied mountain tapirs at new locations.
- New powerful tools to build robust distribution models have been developed.

### Those are some locations

<iframe width='100%' height='520' frameborder='0' src='/content/2.html' allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

Click on each point to see the type of evidence: hair, photo, footprint or faeces.

### The R code to make it

```r
require(rMaps)
fieldpoints_Diego<-read.csv("C:\\Users\\Diego\\Documents\\CodigoR\\Tapirus_SDM\\data\\T_pin.csv")

cord<-c(2.5,-73) #central location in the map
map3 <- Leaflet$new()
map3$setView(cord, zoom = 6)
map3$tileLayer(provider = 'MapQuestOpen.OSM')#Stamen.TonerLite
# put the info over the clicking points
for (i in 1:nrow(fieldpoints)){
  binpop<-paste("<p>",as.character(fieldpoints[i,5]),
  as.character(fieldpoints[i,6]), as.character(fieldpoints[i,9]),"</p>", sep=" " )  
map3$marker(c(fieldpoints[i,8], fieldpoints[i,7]), bindPopup = binpop)
  }
```




<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
