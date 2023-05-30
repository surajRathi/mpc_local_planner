# mpc_local_planner
This package is a Model Predictive Control based Local Planner for ROS Melodic.
It is a plugin to `base_local_planner` of the 2D [ROS Navigation stack](http://wiki.ros.org/navigation).
It wraps the library [surajRathi/mpc_lib](https://github.com/surajRathi/mpc_lib).

Forked from [project-TARIV/mpc_local_planner](https://github.com/project-TARIV/mpc_local_planner) to add documentation.

# Dependencies:

The package should be built within a catkin workspace. Dependencies are defined in `package.xml`.

# Working

The package has two steps of note.

1. Converting ROS global path and costmap structures to polynomials in the local frame suitable for MPC.
2. Invoking the `mpc_lib` MPC formulation.

# Configuration
- `frequency`: Frequency at which points are sampled in the vehicles predicted trajectory, *int*
- `reference/velocity` : The target velocity for the vehicle, *float*
- `limits/vel/low` : Hard limit on velocity, *float*
- `limits/vel/high` : Hard limit on velocity, *float*
- `limits/acc/low` : Hard limit on acceleration, *float*
- `limits/acc/high` : Hard limit on acceleration, *float*
- `weights/cte` : Cross track error weight, *float*
- `weights/etheta` : Yaw error weight, *float*
- `weights/vel` : Linear velocity error weight, *float*
- `weights/omega` : Angular velocity error weight, *float*
- `weights/acc` : Acceleration minimizing weight, *float*
- `weights/obs` : Obstacle distance weight, *float*

# Converting the Costmap to a set of Polynomials:

Modified (bending) recursive shadow casting to get useful obstacle boundaries in the correct order.  

```lua
38 outline points.
'@' is the robot.
'.' is empty space.
'=' is obstacle in the map.
'#' is an outline point.
. . . . . . . . . . = = = = = = = = = .
. . . . . . . . . V = = = = = = = = = .
. . . . . . . . . U = = = = = = = = = .
. . . . . . . . . T = = = = = = = = = .
. . . . . . . . . . S R Q = = = = = = .
. . . . . . . . . . . . P O N = = = . .
. . . . . . . . . . . . . . M L = . . .
. . . . . . . . . . . . . . . K J . . .
. . . . . . . . . . . . . . . . . . . .
. . . W X . . . . . . . . . . . . . . .
. . = = Y Z . . . . @ . . . . I = = . .
. . = = = [ . . . . . . . . . H = = = .
. . = = = \ . . . . . . . . F G = = . .
. . = = = ] . . . . . . . . E = = = . .
. . . = ^ . . . . . . . . . . D = . . .
. . . . . . . ` a . . . . . . . . . . .
. . . . . . _ = b . . . . . . . . . . .
. . . . . . = = c . f A B . . . . . . .
. . . . . . . . . . e = C . . . . . . .
. . . . . . . . . . d = = . . . . . . .
```

An example of the path and obstacle boundaries represented as polynomials.

![Path and obstacle poly example](https://user-images.githubusercontent.com/6284428/87867471-96cf1b00-c9aa-11ea-96c9-c6f3652dea62.png)
