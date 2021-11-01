+++
title = "IotMonitor project"
description = "IotMonitor description"
date = "2021-11-01"

+++
## IOTMonitor project

What's new on version 0.2.2:


	On the 0.2.2 version, several small enhacements :
		- Mqtt client specification on the config.toml
		- building from docker, either release and dev zig version



IotMonitor is an effortless and lightweight mqtt monitoring for devices (IOT things) and agents (software mqtt client) on Linux. 

IotMonitor aims to solve the "always up" problem of large IOT devices and agents system. This project is successfully used every day for running smart home automation system.

Disrupting existing heavy others solutions aiming to embrace all possible combinations of conneectivity. (and also locking users into a single solution),  Iotmonitor rely on it's side on aggregating and giving a "whole picture" for the system diversity implementation. It only rely on long term interfaces as OS process, MQTT. Microservice implementation strategy surf those days on this key aspect. When considering large and longlived running mqtt systems, solutions can hardly rely only on monolytics plateforms, leveling down the functionnality. Reality is that complex and focused systems always rely on composition of specialized implementation. As agents and functionnalities may increase with time,  diversity then occurs and system maintenance has to with checking and running several specialized agents programmed in different programming languages implementation. 

This project (iotmonitor) offers then a simple command line, as in *nix system philosophy, to monitor MQTT device and agents. MQTT based communication devices (IOT) and agents are monitored and watched, iotmonitor emits alerts if devices or agents are not responding any more. Iotmonitor also restart declared software agents when crashed. 

In the standard behaviour, once the mqtt topics are associated to a device or agent, IotMonitor records and restore the given MQTT "states topics" as device and agents go or recover. It really helps reinstalling IOT system states, and avoid lots of manual or administration tasks.

IotMonitor use a simple TOML config file. Each device has an independent configured communication message time out. When the device stop communication on this topic, the iotmonitor publish a specific monitoring failure topic for the lots device, with the latest reached timestamp. This topic is labelled :

	home/monitoring/expire/[device_name]

This topic can then be displayed, monitor as a whole or used as an input alert agent to inform the device or agent unavailability. 

IotMonitor command line, is based on the famous C Paho MQTT client library and use leveldb library as state database.


### Configuration File

The configuration is defined in the  `config.toml` TOML file, (see an example in the root directory)

A global Mqtt broker configuration section is defined using a heading `[mqtt]` 
the following parameters are found :

```toml
[mqtt]
serverAddress="tcp://localhost:1883"
baseTopic="home/monitoring"
user=""
password=""
```

An optional clientid can also be specified to change the mqtt clientid, a default "iotmonitor" is provided


#### Device declaration

In the configuration toml file, each device is declared in a section using a "device_" prefix
in the section : the following elements can be found :

```toml
[device_esp04]
watchTimeOut=60
helloTopic="home/esp04"
watchTopics="home/esp04/sensors/#"
stateTopics="home/esp04/actuators/#"
```

- `watchTimeOut` : watch dog for alive state, when the timeout is reached without and interactions on watchTopics, then iotmonitor trigger an expire message for the device
- `helloTopic` : the topic to observe to welcome the device. This topic trigger the state recovering for the device and agents. IotMonitor, resend the previous stored `stateTopics`
- `watchTopics` : the topic pattern to observe to know the device is alive
- `stateTopics` : list of topics for recording the states and reset them as they are welcomed

#### Agents declarations

Agents are declared using an "agent_" prefix in the section. Agents are devices with an associated command line (`exec` config key) that trigger the start of the software agent. IotMonitor checks periodically if the process is running, and relaunch it if needed.

```toml
[agent_ledboxdaemon]
exec="source ~/mqttagents/p3/bin/activate;cd ~/mqttagents/mqtt-agent-ledbox;python3 ledboxdaemon.py"
watchTopics="home/agents/ledbox/#"
```

IotMonitor running the processes identify the process using a specific bash command line containing an IOTMONITOR tag, which is recognized to detect if the process is running. Monitored processes are detached from the iotmonitor process, avoiding to relaunch the whole system in the case of restarting the `iotmonitor` process.

Agents may also have `helloTopic`, `stateTopics` and `watchTimeOut` as previously described.

#### State restoration for things and agents

At startup OR when the `helloTopic` is fired, iotmonitor fire the previousely recorded states on mqtt, this permit the device (things), to take it's previoulsy state, as if it has not been stopped.. All mqtt recorded states (`stateTopics`) are backuped by iotmonitor in a leveldb database.
For practical reasons, this permit to centralize the state, and restore them when an iot device has rebooted. If used this functionnality, reduce the need to implement a cold state storage for each agent or device.  Starting or stopping iotmonitor, redefine the state for all elements.

#### Monitoring iotmonitor :-)

IotMonitor publish a counter on the `home/monitoring/up` topic every seconds. One can then monitor the iotmonitor externally.
The counter is resetted at each startup.



# More information

[More information are available on the github repository](https://github.com/mqttiotstuff/iotmonitor/)

