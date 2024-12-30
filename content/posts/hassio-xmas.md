+++
date = '2024-12-30T11:28:58+01:00'
draft = false
title = 'All I want for Christmas is Zigbee'
tags = ["home assistant", "home automation", "raspberry pi", "zigbee"]
description = "How I set up motion-activated christmas tree lights in my personal home automation system."
+++

The end of the year 2024 has arrived, and like many I took time to unite with family and exchange gifts. In between the festivities I took a couple of days off to recover from  the food and social events. Finally, being able to finish up some long due home tasks provides me with a good mood to start the new year clean (including cleaning up, organizing and archiving files and folders on PC and NAS).

Every Christmas, we get the nicest tree we can find and decorate it with trinkets we collected from our vacations together and add some nice lights. Every day, we'd turn on the lights before breakfast by plugging them in. Of course, instead of having to turn on the lights every time yourself, why not tinker for a couple of hours to turn the lights on automatically whenever you're at home?

{{< video src="/videos/hassio-xmas.mp4" height="300" autoplay="true" loop="true" muted="true" description="Turning on the tree with the TRADFRI remote connected with CC2531 wireless transceiver." >}}

### What did I need?

Here’s a list of things I bought off the shelf:

- Ikea TRADFRI Outlet and switch
- Ikea TRADFRI Motion sensor
- Raspberry Pi 3 Model B (also referred to as RPI from here)
    - I bought this in 2019 and it has been reliably running home assistant for over 5 years. 
- Kingston A400 SSD 240GB
    - The MicroSD card I originally got for the RPI started to corrupt after 4 years of continuous use (they have a limited number of read/write cycles). So replaced it with an SSD, which is better equipped to deal with the always on system.
- Zigbee CC2531 Wireless Transceiver
    - Low-power 2.4-GHz IEEE 802.15.4 USB dongle from Texas Instruments.
    - This radio device provides the communication between the RPI and other Zigbee-compatible devices.
    - At this time, there are better options available with better range, that support more devices. 
- Texas Instruments CC Debugger for RF system-on-Chips + Downloader cable
    - Used to flash the original CC2531 dongle for use as a Zigbee coordinator, to integrate with Zigbee2MQTT or Home Assistant to control other Zigbee devices.

Most home automation devices only work over your network if you also purchase the manufacturers hub device. By setting up the CC2531 as your own Zigbee controller, it can be used across a wide range of home automation devices that support Zigbee.

> Home automation manufacturers often require you to purchase their proprietary "hub" device, even though most are using the same protocols. Customers have to either limit their set up to devices from a single manufacturer, or purchase a bunch of different hub devices.

### Set up the Home Assistant hardware
Firstly, set up the Raspberry Pi with Home Assistant (HA).

> Home Assistant is free and open-source software to configure your own home automation system, including dashboards and configuration of your home automation devices. It supports deployment using Docker, or integrated in the Raspberry Pi OS. Since a few years, they also sell a preconfigured hardware device to run Home Assistant. So if you don't feel like setting up your own Raspberry Pi, you can get yourself one of those. https://www.home-assistant.io/green

Home Assistant can be installed on a Raspberry Pi using the [Raspberry Pi Imager software](https://www.home-assistant.io/installation/raspberrypi). I installed the software on an SSD instead of the SD card because it supports more read/write cycles and therefore will last much longer.

### Configure the CC2531
> At the time of writing, there are already more powerful options available than the CC2531. I set this up for the first time in 2019, so a lot has changed since then.

Flash the CC2531 transceiver to be used as a Zigbee controller. Find the instructions in [this Github repo](https://github.com/PradeepaRW/Zigbee-CC2531-USB-Dongle-).

Next, connect the device to your Raspberry Pi using USB and set it up through HA. Go to `Settings > Devices > Integrations` and add the integration `Zigbee Home Automation`. A Zigbee Coordinator device should become available in the device overview. For more instructions, visit the [Zigbee2MQTT website](https://www.zigbee2mqtt.io/guide/adapters/zstack.html#usb-1).

### Set up the automation
Here's the automation for a motion sensor based tree. The lights are turned on if your presence is detected, only between 7AM and 22.30PM. After sensing motion, the lights stay on for 3 hours.

```yaml
alias: Motion Xmas tree
description: ""
trigger:
  - type: motion
    platform: device
    device_id: 173fcdb5344f2003f9408d58c405a56e
    entity_id: binary_sensor.ikea_of_sweden_tradfri_motion_sensor_motion
    domain: binary_sensor
condition:
  - condition: time
    after: "07:00:00"
    before: "22:30:00"
action:
  - type: turn_on
    device_id: 785c0eec131ba0fcc4d0bf001765b3bc
    entity_id: switch.ikea_of_sweden_tradfri_control_outlet_switch
    domain: switch
  - delay:
      hours: 3
      minutes: 0
      seconds: 0
      milliseconds: 0
  - type: turn_off
    device_id: 785c0eec131ba0fcc4d0bf001765b3bc
    entity_id: switch.ikea_of_sweden_tradfri_control_outlet_switch
    domain: switch
mode: single
```

**Happy holidays!**