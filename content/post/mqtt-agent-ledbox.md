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

Working part of my time at home, i needed to be informed about all my systems in a non invasive way,  following the calm technology trend to stay focus on my primary working task. With this orientation,  I've then conduct some experiments building a new device standing on the desk and bring system informations and alerts i choose.

Here is how the ledbox came in. I've just wished to experiment in practice the affordance of such device and its communication ability and attention retention. This device is composed of a led banner rolled in a semi transparent box, and also containing a body sensor and 4 touch buttons on the top.

![](https://raw.githubusercontent.com/mqttiotstuff/MyMQTTNodeIOT/master/hardware/SimpleDevice05/ledbox2.jpg)


This device is connected to MQTT brocker, and receive the leds status to display. A bit rought at the first glace, but the work on the user's experience bring interesting points on using low light displays and smooth transitions.

<!--more-->

Giving systems informations without sounds and with a low attention needs is a hard stuff. Here came the idea to make some animations needed for the device to be able to inform what’s going on.



It cames first with an idea of coding a couple of meaning animations, teaching the systems failures and the several alerts i may need.
After a couple of month the meaning associated to alarms, needs to be richer that the initial envisionned goal. In practive, and add more sens easily, an expression parser has been developed to be able to easily compose some led animation’s blocks. 


This capability has leverage the ability add more informations capabilities to the device. Taking the definition from a mqtt message, in practice this capability offer a real disruptive communication ability , integrating more and more meaning externally.  Some conventions are explored to make birth of a new "led language".

Even if this process is still on the craft table, things are tunring more and more relevant on the communication ability of the device. Some other new ideas are also on the design stage, creating a small language. The experience seems promising.

In this experiment, the "led language" must be learned.  As in nature when farmers decode the nature to get additional informations on situation or weather. 
As the observed system is growing, and more and more device's states has to be integrated, as the number of concerns also. It seems this approach powerfull.

Below the source code repository and associated animation gallery. 

- __[mqtt-agent-ledbox](https://github.com/mqttiotstuff/mqtt-agent-ledbox)__

	This repo deals with the controlling software agent dealing with the device, in python and a bunch of generators  and expression parser to simplify the implementation of expressivness.

Still work on the go.


