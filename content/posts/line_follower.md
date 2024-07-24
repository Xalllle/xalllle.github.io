+++
title = "Line Follower Car"
description = "An Intro to Engineering Design Mini-Project"
date = 2024-07-20
+++

![A Silhouette of a line follower car](https://lh3.googleusercontent.com/pw/AP1GczO9RO5wbanPpOPcDrdrVfOZqylvJ6VWh41Fb3jL6Xuru-B4vRQ3VvfhgYiGWMOXVFnSdGZKAZXbJQLy9jTMBbQG9-Vw6D60eQ7RQ0CTgH-ykx5jg2N1Vo4VsIJBv4Byk3QLPuzH4QTr1AUvVTYj1WqPBg=w1034-h775-s-no-gm "Line Follower Car Silhouette")


# Introduction
The first project in Intro to Engineering Design, also known as the mini project, gives students three options for choosing a topic. Those options are:
* Marshmallow launcher
* Mousetrap car
* Line follower

I decided to build a line follower since I wanted to work with microcontrollers and control systems. In this post, I will be omitting most considerations made for the class, and instead will be focusing on the technical details of the actual project.



# Design
The goal of a line follower is to travel along a high contrast line that is drawn onto the ground, as quickly and accurately as possible.

When designing this system, I started by determining the expected inputs and outputs of the line follower control scheme. The input would be some kind of sensor data to determine its rotational position with respect to the line, and the output would be forward or backward movement for the motors.

![Block Diagram of Line Follower Car](https://lh3.googleusercontent.com/pw/AP1GczPENKybI5cwcgWG5uBRzsUQNuX8IC_b5oBvEqkCJCj5e0cXnUUcDrHMdAdM3cE3MK-9xo3tjbETEMOYwj3-hkpAwFLsOspmlrSvk62DY5YnRTc2R9BqMeC9TJmTlxwDJLhIUX2Qkm_fOGxXDDENb9Kq_Q=w1034-h582-s-no-gm "Control Scheme")

To determine rotational position with respect to the line of a track which was not disclosed to use before the demonstration, the car needed to "see" the line. To make this as simple as possible, line follower sensors were the sensor of choice.

Inexpensive line-follower sensors are composed of an infrared LED and an infrared sensor, along with an integrated circuit that interprets the analog value of the infrared sensor input to determine if a surface reflects (white) or absorbs (black) the infrared light. This sensor package is then able to output a high or low state. Using two sensors would be the cheapest and simplest way to control the car, since it could be designed to drive forward and just turn left or right if the sensors detect the line. However, using four of these sensors allows us to increase the resolution of the “location” of the car, which allows for a control system that is much more dynamic, precise, and speedy.

To begin, we were given a chassis that included two wheels, two motors with speed reduction gearboxes, a chassis with a low-friction ball underneath it, a 4s AA battery pack, and an H bridge motor driver. Additionally, we were allowed to use either analog electronics (7400 series ICs) or digital electronics (Microcontrollers). Due to an abundance of arduino boards, I picked the latter. 

The first task was to create a rough prototype to see if this strategy would work. I began by programming a very simple code which would move the car forward, and turn left or right if a sensor "bumped" into the black line. It looked roughly like the psuedocode below:

![Diagram of Line Follower Car](https://lh3.googleusercontent.com/pw/AP1GczNOM74qFjW7Lt1JNa6DTNfGFWLInuplG3iaAVTRzr4Lt0ly5anVFYqesJyTV62y-IfbmlYQaTQQLZBou17yMgeAN4QaP-M9Dgncmyd98WH63KEopp0yJkNTdGLTeRr3FiHjFsEIAcvsvgJ2-y8RhAWo5A=w787-h467-s-no-gm "Diagram")


```
initialize pins/functions

while(1)
    if(left sensor = 1)
        move right
    else if(right sensor = 1)
        move left
    else if(left sensor = 0 and right sensor = 0)
        move forward

move function
```
The result was a very slow and unpredictable car which had a tendency to launch all of the components off of the vehicle whenever it turned. Nonetheless, this test proved that the hardware was capable of the task at hand.

I then soldered an array of line follower sensors to a protoboard to make power distribution easier and make a more rigid system, and I latched them to the front of the car behind a screw that was hanging down from the body. 

![Completed line follower car](https://lh3.googleusercontent.com/pw/AP1GczNZFRnojcoJfYT7Nn_BzgAyqVOWJhfBOXt9AnoCl9W4d2wt1DwKY83tVhGLIYy9pfe0cLW_d664pLzhut5QDAZM6SfgT0Sgaw643-bjSLIh9j1GD-Ook8_0AHEx1wc5pr8gau-umOe2wb9c5B_1pc_UxQ=w1034-h776-s-no-gm "Line Follower Car")
