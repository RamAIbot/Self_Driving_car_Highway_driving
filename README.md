# Highway Driving

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)
   
<img src="Capture.JPG" alt="image"/>

<h2> Overview </h2>
<p>  The project focusses on path planning for the self driving car in Highways. The project uses sensor fusion to get the data about its surrounding vehicles. A behivour planner is used to estimate the best state at any given time based on the sensor fusion data, car's position and map's data. It uses a finite state machine to find out the next state based on the particular behaviour. The trajectory generator generates various trajectories at a given time based on the state output of the behaviour planner and uses a cost function to select the best trajectory at a given time. The output of the trajectory planner is given to motion control system to implement the trajectory. Here we assume that the car implements the behavior without any error as given by the output of the trajectory planner (like the car moves 10m forward if planner sent that without any error), so we don't use a controller here.</p>

