---
title: "Google knows"
comments: true
show_date: true
share: true
toc: true
category:
tags: 
  - R
  - map
  - ggmap
category:
  - post
  - blog
  - github
header:
  teaser: "/images/texture-feature-02.jpg"
  overlay_image: texture-feature-02.jpg
  caption: "Photo: [Andes Mountains, Peru. Diego J. Lizcano](http://500px.com/dlizcano)"
last_modified_at: 2014-04-20T01:24:36-0400
---

## Just a small part of Google data-harvesting practices.

Following the post by [http://seasci.wordpress.com/2013/12/20/it-knows-where-i-live](http://seasci.wordpress.com/2013/12/20/it-knows-where-i-live/). I was amaze to know how much Google knows about me.

#### Get your info

If you use Google maps in your smart phone and you store your locations. This R code is for you. Just download the locations from [Google Takeout](https://accounts.google.com/ServiceLogin) and follow the code.

#### Once you have the location file

Save it in your hard drive \\data\\location\\ in my case.

```r
library(jsonlite)
library(plyr)
library(lubridate)
# Load in the raw data from the JSON file
raw = fromJSON ('data\\location\\HistorialdeUbicaciones.json')
# Get the 'locations' part of the list
locs = raw$locations
# these are all the columns that it contains...
names(locs)
# they're in various formats...
lapply(locs,class)
```

#### Do some simple processing

```r
# Get columns into useful formats
ldf = data.frame(t=rep(0,nrow(locs)))
# time is in POSIX * 1000 (milliseconds) format, convert it to useful scale...
ldf$t = as.numeric(locs$timestampMs)/1000
class(ldf$t) = 'POSIXct'
# lat/lon are xE7, convert them to usable numbers...
ldf$lat = as.numeric(locs$latitudeE7/1E7)
ldf$lon = as.numeric(locs$longitudeE7/1E7)
# Get the map of your location
require(ggplot2)
require(ggmap)
DC = get_map(c(-77.05,38.93),11,source='google', color = "bw")
```

#### Making the map
```r
ggmap(DC) %+% ldf + aes(x = lon, y = lat) +
  stat_binhex(data = ldf, aes(x = lon, y = lat),
                 size = 5, binwidth = c(.01,.01), alpha = 2/4) +
  scale_fill_gradient(low = "green", high = "red")  +
scale_alpha(range = c(0.00, 0.25), guide = FALSE) +
  theme(legend.position = "none", axis.title = element_blank(), text = element_text(size = 12)) +
  geom_point(data=ldf,aes(x=lon,y=lat), colour="blue", alpha = I(0.05))
```

## The result
- Were I have been
<figure>
	<a href="/images/map1.png"><img src="/images/map1.png"></a>
</figure>

## It is clear:
- I spend much of my time in Silver Spring
- I use the GPS of my phone sporadically, usually riding the red and green lines in the metro.

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
