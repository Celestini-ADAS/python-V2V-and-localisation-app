The files ADAS.py, COMS.py and vision.py run on the OBU while the android code runs on a phone. please separate the codes when running locally. The scripts have been put in the same repository only for the sake of convenience.


# OBU : 
## ADAS
An alert generation-transmission system for vehicles. https://www.youtube.com/watch?v=PU8L8YUw1JM
The android part of this project is in the repository "GPS-IMU" https://github.com/Celestini-Lucifer/GPS-IMU-android-Alert

The ADAS project is meant to run on a raspi.

external dependencies : 

opencv (2.4 or better) (pip install python-opencv-3.2.0)

numpy (pip install numpy)

serial (pip install pyserial)

## how it works:
->The ADAS.py file is the main file which uses the vision.py and COMS.py files. 
ADAS.py is supposed to generate an alert for the user when the vision component(vision.py) detects a threat(collision iminent) or sudden braking(by leveraging the sensors in the mobile) and is supposed to broadcast an alert using communications back end(COMS.py, can use any transceiver that works on UART protocol). 

->ADAS.py also checks for the relevance of a received alert. An alert is considered relevant if it is coming from a car in front of us moving in the same direction and on the same road. 

->vision.py : determines whether there is a threat in a given frame by taking the image as well as the optical flow of the image as an input parameter. It generates an alert if there is a large (car-size large) object appearing to move towards the bottom center of the image. This method is highly sensitive to lighting conditions and camera position and orientation. The method also generates false positives when the car goes over a bump on the road.

->COMS.py is a communications back end (not really that complex or sophisticated if you take a peek, it just makes our job easier, thats it) which can handle UART communications. It has been tested for xbees in transparent mode and for RF APC220s. Look into ADAS.py for understanding how to use them.

## Further developments : 
-> refining the system hardware.
-> frequency hopping for different message types.
-> This project is currently being integrated into a mini self driving car project to tinker with the possibilities of combining V2V communication into autonomous cars https://github.com/naughtyStark/Self-driving-car-STM-32

# Smartphone end : 
Android Project back end taken from : https://github.com/maddevsio/mad-location-manager 
The app prints and sends the filtered coordinates, speed and heading over the micro-usb port through an OTG to the raspberry pi.

The following is the readme of the original android project which was used for it's backend in our project. It is included for reference as well as installation instructions if you get stuck.
# mad-location-manager 
This is library for GPS and Accelerometer data "fusion" with Kalman filter. 
Project consists of 2 parts: GpsAccelerationKalmanFusion (AAR module) and 2 helper applications. Main thing here is GpsAccelerationKalmanFusion module.

[Blog (english version)](https://blog.maddevs.io/reduce-gps-data-error-on-android-with-kalman-filter-and-accelerometer-43594faed19c)

[Blog (russian version)](https://blog.maddevs.io/ru-reduce-gps-data-error-on-android-with-kalman-filter-and-accelerometer-b81f1026e06c)

[Our site](https://gps.maddevs.io/en/)


[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## How can mad-location-manager help you to get location more accurately

This module helps to increase GPS coordinates accuracy and smooth "jumps" from track. 

## How to install

Use last version from link below (jitpack): 

[![](https://jitpack.io/v/maddevsio/mad-location-manager.svg)](https://jitpack.io/#maddevsio/mad-location-manager)

## How to use
There is example application in current repository called "Sensor Data Collector" . 

## Theory

Kalman filtering, also known as linear quadratic estimation (LQE), is an algorithm that uses a series of measurements observed over time, containing statistical noise and other inaccuracies, and produces estimates of unknown variables that tend to be more accurate than those based on a single measurement alone, by estimating a joint probability distribution over the variables for each timeframe.

You can get more details about the filter [here](https://en.wikipedia.org/wiki/Kalman_filter).

The filter is a de-facto standard solution in navigation systems. The project simply defines the given data and implements some math.

The project uses 2 data sources: GPS and accelerometer. GPS coordinates are not very accurate, but each of them doesn't depend on previous values. So, there is no accumulation error in this case. On the other hand, the accelerometer has very accurate readings, but it accumulates error related to noise and integration error. Therefore, it is necessary to "fuse" these two sources. Kalman is the best solution here.

So first - we need to define matrices and do some math with them. And second - we need to get real acceleration (not in device orientation) . First one is described in current project's wiki. But second one is little bit more complex thing called "sensor fusion". There is a lot information about this in internet. For real acceleration we need to know 2 things : device orientation and "linear acceleration". Linear acceleration is acceleration along each device axis excluding force of gravity. It could be calculated by high pass filter or with more complex algorithms. Device orientation could be calculated in many ways :

- Using accelerometer + magnetometer
- Using accelerometer + magnetometer + gyroscope
- Using [Madgwick filter](http://x-io.co.uk/open-source-imu-and-ahrs-algorithms/)
- Using virtual "rotation vector" sensor. 

Best results show Madgwick filter and ROTATION_VECTOR sensor, but Madgwick filter should be used when we know sensor frequency. Android doesn't provide such information. We can set minimum frequency, but it could be much higher then specified. Also we need to provide gain coefficient for each device. So best solution here is to use virtual ROTATION_VECTOR sensor. You can get more details from current project's wiki.

## License

MIT License

Copyright (c) 2017 Mad Devs

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
