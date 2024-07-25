+++
title = "Firefighter Drone"
description = "An Intro to Engineering Design Project"
date = 2024-07-24
+++
###### (Please note that this project was created in the Spring semester of 2024, but the article was published on the date above)

![](https://lh3.googleusercontent.com/pw/AP1GczPp-6zdX3NVKqDAgVj4GRoVj5TPLPrUcUHurmz4wPOTK-iGQgIrFTbu8DItPhxJjFZ1lh7sQzT6qnIjsL1vtyuk9oDI6MSfeTmsKCW9wfEYNzW2Z5ywCPrKnYCLiUH51EuBeg4dv8UD0L-4v2WAjiQH6Q=w1498-h1998-s-no-gm "")


# Introduction
Engineering students are required to take Introduction to Engineering Design. The second project in this class is called the main project, and students are required to do market research to create a marketable solution to a major world problem.
My team decided to follow a framework to create a fleet of drones which would be able to supply a continuous stream of fire suppressant. If hundreds or thousands of inexpensive drones were created, this could potentially be deployed continuously in a region. Our team’s task was to create one drone which would be part of the fleet.

# Design
My task was to design the electronics subsystem. The electronics subsystem had to receive and process input from the user and sensors, and then control the motors as well as the fire suppressant system. Additionally, it had to supply power to all components of the drone.

## I/O
First, I identified the inputs and outputs of this system. The drone needed to be capable of knowing its location and orientation, the drone needed to be capable of detecting smoke and wildfire heat, and the drone must be able to report its findings to home base remotely.
![](https://lh3.googleusercontent.com/pw/AP1GczNyrwjIHxpj-j7RAzPPAuWOYE3SOXrfQJCValoG3hRjaYe-0xjn-kHW7AlXPdwRijEXrc1TI3bTfqJqKgVciCqQLpWIBxfghCwWRqhKM955b0bZNqvZz82F89Sr8Uv0mdhABdcNqNaTgXrMM9AhumBszA=w780-h721-s-no-gm "")

## Location/Orientation
For location and orientation, the drone has a 6 axis accelerometer/gyroscope and GPS capabilities. For the gyroscope, I used an MPU-6050 board. Retrospectively, I should have gotten a board with a built-in motion processor. Since I did not have that, I used a first-in first-out scheme (FIFO) which would be triggered by an interrupt provided by the sensor. This greatly degraded performance and the arduino sometimes had difficulty keeping up with the gyroscope and other processes on the microcontroller.
I was having difficulty getting the GPS to work. I downloaded the Icom GPS utility and started troubleshooting, but I found that it would only detect a single satellite at a time, and not for very long. I then realized that I was in a concrete and rebar building, so I went outside and was able to detect many more satellites and my issue was solved. The simplest solutions sometimes work best.

## Wildfire Detection
For detecting smoke and heat, I used an 8x8 infrared sensor and an MQ gas sensor. The plan was to have the drone “seek” the smoke, and then once close enough, position itself a few hundred feet directly above the fire to release the fire suppressant, similar to rain. The implementation of these was very simple, as I only needed to take libraries to read the infrared and gas sensors I2C data and include it in my control scheme.

## Data Transmission
Finally, to report the data, a very simple 2.4GHz transceiver was used. In practical application, most of these sensors would be extremely inadequate due to either IP rating or lack of long distance transmission, but our demonstration would be done outside in a field.

## PCB Design
I was able to prototype all of the sensors on a breadboard, and then I was prepared to make a PCB design using KiCad. 
![](https://lh3.googleusercontent.com/pw/AP1GczNfAlmId5djaGCzZHkO8cfq8KpnZEXCdxxDlWf62LMveWvEv8H-yDyoAo0yokryDSsYiWfLUnaQgOaj5Q8v79tD0yCKqtF2qALrogsXBmSHaA1Lh0inrx0pCEipd4Mikx-VjpvHqm8bz12t_niOoyyNZg=w1579-h682-s-no-gm "")
I began by planning for power distribution. My teammate had identified four motors that we could use, each of which were listed to use a maximum of around 20-30 amps. For our drone, this was extremely overkill and making the power handling of the PCB that high would have been very difficult. I was able to design the PCB to handle about 10 amps per motor with 1oz copper (the least expensive copper thickness). This was a design risk that would be intolerable in most circumstances, but I took the risk since this project was paid for out of pocket.

![](https://lh3.googleusercontent.com/pw/AP1GczM7JnotkVjNI9-9kGRZqcJs_fpt2KM2wrrCLxg8CTQVvhtqJFDOtMbImnjeU59z1ZHJ4v1V7r_-buVosfx06JaKryjgR5oBD6WoFS8hfuZxYv1eaQ3_apPB-kZwoBVcxKJhnUvEBC5h_Ozjs0yqZmJ1ZQ=w484-h660-s-no-gm "")
I designed the power input to the PCB with a XT60 connector (which would be connected to a ~12v supply) and then it was directly distributed to the four electronic speed controllers (ESCs) which were soldered directly to the four motors. 
Since the operation of these motors was unpredictable in nature (due to a constant need to adjust for wind, sudden acceleration, etc.), I used a decoupling capacitor for every motor.
To generate the 5v supply, I used a linear voltage regulator since the power draw of the sensors and controller were calculated to be well under one amp. With the correct capacitors specified by the datasheet, this solution was sufficient even with the noise created by the motors. Additionally, I tried to locate the high current traces away from the data traces to prevent crosstalk.
Finally, I added the pins that would connect the microcontroller data output to the ESC. 
![](https://lh3.googleusercontent.com/pw/AP1GczNDG4WVet-IOVTXQqo63Iu-o6PFp_ayACP8UomL5kKfgzh-KMcon8FL5gPRJaS5xrn044dncxCOH_YJIZv5VC1_N9QeDZqfccdPANnKINfJU7g-nzyVcQ_8T3_NLJGm3cvt6x9IVFC5ORcWKmNju9DXVg=w365-h362-s-no-gm "")
![](https://lh3.googleusercontent.com/pw/AP1GczMF2s_T5M-dUsHKgb1BPT8mnchVkRsdwZDmGN_I0qjB5G9Ei48YXNrlamLDXTJV2Q4RdOQThsJvRCQYqd5yHIT3qPCEjoVgHytAzHXQ4V1aa2goB_F_tPsaw7h1a_1fqJBKbQLnJLjLOJKkF2PC2L-f4Q=w327-h370-s-no-gm "")
There were numerous errors that I made in this PCB. The first error I made was reversing the power pins on the radio transceiver, which immediately prevented remote operation. The gyroscope pinout was also done incorrectly, and the pins to send data to the ESCs were not connected at all. I attempted to fix all of these issues by soldering wires on the back of the board, but I was only successful in fixing the ESC pins. This meant that, at the time of the class, I was not able to make the drone fly, and the motors would only work with the drone tethered.

![](https://lh3.googleusercontent.com/pw/AP1GczNh8Hyg4hvAUN3YtyUfR17wsfh4Hxy0IZZekc1P8JBTtBI-IQuEsHjHU2FRbnXpNHsrF-e1oWSghhe5K-Fr8Wpf946nK_0i4IRr5LUEZ39t-UlKMPY4SL4TjagVB8jXD4jWK61e3ivFOTIFG54b0CQWtA=w659-h420-s-no-gm "")

This article was meant to be a brief overview of design choices and a way to remind myself about this project. If you have any comments or questions, feel free to email me at ah@alectronix.net!