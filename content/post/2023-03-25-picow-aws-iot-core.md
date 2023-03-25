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

#### Creating a new AWS IoT Core Thing

##### Create a thing
You need to navigate to AWS IoT Core -> All devices -> Things and click on `Create things` button.
Create a thing in AWS and call it `pico_w_simple` with a unnamed shadow (classic)

![Create a thing](/post_images/2023-03-25/thing-create.png)

##### Generate a new certificate
In order to communicate with AWS the device needs to have x509 certificate. This allows a device 
to connect to AWS IoT Core (mqtt broker). 

![Create a new certificate](/post_images/2023-03-25/thing-new-cert.png)

##### Policy
We will create a basic aws policy that will be later on attached to the certificate. The policy
defines more granular actions a device can perform. We create a basic policy that allows all actions.

![Create a new policy](/post_images/2023-03-25/new-policy.png)

##### Attach the policy
Attach previously created policy to the certificate.

![Attach policy](/post_images/2023-03-25/attach-policy.png)

##### Download all certificates and keys
This is the last step where we need to download at least private key and certificate. Once you close this window you won't be able to download it anymore.

![Download all keys and certificates](/post_images/2023-03-25/download-keys.png)

##### Keys convertion 
Both private key and certificate are in PEM format. We will need to convert them to DER format as this format is later on used in our code. We can use the following snippet.

```bash
#!/bin/bash

# Converts a certificate and private key downloaded from AWS 
# in PEM format into DER format.

openssl x509 -in certificate.pem.crt -out certificate.der -outform DER
openssl pkey -in private.pem.key -out private.der -outform DER

```

