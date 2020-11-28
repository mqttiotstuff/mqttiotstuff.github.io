+++
author = "MQTT Iot Stuff Authors"
title = "Freeing some Mqtt agents python implementation"
date = "2020-11-28"
description = "Freeing some Mqtt agents python implementation"
featured = true
tags = [
    "agents", "python"
]
categories = [
    "monitoring","python"
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
thumbnail = "images/cloud-symbol-inside-a-circle.png"
+++

Mqtt iot system bring value in every day's life. This is because of the existing agents that help system live. 
Some agents have been freed in repository, for preview. As my own system grow and used,  there are still semantic maturing, and testing. 
These implementation are in python 3, to ease the read and let users understand and extend.

<!--more-->

## Repository List

- __[mqtt-agent-screen](https://github.com/mqttiotstuff/mqtt-agent-screen)__

	This repo deals with a simple square led screen using WS8212 controllers, and display some text, or fancy toon characters.

- __[mqtt-agent-suntime](https://github.com/mqttiotstuff/mqtt-agent-suntime)__

	This repo use the famous suntime library to know the local sunrise and sunset, depending on the GPS coordinates. Knowing the sunrise, agents and things can adjust their behaviour.

- __[mqtt-agent-wifi](https://github.com/mqttiotstuff/mqtt-agent-wifi)__

	This repo connect to the freebox, and grab informations about the connected devices and mobiles to the wifi Access Point. Rss is retrieved, to permit to infer the location. One use of this agent is to detect the presence of the mobile of some one to trigger message or other elements.

- __[mqtt-agent-tregex](https://github.com/mqttiotstuff/mqtt-agent-tregex)__

	This repo implements some event detection, to trigger and generate more actions.
