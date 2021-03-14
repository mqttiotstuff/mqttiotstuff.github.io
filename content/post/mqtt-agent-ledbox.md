+++
author = "MQTT Iot Stuff Authors"
title = "Experiment around LedBoxes"
date = "2021-03-14"
description = "Bringing animations to ledboxed"
featured = true
tags = [
    "agents", "python"
]
categories = [
    "monitoring","python"
]
series = ["Themes Guide"]
aliases = ["mqtt-ledbox-agent"]
thumbnail = "images/cloud-symbol-inside-a-circle.png"
+++

Working part of my time at home, i needed to bring some non invasive informations about my systems, taking informations from calm technologies, i'd like to experiment bit a new device sitting on the desk and inform about system alerts i choose. 

Here is how the ledbox came in. I would like to experiment in practice the affordance of such device. This device is composed of a led banner rolled in a semi transparent box, and also containing a body sensor and a bunch of sensitive buttons at the top.

![](https://github.com/mqttiotstuff/MyMQTTNodeIOT/blob/master/hardware/SimpleDevice05/ledbox2.jpg)

This device is connected to MQTT brocker, and receive the leds status to display. A bit rought at the first glade, but working on the experience broad some interesting feedback using low display lights and smooth transitions.


<!--more-->

Giving the systems informations without sounds and low attention needs, came with some animations needed for the device to be able to inform what's going on.

It came first with an idea of coding a couple of meaning animations. After a couple of month the meaning associated to it needs to be richer that the initial envisionned goal. So an expression parser has been introduced to be able to easily compose some led animation's building blocks. This capability also leverage the capability to compose a new animation and design led pattern througth the mqtt message, in practice this capability offer a real disruptive communication capability of the device, integrating more and more meaning, with some conventions for the led language to be born. 

Even if this process is still on the craft table, things are more and more relevant regarding the communication capability of the device. Some new ideas are on the design and analysis phase, taking some words and translate them into a led animation language. Experience seems interesting.

In the current experiment, the led language must be learned, as in nature (ie: farmers decode the nature and extract some informations). As the observed system is growing, and more and more device states has to be integrated, and also the number of concerns, this explored way looks promising.

 

- __[mqtt-agent-ledbox](https://github.com/mqttiotstuff/mqtt-agent-ledbox)__

	This repo deals with the controlling software agent dealing with the device, in python and a bunch of generators  and expression parser to simplify the implementation of expressivness.

Still work on the go.


