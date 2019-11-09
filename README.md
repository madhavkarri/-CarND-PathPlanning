# **Self-Driving Car**
# **Project: Highway Driving Path Planning**

## MK

Overview
---
Develop a path planner in c++ to safely navigate around a virtual 3 lane highway with other traffic that is driving +/-10 MPH of the 50 MPH speed limit. A successful path planner would keep inside its lane, avoid hitting other cars, and pass slower moving traffic using localization, sensor fusion, and map data.

The Project
---
The goals/steps for this project:
* Ego-car should maintain vehicle speed close to 50 mph, whenever possible
* Ego-car must avoid hitting other cars during all manuvers
* Ego-car must drive inside of the marked road lanes, except when changing lanes
* Ego-car must atleast complete one complete loop around the 6946 (m) length of highway
* Ego-car should take a little over 5 minutes to complete 1 loop
* Ego-car must maintain: (acceleration < 10 m/s^2) and (jerk: rate of change of acceleration < 10 m/s^3)


[//]: # (Image References)

[image1]: ./Writeup_IV/HDPP_FRS.png "HDPP_FRS"

#

### Files Submitted

#### 1. Files included with submission to run the simulator in autonomous mode

Project includes the following files:
* main.cpp contains all the relevant, helper (helpers.h), and header files necessary to execute the path planner to control ego-car within the simulator 
* helpers.h: functions "getXY" (transform from Frenet s,d coordinates to Cartesian x,y) and "distance" (Calculate distance between two points) are the two main functions defined within helpers.h
* spline.h: a function to construct a smooth spline (piece-wise polynomial) passing through a set of X and Y points. spline.h is used in developing a smooth trajectory generation
* README.md summarizes the results
* The first attempt resulted in a distance of 38.9 miles without incident. Final output video [Link](https://www.youtube.com/watch?v=G4B1sXR3a6I&t=10s)
![][image1]


#### 2. Project code (main.cpp)

The main.cpp file contains code for the following set of tasks:
* Read/load map data to acquire highway waypoint data [Link](./src/main.cpp#L33-L59)
* Connect to highway simulator using [micro-websocket](https://github.com/uNetworking/uWebSockets). Websocket handles two-way data transfer between planner and simulator

#### Sensing (Telemetry)
Gather below listed data from simulator [Link](./src/main.cpp#L80-L102):
* Ego car localization data
* Previous unused path from the simulator
* Localization data of other cars

#### Prediction [Link](./src/main.cpp#L110-L165):
* Based on data from sensing, predict other cars localization with respect to ego car. Detect whether other cars are present ahead, left, right of ego car

#### Behavior Planning and Trajectroy Generation [Link](./src/main.cpp#L165-L350):
#### Behavior Planning:
* If other car ahead of ego car (impement below listed actions in oder)
  - Check no other car on left lane and if left lane available, switch to left lane
  - If not, check no car on right lane and if right lane available, switch to right lane
  - Else, deccelerate ego car vehicle speed to avoid hitting other car ahead
* If ego car not on center lane, switch to center lane if it is safe to do so
* If ego car vehicle speed less than maximum allowable vehicle speed (47.5 mph), accelerate ego car vehicle speed

#### Trajectroy Generation
* In a nutshell, trajectory generation is performed by combining waypoints from previous path trajectory and a new set of way points generated by using a spline curve
* The inputs to generate a spline curve are as follows:
  - The last two waypoints points from the previous path trajectory
  - To the above two waypoints 3 additonal waypoints are added at increments of 30, 60, and 90 meters
  - Addition of end points from previous path trajectory to the input of spline function will result in a smooth transition between successive path trajectories
* New path trajectory is generated as follows:
  - Step 1: Initialize new path trajectory by adding unused waypoints from previous path trajectory
  - Step 2: To step 1 add new waypoints by using spline curve generated previously
  - The combined total of previous and new waypoints in the new path trajectory is a fixed set of 50 waypoints
  - In addition, irrespective of the number of new waypoints added to the new path trajectory, the combined distance of the newly added waypoints is a fixed length of 30 meters

#### Next Steps and Future Updates
* In the presence of other car ahead of ego car, the ego-car does not smoothly follow the other car. Improvize the logic, may be maintain a constant pre-set distance to the other car
* May be explore to implement finite state machines

