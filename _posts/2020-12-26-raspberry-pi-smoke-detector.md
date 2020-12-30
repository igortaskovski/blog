---
title: Raspberry Pi smoke detector
date: 2020-12-27 12:23:12 +0800
categories: [Electronics]
tags: [electronics, raspberry pi, python]
---

I do a lot of 3D printing these days and one thing that's always on my mind is the [printer catching fire](https://www.reddit.com/r/3Dprinting/comments/8ah96r/anet_a8_burns_down_half_the_house/). There are a lot of things [you can do to make your printer safer](https://toms3d.org/2018/04/24/make-your-3d-printer-fireproof/) and I've addressed most of these issues with my printer, but I had a spare smoke detector and a Raspberry Pi with OctoPrint already set up, so I decided to play around with it.

The idea was to install the smoke detector on top of the printer so when smoke is detected, it sends a signal to the Raspberry which then controls the relay to cut the printer's power supply and it also sends me an SMS using [Twilio](https://www.twilio.com).

Here is how the detector looks on the inside:
![smoke-detector-open](/assets/img/smoke-detector/smoke-detector-open.jpg)

Found the ground and a point where the voltage hoes high when the alarm is activated
![smoke-detector-contacts](/assets/img/smoke-detector/smoke-detector-contacts.jpg)

Attached a relay to the 3D printer's power supply
![relay](/assets/img/smoke-detector/relay.jpg)

The code looks like this:

{% highlight python %}
import RPi.GPIO as GPIO
import time
from subprocess import call

GPIO.setwarnings(False)
relay = 21
smoke_detector = 23
GPIO.setmode(GPIO.BCM)
GPIO.setup(smoke_detector, GPIO.IN)
GPIO.setup(relay, GPIO.OUT)
GPIO.output(relay, False)

try:
  time.sleep(2)

  while True:
    if GPIO.input(smoke_detector):
        print("Smoke Detected...Turning off printer...")
        GPIO.output(relay, True)
        print("Sending you SMS...")
        call(["python3", "send_text.py"])
        time.sleep(10)
        break

except:
    GPIO.cleanup()
{% endhighlight %}

This is the script that sends the SMS

{% highlight python %}
import twilio
import twilio.rest
from twilio.rest import Client

account_sid = "xxxx" # Twilio SID
auth_token = "xxxx"  # Twilio Auth_Token

client = Client(account_sid, auth_token)

message = client.api.account.messages.create(
            to="xxx",    # Your phone number
            from_="xxx", # Twilio's phone number
            body="The smoke detector has been activated!")
{% endhighlight %}

In the next post I will explain [how to set up a Systemd service to automatically run the python script when the Raspberry Pi boots](/posts/running-a-python-script-on-startup-using-systemd/).