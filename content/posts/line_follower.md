+++
title = "Line Follower Car"
description = "An Intro to Engineering Design Mini-Project"
date = 2024-07-23
+++
###### (Please note that this project was created in the Spring semester of 2024, but the article was published on the date above)

# Introduction
The first project in Intro to Engineering Design, also known as the mini project, gives students three options for choosing a topic. Those options are:
* Marshmallow launcher
* Mousetrap car
* Line follower

I decided to build a line follower since I wanted to work with microcontrollers and control systems. In this post, I will be omitting most considerations made for the class, and instead will be focusing on the technical details of the actual project. (The technical memo is linked at the bottom of the page if you are interested in either more detail or how this is related to the course)

![A Silhouette of a line follower car](https://lh3.googleusercontent.com/pw/AP1GczO9RO5wbanPpOPcDrdrVfOZqylvJ6VWh41Fb3jL6Xuru-B4vRQ3VvfhgYiGWMOXVFnSdGZKAZXbJQLy9jTMBbQG9-Vw6D60eQ7RQ0CTgH-ykx5jg2N1Vo4VsIJBv4Byk3QLPuzH4QTr1AUvVTYj1WqPBg=w1034-h775-s-no-gm "Line Follower Car Silhouette")

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

![Sensor array on front of car](https://lh3.googleusercontent.com/pw/AP1GczNedksKaMOZGTW4kv99N0c8MSF3jJ9WTQjmD04Esyz1KzPEv4dWWj5PvtZ7kJVh5c2uS14Eg3IZi7ouFiMwqpy79Fha76dBpd_h5w46q6s_cVEx3c2Vqcm9YitFgFDmGWSdrjjxjVwgM3RFkMnMmYWdLg=w1034-h775-s-no-gm "Sensor Array")

Next, I wanted to create a program that used a PID control scheme so that I could easily control and modify the vehicles ability to adhere to a line. I created a sensor function which would return the location of the car on the track, and for the PID scheme I assumed that the car always wanted to be at position "0". The code was very simple, and followed the simple program below:
```
#define ls1 2
#define ls2 4
#define ms 5
#define rs1 6
#define rs2 7
#define motorL 11
#define motorR 9
#define motorLBACK 10
#define motorRBACK 3

int on = 0; //Control Loop Toggle
int leftWheelOut; //Output to pin 9
int rightwheelOut; //Output to pin 10
float kp = 1;
float ki = 0.0001;
float kd = 0.1;
int bias = 0;
int timelast = 0;
int integral = 0;
int derivative = 0;
float output = 0;
int time = 0;
int desired_value = 0;
int actual_value = 0;
int error = 0;
int integral_prior = 0;
int error_prior = 0;
int sensorCheck = 0;
int leftSensor = 0;
int rightSensor = 0;

void setup() {
    pinMode(ls1, INPUT_PULLUP);//Initialize left sensor 1
    pinMode(1s2, INPUT_PULLUP);//Initialize left sensor 2
    pinMode(rs1, INPUT_PULLUP);//Initialize right sensor 1
    pinMode(rs2, INPUT_PULLUP);//Initialize right sensor 2
}

void loop() {
    position();
    if(!digitalRead(13) and sensorCheck){
        on = 1;
    }
}

while(on){ //True when the toggle pin has been made high and one or more of the sensors has changed state

    //////////////////////
    //PID Control Scheme//
    //////////////////////

    timelast = time-millis();
    desired_value = 0;
    actual_value = position();
    error = desired_value - actual_value:
    integral = integral_prior+error-timelast;
    derivative = (error - error_prior)/timelast;
    output = kp*error+ki*integral+kd*derivative+bias;
    error_prior=error;
    integral_prior=integral;
    time = millis();

    /////////////////////////
    //Differential Steering//
    /////////////////////////

    leftwheelOut = 30+output; //inversely offsets the PWM value of the wheels
    rightwheelOut = 30-output;

    if (leftwheelOut >= 0){ //Tries to allow for wheels to spin backwards in case of very tight turns
        analogWrite(motorL,pwm( leftWheelOut));
        analogWrite(motorLBACK, 0);

    } else if (leftWheelOut < 0 ){
        analogWrite(motorL, 0);
        analogWrite(motorLBACK, pwm(leftWheelOut));
    }

    if (rightwheelOut -= 0){
        analogWrite(motorR, pwm( rightwheelout));
        analogWrite(motorRBACK, 0);

    } else if (rightwheelOut < 0 ){
        analogWrite(motorR,0);
        analogWrite(motorRBACK  pwm( rightwheelOut));

    }

int position(){
    //This function returns the current location of the vehicle on the black line;
    //It assumes that the location is centered if no black line is detected
    int actualPosition = 0;
    int leftSensor2 = digitalRead(ls2);
    int leftSensor1 = digitalRead(ls1);
    int middleSensor = digitalRead(ms);
    int rightSensor1 = digitalRead(rs1);
    int rightSensor2 = digitalRead(rs2);
    if ((leftSensor2 == 0) or (leftSensor1 == 0) or (middleSensor == 0) or (rightSensor1 == 0) or (rightSensor2 == 0)){
        sensorCheck = 1;
    }
    if ((leftSensor2 == 1) && (rightSensor2 == 1)) {
        actualPosition = 0;
    } else if (leftSensor2 == 1) {
        if(leftSensor1 == 1){
            actualPosition = 30;
        } else {
            actualPosition = 70;
        }
    } else if (rightSensor2 == 1) {
        if(rightSensor1 == 1){
            actualPosition = -30;
        } else {
            actualPosition = -70;
        }
    } else if (leftSensor1 == 1){
        if(middleSensor == 1){
            actualPosition = 10;
        } else {
            actualPosition = 20;
        }
    } else if (rightSensor1 == 1){
        if(middleSensor == 1){
            actualPosition = -10;
        } else {
            actualPosition = -20;
        }
    } else {
        actualPosition = 0;
    }
}

}
```

Alas, tuning the kp, ki, and kd values allowed for a very accurate way to control the vehicle's movement sensitivity, and the only course that the car wasn't capable of completing was the very hard course (due to my inability to get the H-Bridge driver to drive the wheels backwards, which would allow the turn radius to be smaller than the length of the car).

![Completed line follower car](https://lh3.googleusercontent.com/pw/AP1GczNZFRnojcoJfYT7Nn_BzgAyqVOWJhfBOXt9AnoCl9W4d2wt1DwKY83tVhGLIYy9pfe0cLW_d664pLzhut5QDAZM6SfgT0Sgaw643-bjSLIh9j1GD-Ook8_0AHEx1wc5pr8gau-umOe2wb9c5B_1pc_UxQ=w1034-h776-s-no-gm "Line Follower Car")

# Design Choices

## Batteries
Lithium-ion batteries are against the rules of this contest since they are considered dangerous, so we had to use some other chemistry. Alkaline batteries are considered safe, but have a very limited power output, which we found was nearly inadequate for the project. The batteries discharged quickly, which made testing very challenging and was cause for concern for demonstration day.
## Sensor placement
Initially the sensors were placed leaning over the front of the car. By putting these sensors so far from the turning axis, it artificially makes the car much longer, and as a result makes it challenging to make tight turns. This is improved by cutting out a hole on the bottom of the car, and placing the sensors as close to the wheels as possible.
## Weight distribution
On top of the sensor location being important for turn radius, the center of mass needs to project over the turning axis, otherwise the motors experience excessive strain and draw too much energy. 

# Conclusion
I did quite well on this project, and the technical memo can be found [here](https://docs.google.com/document/d/1lLpQpZ7HjyCS6mYA5WTK6pZ1qwjJXwpjk2m00m6qDpM/edit?usp=sharing). I attempted to remove any of my partners personal information from this document. If you have any questions about this project, please feel free to send me an email at ah@alectronix.net!