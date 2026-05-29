# SLAM and location with TurtleBot3 Burger

---

<table>
  <tr>
    <td width="36%" align="center" valign="middle">
     <img src=".assets/image.png" alt="University of San Andres logo" width="320" />
    </td>
    <td width="64%" valign="top">
      <h2>Final Project: Autonomous Robotics - SLAM and Localization</h2>
      <p><strong>Course:</strong> Principles of Autonomous Robotics</p>
      <p><strong>Institution:</strong> University of San Andres, Argentina</p>
      <p><strong>Program:</strong> Artificial Intelligence Engineering</p>
      <p><strong>Academic Year:</strong> 4th year</p>
    </td>
  </tr>
</table>

---

## Overview

Final autonomous robotics project that implements two key components in robotic navigation:

- **Part A - SLAM (Simultaneous Localization and Mapping):** simultaneous mapping and localization of a TurtleBot3 in a maze environment using `slam_toolbox`, teleoperation, and RViz2 visualization.
- **Part B - AMCL (Adaptive Monte Carlo Localization):** probabilistic localization with multiple pose hypotheses, particle filtering, and sensor correction (laser, odom).

Both parts use **ROS2** as the communication middleware, **Gazebo** for simulation, and **RViz2** for real-time data visualization.

## Requirements

### ROS2 Environment
- **ROS2 Humble** or newer
- **Colcon** (ROS2 build tool)
- **Gazebo** (simulator)
- **RViz2** (visualization tool)

### Python Dependencies
```
rclpy
launch_ros
slam_toolbox
rviz2
teleop_twist_keyboard
turtlebot3_gazebo
nav2_common
gazebo_ros
tf_transformations
numpy
scipy
```

### Hardware / Simulation
- **TurtleBot3 Burger** (in simulation with Gazebo)

## Project Structure

```text
SLAM_and_location_with_TurtleBot3_Burger/
├── README.md                                      # This file
├── LICENSE                                        # Apache 2.0 license
├── instructions.txt                               # Execution instructions
├── python_slam_node_aux.py                        # Auxiliary Python node
├── instructions/                                  # Project assignment descriptions
├── report/                                        # Academic report
└── src/
    ├── turtlebot3_slam_mapper/                    # ROS2 package - Part A (SLAM)
    │   ├── turtlebot3_slam_mapper/                # Python module
    │   ├── launch/                                # Launch files
    │   ├── rviz/                                  # RViz2 configurations
    │   ├── config/                                # Parameters and configuration
    │   ├── package.xml                            # Package definition
    │   └── setup.py, setup.cfg                    # Installation configuration
    │
    └── my_py_amcl/                                # ROS2 package - Part B (AMCL)
        ├── my_py_amcl/                            # Python module
        ├── launch/                                # Launch files
        ├── rviz/                                  # RViz2 configurations
        ├── config/                                # Localization parameters
        ├── param/                                 # Node parameters
        ├── maps/                                  # Pre-generated maps
        ├── package.xml                            # Package definition
        └── setup.py, setup.cfg                    # Installation configuration
```

## Installation and Setup

### 1. Clone the repository
```bash
cd ~/colcon_ws/src
git clone <repository-url> tp_final_robotica
cd ~/colcon_ws
```

### 2. Install dependencies
```bash
rosdep install --from-paths src --ignore-src -r -y
```

### 3. Build the workspace
```bash
colcon build
source install/setup.bash
```

## Usage

### Part A - SLAM (Mapping and Localization)

#### Terminal 1: Launch SLAM simulation
```bash
cd ~/colcon_ws
source install/setup.bash
colcon build --packages-select turtlebot3_slam_mapper
source install/setup.bash

export TURTLEBOT3_MODEL=burger
ros2 launch turtlebot3_slam_mapper python_slam_maze.launch.py
```

This opens:
- **Gazebo** with a TurtleBot3 Burger in a maze
- **RViz2** with the map under construction and lidar point clouds
- **SLAM Toolbox** node building the map in real time

#### Terminal 2: Teleoperation
```bash
export TURTLEBOT3_MODEL=burger
ros2 run turtlebot3_teleop teleop_keyboard
```

**Controls:**
- `W / X`: accelerate/decelerate forward motion
- `A / D`: turn left/right
- `S`: stop
- `Space`: reset velocities

#### Save the map
Once the robot has explored the maze, save the map:
```bash
ros2 run nav2_map_server map_saver_cli -f ~/map
```

This generates:
- `~/map.pgm` (map image)
- `~/map.yaml` (map metadata)

---

### Part B - AMCL (Particle Localization)

#### Terminal 1: Build and launch AMCL
```bash
cd ~/colcon_ws
source install/setup.bash
colcon build --packages-select my_py_amcl
source install/setup.bash

export TURTLEBOT3_MODEL=burger
ros2 launch my_py_amcl <launch_file>
```

Typically:
```bash
ros2 launch my_py_amcl amcl_gazebo.launch.py
```

#### Terminal 2: Teleoperation
```bash
export TURTLEBOT3_MODEL=burger
ros2 run turtlebot3_teleop teleop_keyboard
```

In RViz2 you will see:
- The robot's real position in Gazebo
- The particle cloud (localization hypotheses)
- Particles converging toward the correct position

---

## Key Concepts

### SLAM (Simultaneous Localization and Mapping)
- Incremental construction of a map while estimating the robot's localization.
- In this project, **SLAM Toolbox** uses lidar data (range and angle) to create a 2D map.
- The robot's position is continuously estimated as new topological information is added.

### AMCL (Adaptive Monte Carlo Localization)
- Probabilistic localization based on particle filtering.
- Requires an **a priori** map (generated in Part A).
- Estimates the robot pose by comparing current lidar readings with the known map.
- Dynamically adapts the number of particles according to confidence.

### ROS2 Components Used
- **rclpy:** Python client library for ROS2
- **tf2_ros:** spatial transformations (frame graph)
- **sensor_msgs:** sensor messages (LaserScan, PointCloud)
- **nav_msgs:** navigation messages (OccupancyGrid, Path)
- **geometry_msgs:** geometry messages (Twist, Pose, PoseArray)
- **visualization_msgs:** markers for visualization in RViz2

---

## Technologies and Tools

| Category | Tool / Library |
|-----------|----------------|
| **Robotic Middleware** | ROS2 Humble, rclpy |
| **Algorithms** | SLAM Toolbox, AMCL (Monte Carlo Localization) |
| **Simulation** | Gazebo, turtlebot3_gazebo |
| **Visualization** | RViz2 |
| **Language** | Python 3 |
| **Scientific Computing** | NumPy, SciPy |
| **Build System** | Colcon, Ament (Python) |
| **Control** | teleop_twist_keyboard |
| **Navigation** | nav2_common, nav2_map_server |
| **Transformations** | tf2_ros, tf_transformations |

---

## Expected Output

### Part A - SLAM
- **Gazebo:** TurtleBot3 simulation in a maze environment
- **RViz2:** 2D map built in real time, lidar readings, robot trajectory
- **Console:** SLAM Toolbox logs showing scan counts and graph optimization

### Part B - AMCL
- **Gazebo:** robot in the known maze
- **RViz2:** particle cloud, initially spread out, converges to the robot's real position
- **Console:** AMCL logs showing effective particle count and likelihood

---

## Troubleshooting

### Error: `Could not load display plugin ...`
```bash
sudo apt-get install ros-humble-rviz2
```

### Error: `slam_toolbox not found`
```bash
sudo apt-get install ros-humble-slam-toolbox
```

### Error: `turtlebot3_gazebo not found`
```bash
sudo apt-get install ros-humble-turtlebot3-gazebo
```

### The map is not generated or updates slowly
- Verify that the lidar is publishing data on `/scan`
- Increase the robot speed or reduce the simulation speed in Gazebo
- Adjust the SLAM parameters in `config/`

### The particles do not converge in AMCL
- Increase `population_per_meter` in the AMCL parameters
- Verify that the loaded map (`maps/`) matches the Gazebo simulation
- Make sure the lidar is properly calibrated (noise, range)

---

## Academic References

- **SLAM Toolbox:** https://github.com/StanleyInnovation/slam_toolbox
- **Nav2 AMCL:** https://navigation.ros.org/configuration/packages/nav2_amcl/amcl/index.html
- **ROS2 Official Documentation:** https://docs.ros.org
- **TurtleBot3 Manual:** https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/

---

## Authors

- **Mateo Giacometti**
- **Tiziano Levi Martin Bernal**

## License

This project is distributed under the **Apache License 2.0**. See the `LICENSE` file for details.

## Contact

For questions or suggestions, open an issue in the repository.
