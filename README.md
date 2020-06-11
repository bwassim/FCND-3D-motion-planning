# FCND - 3D Motion Planning - Project 2
![Quad Image](./misc/enroute.png)

### Objectives of the project

- Load the 2.5D map in the `colliders.csv` file describing the environment.
- Discretize the environment into a grid or graph representation.
- Define the start and goal locations. You can determine your home location from `self._latitude` and `self._longitude`. 
- Perform a search using A* or other search algorithm. 
- Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
- Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0]). 


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

The global home position is read in [line 127](https://github.com/bwassim/FCND-3D-motion-planning/blob/aa0d1bf3241b164c5bf343f427ba8f547a8c4728/grid_motion_planning.py#L127) using the function `read_csv` found in `grid_planning_utils` at [line 201](https://github.com/bwassim/FCND-3D-motion-planning/blob/aa0d1bf3241b164c5bf343f427ba8f547a8c4728/grid_planning_utils.py#L201)
The extracted values for the initial longitude and latitude are `lat0: 37.792480` and `lon0: -122.397450` 

-----
#### 2. Set local position
In the starter code, we assume the drone takes off from map center, but we need to be able to takeoff from anywhere. Retrieve the current position in geodetic coordinates from `self._latitude`, `self._longitude` and `self._altitude`.
Then use the utility function `global_to_local()` to convert to local position (using `self.global_home` as well, which you just set)

The current position in ECEF frame is retrieved using `self.global_position` and converted to NED local coordinates using the function `global_to_local` See [line 133](https://github.com/bwassim/FCND-3D-motion-planning/blob/78c2f45001240e5feff7025a4228b050f71235ae/grid_motion_planning.py#L133)

-------
####  3. Convert start position to current position
In the starter code, the start point for planning is hardcoded as map center. Change this to be the current local position. See lines [149-151](https://github.com/bwassim/FCND-3D-motion-planning/blob/6c87d47ed6e77e30e42153c060aea450943fa6e4/grid_motion_planning.py#L149-L151)

-------
#### 4. Set Goal position on the grid

In the starter code, the goal position is hardcoded as some location 10 m north and 10 m east of map center. Modify this to be set as some arbitrary position on the grid given any geodetic coordinates (latitude, longitude)

Given the geodetic goal coordinates, the goal coordinates in the grid frame are given after converting to NED coordinates using the `global_to_local` function and taking into consideration the north and east offset. See line [156-158](https://github.com/bwassim/FCND-3D-motion-planning/blob/aa0d1bf3241b164c5bf343f427ba8f547a8c4728/grid_motion_planning.py#L156-L158)

**Note**: The goal position are added in the parser and called as follows: 
```python
python grid_motion_planning.py --goal_lat 37.795845 --goal_lon -122.401500 --goal_alt 10
```
It is also necessary to update the `valid_actions` function[ line 94-101](https://github.com/bwassim/FCND-3D-motion-planning/blob/4ccd07e172a278b568f15cae60b5a1b5f13db630/grid_planning_utils.py#L94-L101) 

--------
#### 5. Update the A* algorithm

Write the search algorithm. Added the diagonal motions to the A* implementation, and assigned them a cost of sqrt(2). See lines [59-62](https://github.com/bwassim/FCND-3D-motion-planning/blob/6c87d47ed6e77e30e42153c060aea450943fa6e4/grid_planning_utils.py#L59-L62) in [grid_planning_utils.py](https://github.com/bwassim/FCND-3D-motion-planning/blob/master/grid_planning_utils.py) 

[alt text][path_planning_1]


--------------------
#### 6. 
Cull waypoints from the path you determine using search.

--------------

