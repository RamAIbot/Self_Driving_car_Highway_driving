# Highway Driving

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)
   
<img src="Capture.JPG" alt="image"/>

<h2> Overview </h2>
<p>  The project focusses on path planning for the self driving car in Highways. The project uses sensor fusion to get the data about its surrounding vehicles. A behivour planner is used to estimate the best state at any given time based on the sensor fusion data, car's position and map's data. It uses a finite state machine to find out the next state based on the particular behaviour. The trajectory generator generates various trajectories at a given time based on the state output of the behaviour planner and uses a cost function to select the best trajectory at a given time. The output of the trajectory planner is given to motion control system to implement the trajectory. Here we assume that the car implements the behavior without any error as given by the output of the trajectory planner (like the car moves 10m forward if planner sent that without any error), so we don't use a controller here.</p>

<h2> Finite State Machine </h2>

<img src="" alt="FSM"/>

<p> The behavioural planner uses Finite state machine to find out the next state based on the current state and measurmenets from sensors.The ready state is the initial state where the car starts from rest and reaches the desired speed limit. The speed limit for the project is 50 mph. To avoid jerk we set the limit the change in acceleration to be less than 10m/s. (here we use 5m/s). So when car is in ready state it acclerates to the speed if (49.5mph in this case) and moves to lane keep state. </p>

<p> Now if the car finds any car preceding it, it goes to prepare lane change state. It first goes to prepare lane change left state where it uses the sensor fusion data to find out the cars in the left lane. It interpolates the current position of the other car by multiplying the car's position with the frame rate (0.02s) and the points our car has pending from the current state before making the lane shift to get the new position of the other car in the left lane. If the value is greater than 30m then it gives a green flag for lane chaning.</p>

<p> The same is repeated for prepare for lane change right state. The keep lane state first checks whether lane chaning is feasible for left lane and then right lane. If any one lane change maneuver is feasible then it moves to that particular lane change left or lane change right state. If both lanes are free then we apply a cost function based on the number of cars detected by the sensor fusion in the particular lane and shift to the lane with lesser number of cars.</p>

<h2> Behavioural planner </h2> 

<h4> Psuedo code </h4>

```
Initialized Max Speed to let say 49.5
Initialized Acceleration to .224 (5m/s)
IF Car Ahead 
   Reduce Acceleration
   
	IF there is NO car on the left lane AND ego car is NOT on the left lane
		Change lane to left
	ELSE IF there is NO car on the right lane AND ego car is NOT on the right lane
		Change lane to right
	
		
ELSE
   IF Reference Velocity is LESS THAN Max Speed
	   Increase Acceleration
    
	IF ego car is NOT on the center lane
		IF ego car is on the left lane AND there is NO car on the right    OR    ego car is on the right lane AND     there is NO car on the left
			Go back to the center lane

   
```

<p> The behavioural planner is implemented based on the above psuedo code. After the car is set to the desired state from rest, it checks whether any car is ahead of it or not. If so then it prepares for lane change based on the FSM above and moves to corresponding lane. If both the left and right lanes are occupied then the our car is deacclerated until we are in safe distace from the car ahead (30m).</p>

<p> If there is no car ahead of us we check whether our ego car is in center lane and our car drives in reference speed of 49.5 mph. If the speed is less we accelerate and if the car is not in center lane but in left lane we check our right lane and move to right if it is free and vice versa.</p>


<h2> Frenet Coordinates </h2>

<p> The planner uses frenet coordinate system to estimate the vehicles around it and to compute the trajectory. The main advantage of using frenet is that it gives linear function even for curvy roads. The cartesian coordinate system for the road of below kind gives a quadratic equation.</p>

<img src="" alt=""/>

<p> But the frenet coordinates gives a direct straight line  as below.</p>

<img src="" alt=""/>
<img src="" alt=""/>
<p>The d parameter in frenet coordinate system corresponds to the lanes width or  lateral displacement. The center yellow lane is marked as 0 in the project, the left lanes are numbered positive and right are negative. The lane width used in the project os of 4m and our car is in the center. The s parameter is denotes the longitudinal displacement which represents distance along the road.</p>

<h2>Trajectory Generation </h2>

<p>The trajectory generation module uses spline library of c++ to provide polynomial fitting for the points. The main advantage of spline library is that it generates smooth trajectory even in curved roads. Now to provide smooth traversal from one state to another, we use the previous state values and fill the array for new states only for those points which are travelled in the previous state. We keep an array of size 50 for this purpose. First if the previous state array is empty we fill the present car coordinates and we find out the previous coordinate by subtracting the cosing and sine of yaw angle. If the array has values we take only the last 2 elements for generating the next state points. </p>

<p> We then interpolate the previous points with the last measured car's s value incremented by 30,60 and 90 to generate the next trajectory using spline function. Before we pass to spline we just transform all the 5 new points to last measured car's point space to make the yaw angles 0.</p>
