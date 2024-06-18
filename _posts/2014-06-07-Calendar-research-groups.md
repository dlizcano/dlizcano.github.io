---
layout: post
title: "Evolución de la ciencia en Colombia"
modified: 2014-06-07 11:10:36 -0400
category:
tags: [R, ciencia, Colombia]
image:
  feature: texture-feature-16.jpg
  credit: Cherry blossom, Washington, DC, USA. Diego J. Lizcano
  creditlink: http://500px.com/dlizcano
comments: true
share: true
---

## Calendario de la creación de grupos de investigación en Colombia.  

Continuando con el [post anterior,](http://dlizcano.github.io/2014/06/05/Science-in-Colombia.html) pensé que usando la fecha de creación de los grupos de investigación, podría revelar algo sobre la forma como ha cambiado la ciencia en Colombia.

<figure>
	<a href="/images/Colciencias/calendar_big.pdf"><img src="/images/Colciencias/calendar_small.png"></a>
</figure>

Aunque la figura resulta muy grande, pareciera haber un patrón interesante? La mayoría de los grupos se crean en enero, o en los primeros meses del año.

Definitivamente debo pensar en una forma diferente para ver esto..... continuara...

```r
P<- ggplot(grupos2, aes(y=1, x=month))
P + stat_bin(aes(fill=..count..), geom="tile", binwidth=1, position="identity") +
  scale_fill_gradient(low="blue", high="red") +
  facet_grid(Anio~Clasificacion) +
  theme(axis.text.y = element_blank()) + # elimina eje y
  theme(strip.text.y = element_text(colour = "black", angle = 0, size = 9)) +
  opts(panel.grid.major = theme_blank()) + # borra las lineas guía
  opts(panel.grid.minor = theme_blank()) + # borra las lineas guía
  theme(panel.background = element_rect(fill = "white"))+
  theme(axis.ticks.y = theme_blank()) + # tick en y
  theme(panel.background = element_rect(colour = "gray")) +
  ylab("")
```

Cualquier comentario sera muy apreciado y agradecido.

<p></p>

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
