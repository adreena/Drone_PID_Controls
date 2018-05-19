

## Development Environment Setup ##

Regardless of your development platform, the first step is to download or clone this repository.

Once you have the code for the simulator, you will need to install the necessary compiler and IDE necessary for running the simulator.

Here are the setup and install instructions for each of the recommended IDEs for each different OS options:



### The Code ###

For the project, the majority of your code will be written in `src/QuadControl.cpp`.  This file contains all of the code for the controller that you will be developing.



When you run the simulator, you'll notice your quad is falling straight down.  This is due to the fact that the thrusts are simply being set to:

```
QuadControlParams.Mass * 9.81 / 4
```

Therefore, if the mass doesn't match the actual mass of the quad, it'll fall down.  Take a moment to tune the `Mass` parameter in `QuadControlParams.txt` to make the vehicle more or less stay in the same spot.

Note: if you want to come back to this later, this scenario is "1_Intro".

With the proper mass, your simulation should look a little like this:

<p align="center">
<img src="animations/scenario1.gif" width="500"/>
</p>

### Scenario 2

In this scenario, quad starts above the origin with a small initial rotation speed about its roll axis and the task is to stabilize its rotational motion and bring it back to level attitude.

##### Body Rate Control

  - `GenerateMotorCommands()`: Calculates the thrust for each quad using the total thurst, 3-axis moments and the arm length.
  - `BodyRateControl()`: Calculates the target 3-axis moment based on the target and the current body rate. The commanded roll, pitch, and   yaw are collected by the body rate controller, and they are translated into the desired rotational accelerations along the axis in the body frame. 
     * `kpPQR`: it gets the vehicle to stop spinning quickly without overshooting
     * u_bar_p = kp_p * (p_target - p_current)
     * u_bar_q = kp_q * (q_target - q_current)
     * u_bar_r = kp_r * (r_target - r_current)
  
  With the angle ratio gain `p=90, q=100, r=10` the rotation of the vehicle about roll (omega.x) get controlled to 0 while other rates remain zero.  

##### Roll Pitch control

 - `RollPitchControl()` is a P controller responsible for commanding the roll and pitch rates in the body frame, it receives the current attitude of the vehicle and sets the target rate of change of the given matrix elements R.
 - `kpBank` parameter minimize the settling time

With `kpBank=10` quad is able to level itself (as shown below), and its angle (Roll) gets controlled to 0.

<p align="center">
<img src="animations/scenario2.gif" width="500"/>
</p>


### Scenario 3

##### Position Velocity Control
Next, you will implement the position, altitude and yaw control for your quad.  For the simulation, you will use `Scenario 3`.  This will create 2 identical quads, one offset from its target point (but initialized with yaw = 0) and second offset from target point but yaw = 45 degrees.

 - `LateralPositionControl()`
 - `AltitudeControl()`
 - `kpPosZ` and `kpPosZ`
 - `kpVelXY` and `kpVelZ`

If successful, the quads should be going to their destination points and tracking error should be going down (as shown below). However, one quad remains rotated in yaw.

##### Yaw Control
 - implement the code in the function `YawControl()`
 - tune parameters `kpYaw` and the 3rd (z) component of `kpPQR`

Tune position control for settling time. Don’t try to tune yaw control too tightly, as yaw control requires a lot of control authority from a quadcopter and can really affect other degrees of freedom.  This is why you often see quadcopters with tilted motors, better yaw authority!

<p align="center">
<img src="animations/scenario3.gif" width="500"/>
</p>


###  Scenario 4
Non-idealities and robustness
In this part, we will explore some of the non-idealities and robustness of a controller.  For this simulation, we will use `Scenario 4`.  This is a configuration with 3 quads that are all are trying to move one meter forward.  However, this time, these quads are all a bit different:
 - The green quad has its center of mass shifted back
 - The orange vehicle is an ideal quad
 - The red vehicle is heavier than usual

1. Run your controller & parameter set from Step 3.  Do all the quads seem to be moving OK?  If not, try to tweak the controller parameters to work for all 3 (tip: relax the controller).

2. Edit `AltitudeControl()` to add basic integral control to help with the different-mass vehicle.

3. Tune the integral control, and other control parameters until all the quads successfully move properly.  Your drones' motion should look like this:

<p align="center">
<img src="animations/scenario4.gif" width="500"/>
</p>


### Tracking trajectories ###

Now that we have all the working parts of a controller, you will put it all together and test it's performance once again on a trajectory.  For this simulation, you will use `Scenario 5`.  This scenario has two quadcopters:
 - the orange one is following `traj/FigureEight.txt`
 - the other one is following `traj/FigureEightFF.txt` - for now this is the same trajectory.  For those interested in seeing how you might be able to improve the performance of your drone by adjusting how the trajectory is defined, check out **Extra Challenge 1** below!

How well is your drone able to follow the trajectory?  It is able to hold to the path fairly well?


