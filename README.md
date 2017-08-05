# SDC-Controls-MPC-P10

## Model Predictive Control
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

In this project, I implemented a Model Predictive Control to drive the car around the track. I had to calculate the cross track error (cte) and also take into consideration a 100 millisecond latency between actuations commands on top of the connection latency.

## Overview 
Model Predictive Control reframes the task of following a trajectory as an optimization problem. The solution to the optimization problem is the optimal trajectory. Model predictive control involves simulating different actuator inputs, predicting their result into the trajectory and selecting the trajectory with the minimum cost. 

This project defines a non linear model predictive control system using Udacity's simulator. The simulator provided the vehicle's global 2D location, `x` and `y`, the vehicle global orientation angle between x and y, called `psi`, a yellow projected reference line called `waypoints` in global coordinates and the `velocity` of the vehicle. The waypoints are converted into car coordinate space and then fit into a 3rd order polynomial. The `cte - Cross Track Error`, is defined as the coefficients of the previous 3rd order polinomial evaluated. The `epsi - Orientation Error`, is defined as the change in error, derivated of the vehicle's movement. The 'state vector' is defined by using the car coordinates (x,y and psi). Finally, 'v - the Velocity', is provided by the simulator. This gives us the state vector ` state << px, y, psi, v, cte, epsi`.

The update state is defined by using the IPOPT library, through the `Solve` function. The inputs are the state and the coefficients. The reference waypoint (where the car coordinates are x=0, y=0 and psi=0) is displayed as a yellow line in the simulator.

To complete, the updated values (steering angle, throttle, x, y and the waypoints ptsx and ptsy) are sent to the simulator and are displayed as a green line. The goal of is to have the yellow line match the green line.

### Timestep Length and Elapsed Duration (N & dt)
I started with higher values for dt and found lower seemed to perform better. I settled on `N=16` and `dt=0.16` as this gets the car to visualize .256 steps ahead - considering we have a latency of 0.1, which seems to be a reasonable time frame.

### Polynomial Fitting and MPC Preprocessing
All computations are performed in the vehicle coordinate system. The coordinates of waypoints in vehicle coordinates are obtained by first shifting the origin to the current position of the vehicle and a subsequent 2D rotation to align the x-axis with the heading direction. A 3rd order polynomial is then fitted to the waypoints. 

### Model Predictive Control with Latency
The latency is introduced to mimic real world driving, where actuators are limited by wire distance, mechanical properties and other variables, and so are unlikely to respond instantaneously. The latency is implemented in the process through the following code:
`this_thread::sleep_for(chrono::milliseconds(100));`.

Based on our state vector, we define `px = v * latency`, where `latency = 0.1` to allow for this latency in our predictions.

## Video with Results

A video of the results can be found [here](https://youtu.be/dQHF_KRFk10).