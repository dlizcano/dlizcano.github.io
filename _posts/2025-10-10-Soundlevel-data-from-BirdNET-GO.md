---
title: "Saving Sound Level Data from BirdNET-GO to a CSV file"
show_date: true
toc: true
category: 
  - English
tags: 
  - Phython
  - Home Assistant
  - Birds
  - aves
  - acustica
  - BirdNET-Pi 
header:
  teaser: "/images/birdnetgo/BirdNET-Go-logo.png"
  overlay_image: /images/texture-feature-6-2025.jpg
  caption: "Photo: [Aviario Nacional, Baru, Colombia. Diego J. Lizcano](https://www.instagram.com/walking_tapir/)"
comments: true
share: true
last_modified_at: 2025-10-10T01:24:36-0400
---


Recently I have adquired a Raspberry pi 4 and decided to upgrade my [BirdNet-Pi station Entrelomas](https://app.birdweather.com/data/K9kBtRztpJHkdiXSnfWyi3nr) to  BirdNET-Go [in the same location](https://app.birdweather.com/stations/18147). BirdNET-GO is a newer BirdNET-based project. 

# Real-Time Soundscape Analysis: Exploring BirdNET-Go and Custom Data Logging

I want to start with a massive shout-out to the developers who made this all possible. My deepest thanks go to [**Tomi P. Hakala**](https://github.com/tphakala), who built [BirdNET-Go](https://github.com/tphakala/birdnet-go) on top of the core [BirdNET project](https://birdnet.cornell.edu/), and to [**mcguirepr89**](https://github.com/mcguirepr89) for his foundational work on the original [BirdNET-Pi](https://github.com/Nachtzuster/BirdNET-Pi) project, now directed by [**Nachtzuster**](https://github.com/Nachtzuster) on which I've relied on extensively.

Without the tireless efforts of these two incredible developers and all the many project contributors! my journey into birding and biodiversity monitoring as a citizen scientist simply wouldn't have been possible.

## Introducing BirdNET-Go: A High-Performance Acoustic Monitoring Tool

**BirdNET-Go** is a high-performance, continuous classification tool written in Go that acts as a real-time soundscape analyzer for avian vocalizations. Leveraging Artificial Intelligence, it's designed for efficiency and built on the powerful academic work of the BirdNET project, while drawing architectural inspiration from BirdNET-Pi, which I used extensively [as in this post](https://dlizcano.github.io/spanish/Monitoreando-aves-con-Birdnet/).

### Sleek Interface and Critical Integrations

BirdNET-Go provides a clean, modern web interface where users can easily view detected birds, analyze their frequencies, and play back captured recordings. 

![BirdNET-GO Dashboard](/images/birdnetgo/BirdNET-GO_dashboard.png)  

It also includes a streamlined, resource-light analytics dashboard, compared with the one in BirdNET-Pi that takes much longer to load. I also love the posibility to export the data as a csv file.

![BirdNET-GO Dashboard](/images/birdnetgo/BirdNET-GO_Analytics.jpg)  

However, the most interesting feature, especially for home users, is its seamless integration with **Home Assistant** via MQTT. It supports audio streams directly via **RTSP**, which is the standard protocol for streaming video and audio from common IP cameras. This allows users to turn existing security hardware into a powerful, real-time acoustic monitoring station.

### The Game-Changer: Soundscape Context

Beyond simple species identification, BirdNET-Go includes a powerful feature called **Sound Level Monitoring**. This is crucial for understanding the *acoustic context* of the environment, which helps in filtering unwanted noise and provides invaluable sounscape data for researchers.

The system registers sound levels in [**1/3 octave bands**](https://www.engineeringtoolbox.com/octave-bands-frequency-limits-d_1602.html), providing a detailed frequency breakdown of the soundscape. This data is recorded as frequently as every ten seconds, offering continuous, advanced audio monitoring that gives a far richer picture of the overall soundscape than just a list of bird names.

![BirdNET-GO_Audio_setting](/images/birdnetgo/BirdNET-GO_Audio_setting.jpg)

Notice I selected to get data each 30 seconds.

### The Data Challenge: Optimizing for a Home Server

While this **Sound Level** data is incredibly valuable, accessing it required a solution tailored to my home lab server, which is an old Dell laptop running Home Assistant in a virtual machine of Proxmox. By the way [here I got the inspiration to build it](https://www.youtube.com/watch?v=wX75Z-4MEoM). 

Sound level data from BirdNET-GO can be published via **MQTT** or accessed through a **Prometheus-compatible endpoint**, and it's typically consumed by databases such as [**InfluxDB**](https://www.influxdata.com/) for high-resolution time series, and for its visualization is common to use tools like [Grafana](https://grafana.com/grafana/?plcmt=products-nav).

But dedicating that much processing power and space to a separate database in InfluxDB and for Grafana to visualize wasn't a viable option for my old laptop as home server. To keep the resource load minimal, I decided on a low-tech, simple and diferent approach:

I leveraged BirdNET-Go's tight integration with Home Assistant and created a simple **automation** to capture the necessary sound level attributes and write them directly to a local **CSV file**. This file now serves as my lightweight data repository, ready for deeper analysis and visualization using **R**. 

The inspiration to integrate Home Assistant with BirdNET-GO came from [Kyle Niewiada](https://www.kyleniewiada.org/blog/2025/05/backyard-bird-tracking-with-ai/). 

### This is what you need:

#### 1. Setup:

Save this Python script to: `/config/scripts/save_audio_data.py`. 

This script reads the JSON data embeeded in the MQTT, extracting the time, frequency, noise, mean and maximum data, and save it as a new row in the soundlevel_data.csv file.  Notice the file path at `/config/www/`.

<script src="https://gist.github.com/dlizcano/be069b6feea3f742a6a2f0a37a51d05c.js"></script>

#### 2. Add this to `configuration.yaml` in Home Assistant

<script src="https://gist.github.com/dlizcano/06ef4578975b240503c83fa41239bef8.js"></script>

#### 3. Add this automation in Home Assistant. 

For that go to: Settings → Automations → Create Automation → Edit in YAML, then paste the automation code.

<script src="https://gist.github.com/dlizcano/8bd69131664d7cce10b7956c6703468f.js"></script>

Notice the `topic: birdnet-go/soundlevel` should be the same topic you configure in BirdNET-GO.  Also notice the last line includes inside the "" the word `trigger.payload` surrounded by double bracket **{}**. For some reason GitHub prevent to visualize it properly.

![trigger.payload](/images/birdnetgo/triger.jpg)

#### 4. Restart Home Assistant

> Voila!!! you have a csv file that grows by twenty rows each minute storing sound levels.
{: .notice--warning} 

### To download the csv data

I use [WinSCP](https://winscp.net/) to make the file trasnsfer from and to my home server.

![BirdNET-GO_Audio_setting](/images/birdnetgo/window_WinSCP.PNG)

Of course BirdNET-GO is still a work on progress and its down side is that if you use an older, or diferent browser to Chrome, the visualization can freeze. 

<p>
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
</p>
