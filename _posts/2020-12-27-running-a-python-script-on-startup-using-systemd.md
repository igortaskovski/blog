---
title: Running a Python script on startup using Systemd
date: 2020-12-27 16:53:13 +0800
categories: [Linux]
tags: [linux, raspberry pi]
---

In my previous post I explained how I hacked a smoke detector to [send a signal to my Raspberry Pi](/posts/Raspberry-Pi-smoke-detector/) running [OctoPi](https://github.com/guysoft/OctoPi) and kill the 3D printer's power if something starts burning. I installed a relay on one of the input wires going into the power supply, so this way even if the power supply fails and starts smoking, we'll cut the power.

![3d-printer-power-supply](/assets/img/smoke-detector/3d-printer-power-supply.jpg)
The black wire marked in red goes through the relay
![relay](/assets/img/smoke-detector/relay.jpg)

I needed a [Systemd](https://en.wikipedia.org/wiki/Systemd) service to call my [Python script](/posts/raspberry-pi-smoke-detector-script/) on system startup, so here is how that looks like. 

First, I created a Systemd service in `/etc/systemd/system` with the name `smoke-detector.service` with the
### Create the smoke detector Systemd service
{% highlight yml %}
[Unit]
Description=Smoke Detector service
After=multi-user.target

[Service]
User=pi
ExecStart=python /usr/local/bin/smoke_detector.py

[Install]
WantedBy=multi-user.target
{% endhighlight %}

* `After`: This tells systemd when the script should be executed, basically the systemd unit specified here will be started before the current unit.
* `ExecStart`: This provides the path of the script we want to execute.
* `WantedBy`: This directive specifies how the systemd unit should be enabled. `multi-user.target` defines a system state where all network services are started up and the system will accept login.

### Next, we need to enable the service
First, we reload the systemctl daemon, then we enable the service. Basically, every time we make a change in the service, we need to reload the daemon.
{% highlight shell %}
sudo systemctl daemon-reload
sudo systemctl enable smoke-detector.service
{% endhighlight %}

### Starting the service
Here, we start the systemd service and check it's status. If everything was done right, the service should be active and running.
{% highlight shell %}
sudo systemctl start smoke-detector.service
sudo systemctl status smoke-detector.service
{% endhighlight %}

### Here is the service running
![smoke-detector-systemd-service](/assets/img/smoke-detector/smoke-detector-systemd-service.png)
