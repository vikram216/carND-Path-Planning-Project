
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

### Behavior Planning

This part decides what our car should do based on the input from prediction. There can be following behaviors which our car can do based on the other moving cars.

| Prediction                                                 | Behavior of our car                                                                                                |
|------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| Car ahead us is too close                                  | Slow down our car                                                                                                  |
| Car ahead us is driving slow                               | See if we can change lane safely                                                                                   |
| Car ahead us is slow and there is no car on left lane      | Change the lane to left lane (but see first if it is safe to change the lane and we are not in the left most lane) |
| Car ahead us is slow and there is another car in left lane | Change the lane to right (but see first if it is safe to change the lane and we are not in the right most lane)    |
| There is no car ahead of us or car is too far away         | Increase the speed of the car to approx. speed limit and maintain the lane                                         |

### Trajectory Generation

The calculation of trajectory is based on the speed, other vehicles position, xvelocity and lane, current lane, car coordinates and past path points. To make the trajectory smooth we adds the last two points from the previous trajectory path. If there are no previous paths we calculate the previous point from the current yaw rate and the current car coordinates.

Also we add three way points in next 30 meters, 60 meters and 90 meters to the trajectory. All these points are then shifted to the car reference angle (local car coordinates) – this is done to simplify the calculations.

We then use spline library to get the remaining points of the trajectory based on the current path (we are adding total of 50 points to the trajectory). All these points are then again moved to the global coordinates (so that these can be sent to the simulator to generate the trajectory and to drive the car based on this trajectory). The speed change is decided on the behavior part of the code, but it is used in that part to increase/decrease speed on every trajectory points instead of doing it for the complete trajectory.

### Final Result

[Here] (https://youtu.be/16kchiB8ux0) is a link to the video that I recorded while the car was run autonomously in the simulator. The car was able to drive around the virtual highway in the simulator taking care of all the Rubric points mentioned in the project. 

![alt Text](Distance_covered_without_collision.jpg)


