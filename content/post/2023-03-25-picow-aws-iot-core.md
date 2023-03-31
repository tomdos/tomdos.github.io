---
author: "Tomas Dosoudil"
title: "Connecting RPi Pico W to AWS IoT Core"
date: "2023-03-25"
description: "Connecting RPi Pico W to AWS IoT Core"
tags: [
    "rpi",
    "aws",
    "pico"
]
categories: [
    "Raspberry Pi"
]
---

In this post I will show you how to connect your Pico W to AWS IoT Core and send 
temperature to the cloud.

## Creating a new AWS IoT Core Thing

I am assuming you already have an AWS account and we will focus on setting up 
IoT Core only.

### Create a thing
You need to navigate to AWS IoT Core -> All devices -> Things and click on `Create things` button.
Create a thing in AWS and call it `pico_w_simple` with a unnamed shadow (classic)

![Create a thing](/post_images/2023-03-25/thing-create.png)

### Generate a new certificate
In order to communicate with AWS the device needs to have x509 certificate. This allows a device 
to connect to AWS IoT Core (mqtt broker). 

![Create a new certificate](/post_images/2023-03-25/thing-new-cert.png)

### Policy
We will create a basic aws policy that will be later on attached to the certificate. The policy
defines more granular actions a device can perform. We create a basic policy that allows all actions.

![Create a new policy](/post_images/2023-03-25/new-policy.png)

### Attach the policy
Attach previously created policy to the certificate.

![Attach policy](/post_images/2023-03-25/attach-policy.png)

### Download all certificates and keys
This is the last step where we need to download at least private key and certificate. Once you close this window you won't be able to download it anymore.

![Download all keys and certificates](/post_images/2023-03-25/download-keys.png)

### Keys conversion 
Both private key and certificate are in a PEM format. We will need to convert them to DER format as this format is later on used in our code. We can use the following snippet.

```bash
#!/bin/bash

# Converts a certificate and private key downloaded from AWS 
# in PEM format into DER format.

openssl x509 -in certificate.pem.crt -out certificate.der -outform DER
openssl pkey -in private.pem.key -out private.der -outform DER

```


## Pico W code

In order to connect your Pico to AWS IoT Core, you will need to use an mqtt client, 
previously converted key with certificate and main code.

As mqtt client you may use [umqtt.simple](https://github.com/micropython/micropython-lib/blob/master/micropython/umqtt.simple/umqtt/simple.py) from micropython-lib.

Files on your should look like this:

```bash
.
├── keys
│   ├── certificate.der
│   └── private.der
├── main.py
└── umqtt
    └── simple.py
```

Where the `main.py` file contains the following code:

```python
import network
from machine import Pin
from umqtt.simple import MQTTClient
import time

# WIFI parameters
# Access point name
ACCESS_POINT = ""
# Access point password
PASSWORD = ""

# AWS endpoint parameters.
# Should be different for each device can be anything
CLIENT_ID = "pico_w_simple"

# AWS iot core endpoint
AWS_ENDPOINT = b'ai9pi313gw5mi-ats.iot.eu-central-1.amazonaws.com'

# MQTT channel name for publishing
PUBLISH_CHANNEL='$aws/rules/telemetry/' + CLIENT_ID

# Set LED 
LED = Pin("LED", machine.Pin.OUT)
LED.value(0)

def get_ssl_params():
    """ Get ssl parameters for MQTT"""
    keyfile = '/keys/private.der'
    with open(keyfile, 'rb') as f:
        key = f.read()
    certfile = "/keys/certificate.der"
    with open(certfile, 'rb') as f:
        cert = f.read()
    ssl_params = {'key': key,'cert': cert, 'server_side': False}
    return ssl_params

def wifi_connect(wlan):
    """Wait for connection"""
    print("WiFi connecting: ", end = "")
    while not wlan.isconnected():
        print(".", end = "")
        led_blink()
        led_blink()
        time.sleep_ms(1300)
        wlan.connect( ACCESS_POINT, PASSWORD )
    
    print(" connected")

def read_internal_temp_sensor():
    """Read internal temperature sensor of Pico W"""
    sensor_temp = machine.ADC(4)
    conversion_factor = 3.3 / (65535)
    reading = sensor_temp.read_u16() * conversion_factor
    temperature = 27 - (reading - 0.706)/0.001721
    return temperature

def led_blink():
    """Led blinking for a simple debug"""
    LED.value(1)
    time.sleep_ms(50)
    LED.value(0)
    time.sleep_ms(50)

# Setup WiFi connection.
wlan = network.WLAN( network.STA_IF )
wlan.active(True)
wifi_connect(wlan)

# Connect to AWS MQTT broker.
ssl_params = get_ssl_params()
mqtt = MQTTClient( CLIENT_ID, AWS_ENDPOINT, port = 8883, keepalive = 10000, ssl = True, ssl_params = ssl_params )
mqtt.connect()

while True:    
    # Publish temperature
    temperature = read_internal_temp_sensor()
    msg = b'{{"temp": {}}}'.format(temperature)
    mqtt.publish(topic = PUBLISH_CHANNEL, msg = msg , qos = 0)
    
    print(msg)
    led_blink()
    time.sleep_ms(2000)
```

## Rules

At this stage your device is able to connect to AWS and send temperature. However there is nothing subscribed to these messages and they are not processed by any rule.

Let's create a new rule under `AWS IoT Core -> Message routing -> Rules`. The rule is called `telemetry` in the above example and a new basic ingest topic name will be `$aws/rules/telemetry`.

Once you create your the rule the settings should look like this:

![Rule](/post_images/2023-03-25/rule.png)

SQL statement defines that we want keep all keys from the original and add topic() as `device_id` and current timestamp as `received_ad`. The `device_id` will be `pico_w_simple` in our case because all message are published to `$aws/rules/telemetry/pico_w_simple`

```SQL
SELECT *, topic() as device_id, timestamp() AS received_at
```

The current rule is still doing nothing as we have not defined any action. You can add a new action by clicking on `Edit` button in the rule. The simple action might be logging all incoming message to CloudWatch Logs.

![Rule action](/post_images/2023-03-25/rule-action.png)

If everything is configure correctly and you run your Pico W you should start getting messages to `iot-core-telemetry` log group.

A message should look like this:

```json
{
  "temp": 27.51254,
  "device_id": "pico_w_simple",
  "received_at": 1679781014810
}
```

And that's it.
