
# CarND-Path-Planning-Project

## Overview
The objective of this project is to implement a path planning algorithm to drive a car safely on a virtual highway in a simulator provided by Udacity. The simulator sends car telemetry information (car's position and velocity) and sensor fusion information about the rest of the cars in the highway (Ex. car id, velocity, position). It expects a set of points spaced in time at 0.02 seconds representing the car's trajectory. The vehicle must not violate any of the set threshold values such as jerk and acceleration and must not come in contact with other vehicles on the road. 

## Model Description
The main.cpp file listens to the message sent from the simulator (using web sockets). The simulator sends the data related to car, like Car’s location, velocity, yaw rate, speed and Frenet coordinates (longitudinal and lateral displacement). It also sends the data from the sensor fusion which contains data for other cars in vicinity.

This model is made up of three parts

1. Prediction
2. Behavior Planning
3. Trajectory Generation

### Prediction

In order to plan the behavior of our vehicle we need to use the sensor data passed from the simulator that contains a list of vehicles detected. The data from the sensor fusion and simulator is used to generate the predictions about the likely behavior of moving objects. We use prediction to get answers to the following questions as our car keeps moving on the highway

1. Is there any slow moving car ahead of us because of which we have to change the speed of our car or change lane
2. Is there a car to the right of us making a lane change not safe
3. Is there a car to the left of us making a lane change not safe.

These questions are answered by calculating the lane each other car is in and the position it will be in at the end of the last plan trajectory. A car's position is considered too close when its distance to our car is less than 30 meters in front or behind us.
