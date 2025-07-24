---
title: "Monitoreando aves con BirdNET-Pi"
comments: true
show_date: true
share: true
toc: true
category: Spanish
header:
  teaser: "/images/birdnetpi/BirdNetPi_setup.jpg"
  overlay_image: /images/texture-feature-6-2025.jpg
  caption: "Photo: [Aviario Nacional, Baru, Colombia. Diego J. Lizcano](https://www.instagram.com/walking_tapir/)"
tags:
  - acustica
  - BirdNET-Pi 
  - aves
  - monitoreo
last_modified_at: 2025-06-15T13:05:25-05:00
feature_row:
  - image_path: https://dlizcano.github.io/images/birdnetpi/TurdusActivity.png

---

## Monitoreo acustico de aves 

Al final de la pandemia en 2023, y como parte de mi entusiasmo como parte del proceso de aprendizaje en métodos de monitoreo acústico que surgió con el proyecto [Monitoreo de Biodiversidad en destinos de turismo de naturaleza](https://monitoreo-acustico.netlify.app/es/) de [Awake](https://www.awake.travel/), me embarque en el proyecto de construir mi propio sistema de monitoreo acústico de aves con un BirdNET-Pi, siguiendo el tutorial de Core Electronics.

{% include video id="IM-F4sJ-5rc" provider="youtube" %}  

## El aparato 

Aprovechando una de las promociones de sigma electronica, adquiri un [Raspberry Pi 3 Modelo A+](https://www.sigmaelectronica.net/producto/rpi3-a/) a muy buen precio, junto con un [microfono USB](https://www.sigmaelectronica.net/producto/adafr-3367/). 

Siguiendo el tutorial, el montaje y la instalación del BirdNET-Pi fue muy sencilla! 

![image](https://dlizcano.github.io/images/birdnetpi/BirdNetPi_setup.jpg)

En un comienzo lo instale en la ventana de mi apartamento, pero muy pronto note que solo estaba registrando a la gente, los perros y los carros, ya que la ventana daba a la parte interna y el parqueadero de los edificios. Así que decidí moverlo a la ventana del apartamento de mis suegros, que da hacia la parte exterior de los mismos edificios en Cajica, donde hay algunos potreros con vacas y cercas de pinos. Con esta nueva localización el dispositivo quedo registrado como la [estación 754 en birdweather](https://t.co/vKG0YMxY6K). Luego de un poco más de un año funcionando continuamente decidí recoger el aparato para reutilizar el Raspberry pi en otro proyecto. 

Junto con la instalación del BirdNET-Pi me di a la tarea de crear una cuenta en twitter llamada [BirdNET Pi Cajica](https://x.com/BirdNetPi_Cajic), para registrar las detecciones de forma automática, pero desafortunadamente, con la compra de twitter y su conversión a X, las políticas de las API para crear cuentas automatizadas cambiaron y la cuenta quedo desactivada luego de unos pocos meses.  

Usando las [API de birdweather](https://app.birdweather.com/api/) he recuperado los registros con un codigo muy sencillo. Aca se muestran los registros de las 15 especies mas comunes con su nombre en español.


```r
### R API to birdweather
library(httr)
library(jsonlite)
library(tidyverse)
library(kableExtra)

# 1st request. Ask who is
res = GET("https://app.birdweather.com/api/v1/stations/{API-KEYword-del-dispositivo}/species?period=all&locale=es")
res # to see 200 is success 

# from Jsaon convert to list
data = fromJSON(rawToChar(res$content))
names(data)

View(data$species)
names (data$species)

# make table with thumb
tbl_img <- data.frame(
  Common_name =c(data$species$commonName[1:15]),
  Scientific_name = c(data$species$scientificName[1:15]),
  Image = ""
)

tbl_img  %>% kbl(booktabs = T) %>%
  kable_paper(full_width = F) %>%
  column_spec(1, color = "white",bold=T,
              background = c(data$species$color[1:15])) %>% 
  column_spec(2, #color = "white",
              italic =T,
              link=data$species$imageUrl[1:15]) %>% 
  column_spec(3, image = spec_image(
    c(data$species$thumbnailUrl[1:15]), 100, 100)) 


```


<iframe
  id="inlineFrameExample"
  title="Inline Frame Example"
  width="500"
  height="400"
  src="/content/BirdNetPi_Cajic.html">
</iframe>

### Extrayendo mas detalles de una especie

Para obtener más datos de una especie se puede usar el [Get detections](https://app.birdweather.com/api/v1/index.html#detections-detection-get) que acoplado a los paquetes de R httr y jsonlite, hacen que el manejo de la API sea muy fácil. 

Para este ejemplo extraeremos las detecciones del mes de Marzo del 2023 y usando la magia del paquete camtrapR vamos a obtener una gráfica del patrón de actividad de ese mes. 


```r
library(httr)
library(jsonlite)
library(tidyverse)
library(camtrapR)

#  get turdus detections and select a few columns
turdus_mar <- GET("https://app.birdweather.com/api/v1/stations/{API-KEYword-del-dispositivo}/detections?from=2023-03-01&to=2023-03-31&speciesId=422&locale=es")
turdus_mar_data = fromJSON(rawToChar(turdus_mar$content))
turdus_mar_detections <- as.data.frame(turdus_mar_data$detections)|> 
  dplyr::select(c(id, timestamp, confidence, probability, score, certainty)) |> 
   mutate(Species="Turdus fuscater")

# fix datetime stamp
turdus_mar_detections$DateTime <- str_replace(turdus_mar_detections$timestamp, "T", " ") |> 
  str_sub(start=1,end=19) |> strptime ("%Y-%m-%d %H:%M:%S")

########### Activity Plot
activityDensity (recordTable = turdus_mar_detections,
                 species     = "Turdus fuscater",
                 recordDateTimeCol="DateTime") 
rect(0, 0, 6, 0.9, col= rgb(0.211,0.211,0.211, alpha=0.2), border = "transparent")
rect(18, 0, 24, 0.9, col= rgb(0.211,0.211,0.211, alpha=0.2), border = "transparent")

```

![Patrón de Actividad de Turdus fuscater](https://dlizcano.github.io/images/birdnetpi/TurdusActivity.png)

Es una lástima que las detecciones tengan un límite de 100 registros. Tal vez para hacerlo más detallado habría que bajar los datos diariamente. 

El entusiasmo con el monitoreo acústico no ha parado. Recientemente instale un [dispositivo igualito en la casa de mis padres en Cúcuta](https://app.birdweather.com/stations/2595). Pero esta vez usando el nuevo [código de BirdNet implementado en GO](https://github.com/tphakala/birdnet-go). Si bien tiene menos "features" y no viene con apraise, me parece que funciona bastante bien, siendo ligero y eficiente. 

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
