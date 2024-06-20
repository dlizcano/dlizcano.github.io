---
title: "Recuperando fotos y datos de Flickr con R"
show_date: true
toc: true
category: 
  - Spanish
tags: 
  - Ecuador
  - Cameratrap
  - R
  - ciencia
header:
  teaser: /images/flickr/flickr_album.jpg
  overlay_image: "/images/texture-feature-22.jpg"
  caption: "[Cuniculus paca, Machalilla, Ecuador. Fauna de Manabi](https://faunamanabi.github.io/)"
comments: true
share: true
last_modified_at: 2016-05-22T01:24:36-0400
---

## Bajando un album completo y los datos exif de Flickr.com.


Luego de mas de un año en Manta, Ecuador, trabajando como investigador del [DCI-ULEAM](http://www.uleam.edu.ec/] en el proyecto [Fauna de Manabí](http://faunamanabi.github.io/). Hemos usado [Flickr.com](http://www.flickr.com) como un backup para almacenar las fotos del Proyecto. Flickr ofrece algunas ventajas como; una interfaz sencilla, un Terabyte de almacenamiento gratis, geotags y fácil organización de las fotos en albums y colecciones. Hasta el momento son cerca de 15 mil fotos las que hemos obtenido con el proyecto y muchas de ellas están en Flickr y en los computadores de la ULEAM. Sin embargo, el reciente [terremoto de Ecuador](https://en.wikipedia.org/wiki/2016_Ecuador_earthquake) nos hizo pensar que existía una gran posibilidad de perder los datos del proyecto, los cuales se encuentran almacenados en los discos duros de algunos computadores de la ULEAM.


Si bien se puede descargar un álbum completo (set, en el lenguaje de Flickr) con un simple click en la flecha de cada álbum.

<figure>
  <a href="/images/flickr/flickr_album.jpg"><img src="/images/flickr/flickr_album.jpg"></a>
</figure>

En algún momento pensé que eran muchos clicks, de muchos albumes y que sería chévere y divertido poder bajar los albums usando R. Adicionalmente me preguntaba si al bajar las fotos podría recuperar los datos exif de cada foto?.  Luego de algunas horas buscando como podría ser me encontré con  las fantásticas y simples instrucciones de web scraping elaboradas por [Rolf Fredheim](http://blog.rolffredheim.com/2014/03/web-scraping-scaling-up-digital-data.html).


Definitivamente lo mejor es trabajar con los API de  Flickr.com. Para lo cual se debe establecer una cuenta y obtener una key para usarlos, [acá pueden encontrar más detalles](http://www.flickr.com/services/api/misc.api_keys.html).


Al final, termine invirtiendo más horas en entender la estructura de los API de Flickr y desarrollando el código que baja los datos exif y los álbums, que si hubiera bajado cada álbum haciendo click en la flechita de cada uno. Aniway...

Espero esto le sirva a alguien!

### Acá el código para obtener los datos exif a partir del nombre de cada foto.

<script src="https://gist.github.com/dlizcano/650c3528a7ff1a00e9af639b3dfdfb38.js"></script>


### Y ahora el código para bajar el álbum completo (set) y obtener los datos de cada foto en una tabla.


<script src="https://gist.github.com/dlizcano/06fbf3af7a235f9c4cafb17d4223226e.js"></script>


<p>
<p></p>
</p>

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
