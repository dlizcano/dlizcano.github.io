---
title: "Saving Soundlevel data from BirdNET-GO to a csv file"
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

Without the tireless efforts of these two incredible developers and all the many project contributors! my journey into bird and biodiversity monitoring as a citizen scientist simply wouldn't have been possible.

## Introducing BirdNET-Go: A High-Performance Acoustic Monitoring Tool

**BirdNET-Go** is a high-performance, continuous classification tool written in Go that acts as a real-time soundscape analyzer for avian vocalizations. Leveraging Artificial Intelligence, it's designed for efficiency and built on the powerful academic work of the BirdNET project, while drawing architectural inspiration from BirdNET-Pi, which I used extensively [as in this post](https://dlizcano.github.io/spanish/Monitoreando-aves-con-Birdnet/).

### Sleek Interface and Critical Integrations

BirdNET-Go provides a clean, modern web interface where users can easily view detected birds, analyze their frequencies, and play back captured recordings. It also includes a streamlined, resource-light (compared with BirdNET-Pi) analytics dashboard.

![BirdNET-GO Dashboard](/images/birdnetgo/BirdNET-GO_dashboard.png)  


However, the most interesting feature, especially for home users, is its seamless integration with **Home Assistant** via MQTT. It supports audio streams directly via **RTSP**, which is the standard protocol for streaming video and audio from common IP cameras. This allows users to turn existing security hardware into a powerful, real-time acoustic monitoring station.

### The Game-Changer: Soundscape Context

Beyond simple species identification, BirdNET-Go includes a powerful feature called **soundlevel**. This is crucial for understanding the *acoustic context* of the environment, which helps in filtering unwanted noise and provides invaluable sounscape data for researchers.

The system registers sound levels in [**1/3 octave bands**](https://www.engineeringtoolbox.com/octave-bands-frequency-limits-d_1602.html), providing a detailed frequency breakdown of the soundscape. This data is recorded as frequently as every ten seconds, offering continuous, advanced audio monitoring that gives a far richer picture of the overall soundscape than just a list of bird names.

![BirdNET-GO_Audio_setting](/images/birdnetgo/BirdNET-GO_Audio_setting.jpg)

Notice I selected to get data each 30 seconds.

### The Data Challenge: Optimizing for a Home Server

While this sound level data is incredibly valuable, accessing it required a solution tailored to my home lab server, which is an old Dell laptop running Home Assistant in Proxmox. By the way [here I got the inspiration to build it](https://www.youtube.com/watch?v=wX75Z-4MEoM). 

Sound level data from BirdNET-GO can be published via **MQTT** or accessed through a **Prometheus-compatible endpoint**, and it's typically consumed by databases such as [**InfluxDB**](https://www.influxdata.com/) for high-resolution time series, and for its visualization is common to use in tools like [Grafana](https://grafana.com/grafana/?plcmt=products-nav).

But dedicating that much processing power to a separate database stack and Grafana wasn't a viable option for my older home server. To keep the resource load minimal, I decided on a low-tech, high-impact approach:

I leveraged BirdNET-Go's tight integration with Home Assistant and created a simple **automation** to capture the necessary sound level attributes and write them directly to a local **CSV file**. This file now serves as my lightweight data repository, ready for deeper analysis and visualization using **R**.

### This is what you need:

#### 1. Setup:

Save this Python script to: `/config/scripts/save_audio_data.py`. 

This script reads the JSON data embeeded in the MQTT extracting the time, frequency, noise, mean and maximum data, and save it as a new row in the soundlevel_data.csv file.  Notice the file path at `/config/www/`.


```python

# extract data from JSON make it CSV
# made by claude.ai to Diego Lizcano
import json
import csv
import sys
from pathlib import Path
from datetime import datetime

def save_audio_data_to_csv(json_data):
    # Parse the JSON
    data = json.loads(json_data)
    
    # Define CSV file path
    csv_file = Path("/config/www/soundlevel_data.csv")
    
    # Check if file exists to determine if we need headers
    file_exists = csv_file.exists()
    
    # Open CSV file in append mode
    with open(csv_file, 'a', newline='') as f:
        # Prepare rows for each frequency band
        rows = []
        
        for band_name, band_data in data['b'].items():
            row = {
                'timestamp': data['ts'],
                'frequency': band_data['f'],
                'noise': band_data['n'],
                'max': band_data['x'],
                'mean': band_data['m']
            }
            rows.append(row)
        
        # Write to CSV
        if rows:
            fieldnames = ['timestamp', 'frequency', 'noise', 'max', 'mean']
            writer = csv.DictWriter(f, fieldnames=fieldnames)
            
            # Write header only if file is new
            if not file_exists:
                writer.writeheader()
            
            # Write all rows
            writer.writerows(rows)
    
    print(f"Saved {len(rows)} frequency bands to {csv_file}")

if __name__ == "__main__":
    if len(sys.argv) > 1:
        json_data = sys.argv[1]
        save_audio_data_to_csv(json_data)
    else:
        print("Error: No JSON data provided")
        sys.exit(1)

```

#### 2. Add this to `configuration.yaml` in Home Asistant

```yaml
shell_command:
  save_audio_data_csv: python3 /config/scripts/save_audio_data.py '{{ json_data }}'
```

#### 3. Add this automation in Home Assistant. 

For that go to: Settings → Automations → Create Automation → Edit in YAML, then paste the automation code.

```yaml
alias: BirdNET Audio Data to CSV
description: Capture audio frequency data from BirdNET-Go MQTT and save to CSV
trigger:
  - platform: mqtt
    topic: birdnet-go/soundlevel
mode: queued
max: 10
action:
  - service: shell_command.save_audio_data_csv
    data:
      json_data:"{{ trigger.payload }}"


```
Notice the `topic: birdnet-go/soundlevel` should be the same topic you configure in BirdNET-GO.  Also notice the last line includes inside the "" the word trigger.payload surrounded by double bracket **{{}}**. For some reason GitHub prevent to visualize it properly.

#### 4. Restart Home Assistant

> Voila!!! you have a csv file that grows by twenty rows each minute.
{: .notice--warning} 

### To download the csv data

I use [WinSCP](https://winscp.net/) to make the file trasnsfer to and from my server.

![BirdNET-GO_Audio_setting](/images/birdnetgo/window_WinSCP.PNG)

<p>
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
</p>
