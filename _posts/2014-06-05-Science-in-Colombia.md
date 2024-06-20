---
title: "Convocatoria Colciencias 640 del 2013"
category: Spanish
tags: 
  - R
  - ciencia
  - Colombia
  - code
header:
  teaser: /images/Colciencias/calendar_small.png
  overlay_image: "/images/texture-feature-15.jpg"
  caption: "[Cherry blossom, Washington, DC, USA. Diego J. Lizcano](http://500px.com/dlizcano)"
comments: true
show_date: true
share: true
toc: true
modified: 2014-06-05T10:10:36-0400
---

## Resultados convocatoria de medición de grupos

La [negativa de Colciencias](http://dlizcano.github.io/2014/05/28/New-system-to-evaluate-science-in-Colombia.html) para compartir los resultados de su convocatoria 640 del 2013, fue mi mejor excusa para aprender web scraping y extraer datos de la pagina [Colciencias-Scienti.](http://scienti.colciencias.gov.co:8083/ciencia-war/) Acá les dejo el [código en R y las tablas](https://github.com/dlizcano/Grupos_Colciencias) a los interesados.

Los resultados de este nuevo sistema de medición ha generado controversia. [Acá](http://www.eltiempo.com/archivo/documento/CMS-13826027) y [acá](http://www.rcnradio.com/noticias/investigadores-de-la-udea-criticaron-el-modelo-de-investigacion-de-colciencias-139109?utm_source=dlvr.it&utm_medium=twitter) algunos artículos al respecto. Aunque en mi opinión el método de medición tiene indices raros (como eso de la colaboración interna y no externa), la verdad, yo les confieso que me alegra ver como Colciencias por fin se pone al tanto de los estándares internacionales, dando mas puntos al factor de impacto de las publicaciones. Es que antes como era posible que de acuerdo a [Publindex](http://201.234.78.173:8084/publindex/) se le daba el mismo peso a publicar en Nature o en Caldasia. Con todo el respeto y admiración que siento por las revistas nacionales... Eso era perverso!.

Gabriela Delgado [@gabydel](https://twitter.com/gabydel) me aclara que el indice de colaboración no es tan "raro" como yo pensaba, y que si reconoce la cooperación con grupos externos.   

## Como quedo la clasificación de los grupos por área?

<figure>
	<img src="/images/Colciencias/grupos1.png">
</figure>

Es interesante que hay mas grupos A1 que A y que haya mas C que D. Se supone que hay una secuencia de D hasta A. Con A1 como lo máximo. Se les desvió la pirámide que buscaban?.

## El genero

Discriminando por el genero del director del grupo
<figure>
	<img src="/images/Colciencias/grupos_genero.png">
</figure>
La verdad yo esperaba una mayor diferencia. Aunque las mujeres directoras de grupo son menos, no están excluidas en ninguna categoría.

```r
library(ggplot2)
library(ggmap)
library (reshape2)
library (plyr)
library (dplyr)
library (googleVis)

# read table
grupos<-read.csv(file="Colciencias\\data\\fulltable3.csv",header=TRUE")
Areas<-colsplit(grupos$Área1, pattern = "\\ -- ", names = c('Area1', 'Area2'))
grupos<-cbind(grupos, Areas)

ggplot(grupos, aes(Clasificacion, fill=Area1)) + geom_bar() + coord_flip()
ggplot(grupos, aes(Clasificacion, fill=Area1)) + geom_bar() + facet_wrap(~ Genero)
```

## Donde se localizan los grupos?

Según la nueva clasificación

<figure>
	<a href="/images/Colciencias/mapa_grupos_big.png"><img src="/images/Colciencias/mapa_grupos.png"></a>
</figure>

Bogotá domina en todas las categorías. Hay varios departamentos sin grupos de investigación. Me pregunto como encajará esta falta de grupos de investigación en algunas regiones con los proyectos financiados con el fondo de regalías?

```r
### Takes 5 minutes and require Internet conexion
### make a query in google maps add details for some city
ciudades<-as.character(unique(grupos$Cuidad))
ciudades[ciudades == "Florencia"] <- "Florencia, Caqueta"
ciudades[ciudades == "Armenia"] <- "Armenia, Quindio"
ciudades[ciudades == "Pamplona"] <- "Pamplona, Norte de Santander"
ciudades[ciudades == "Madrid"] <- "Madrid, Cundinamarca"
ciudades[ciudades == "Socorro"] <- "Socorro, Colombia"
latlongit<-geocode(as.character(ciudades), output = "latlona")
geocodeQueryCheck() #see queries remaining
# back to original
ciudades[ciudades == "Florencia, Caqueta"] <- "Florencia"
ciudades[ciudades == "Armenia, Quindio"] <- "Armenia"
ciudades[ciudades == "Pamplona, Norte de Santander"] <- "Pamplona"
ciudades[ciudades == "Madrid, Cundinamarca"] <- "Madrid"
ciudades[ciudades == "Socorro, Colombia"] <- "Socorro"

# paste coord
ciudades<-cbind(ciudades,latlongit)
ciudades<-ciudades[,-4]
colnames(ciudades)<- c( "Cuidad",	"lon",	"lat")
grupos2<-left_join(grupos, ciudades)

#get polygon of Colombia
co<-getData("GADM",country="CO",level=1,download=TRUE)
col_depto <- fortify(co,region="NAME_1") # make compatible to ggplot2

mapbase<- ggplot(col_depto, aes(long,lat,group=group)) + geom_polygon(fill="grey60") + coord_equal() +
  geom_path(color="grey")

# grupos por ciudad
map3<-mapbase + geom_point(aes(x = lon, y = lat, group = FALSE), size=1,
                             data = grupos2,alpha=I(0.25),colour="red") +
  stat_binhex(aes(x = lon, y = lat, group = FALSE),
              size = 1, binwidth = c(1,1), alpha = 1/3,data = grupos2) +
  scale_fill_continuous(low = "green", high = "red") + facet_wrap(~ Clasificacion) + theme_bw()
```


## Que impacto han tenido los grupos?

Generalmente muchos de los integrantes de los grupos son estudiantes . Así que una forma de ver el impacto de los grupos en la formación de investigadores, es contar a sus integrantes, los cuales no discrimine como activos o inactivos.  


<iframe width='100%' height='520' frameborder='0' src='https://dlizcano.github.io/content/map_chart.html' allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

Vea el resultado [aqui](https://dlizcano.github.io/content/map_chart.html)

```r
#  investigadores per Depto
s1<-summarise(group_by(grupos, Departamento),sum(integrantes))
colnames(s1)<-c("Departamento", "investigadores")
# investigadores per ciudad
s2<-summarise(group_by(grupos, Cuidad),sum(integrantes))
invest<-left_join(s2, ciudades)
invest$loc=paste(invest$lat, invest$lon, sep=":")

# map total integrantes per Ciudad
G2 <- gvisGeoChart(invest, "loc", "sum(integrantes)"  ,
                  options=list(displayMode="Markers", region="CO", resolution="provinces",
                               colorAxis="{colors:['blue','purple', 'red']}",
                               backgroundColor="lightblue", width=500, height=400), chartid="Integrantes_Grupo")

# Barchart integrantes per Ciudad
B <- gvisBarChart(s1[,1:2], yvar="investigadores", xvar="Departamento",                  
                  options=list(width=400, height=600, legend='none'))

GB <- gvisMerge(G2,B,horizontal=TRUE)

plot(GB)
```

Aun estoy lejos de los mapas interactivos que hace [RangoFinito.](http://finiterank.github.io/censo/empleadas.html)  Pero poco a poco!.

Cualquier comentario sera muy apreciado y agradecido.

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
