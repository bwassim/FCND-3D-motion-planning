# FCND - 3D Motion Planning - Project 2
![Quad Image](./misc/enroute.png)



###  Explain what's going on in  `motion_planning.py` and `planning_utils.py`

The structure of the code of the motion planning is different from that of the backyard flyer in the following points:
1. We have an additional `flight_state` consisting of the `PLANNING` state
2. Therefore we added the condition on this new state in the state_callback function
3. The waypoints does not constitute a box anymore. The path constructed using the A* algorithm is converted to actual waypoints.
4. The other functions remains the same
5. Plan_path is the function where most of the work will be done, since the main goal of this exercice is to find a way to go from point A to point B while avoiding obstacles. This is done through generating a path and then, as mentioned before, generate the minimum number of waypoint for the drone to follow.
6. After creating the grid and defining the offsets we use the A* algorithm to generate the waypoints that we send to the autopilot



### Implementing the Path Planning algorithm

#### 1. Set the global home position

In the starter code, we assume that the home position is where the drone first initializes, but in reality you need to be able to start planning from anywhere.
Modify your code to read the global home location from the first line of the `colliders.csv` file and set that position as global home `(self.set_home_position())`

The global home position is read in [line 127](https://github.com/bwassim/FCND-3D-motion-planning/blob/aa0d1bf3241b164c5bf343f427ba8f547a8c4728/grid_motion_planning.py/README#L127) using the function `read_csv` found in `grid_planning_utils` at [line 201](https://github.com/bwassim/FCND-3D-motion-planning/blob/aa0d1bf3241b164c5bf343f427ba8f547a8c4728/grid_planning_utils.py/README#L201)

-----
In the starter code, we assume the drone takes off from map center, but you'll need to be able to takeoff from anywhere. Retrieve your current position in geodetic coordinates from `self._latitude`, `self._longitude` and `self._altitude`.
Then use the utility function `global_to_local()` to convert to local position (using `self.global_home` as well, which you just set)

-------
In the starter code, the start point for planning is hardcoded as map center. Change this to be your current local position.

-------

In the starter code, the goal position is hardcoded as some location 10 m north and 10 m east of map center. Modify this to be set as some arbitrary position on the grid given any geodetic coordinates (latitude, longitude)

------

Write your search algorithm. Minimum requirement here is to add diagonal motions to the A* implementation provided, and assign them a cost of sqrt(2).
However, you're encouraged to get creative and try other methods from the lessons and beyond!

--------------------

Cull waypoints from the path you determine using search.

--------------

