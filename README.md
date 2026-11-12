# KUKA iiwa Trajectory Planning and Inverse Dynamics

A ROS 2 and Gazebo Sim study of end-effector trajectories for a seven-joint KUKA iiwa. The study compares joint-space and operational-space inverse dynamics on four trajectory variants.

[Technical report](Trenti_hw2.pdf)

## Implementation

- The KDL planner was extended with straight-line and circular paths, each with trapezoidal velocity and cubic time profiles.
- Joint torque generation was implemented using joint-space and operational-space inverse dynamics.
- The evaluation recorded both controllers across all four trajectories and used MATLAB to plot the seven commanded torques.

## Run the demos

Use ROS 2 Humble, Gazebo Sim, `ros2_control`, Orocos KDL, and Eigen. See the [bundled iiwa stack documentation](ros2_iiwa/README.md) for its setup and dependencies.

Clone the repository from the `src` directory of the ROS 2 workspace.

```bash
git clone https://github.com/FedericoTr26/RL_Homework-2.git
```

Return to the workspace root to build and source the workspace.

```bash
rosdep install -i --from-path src --rosdistro humble -y
colcon build
source install/setup.bash
```

Source `install/setup.bash` from the workspace root in each new terminal.

### Position control

Launch the robot with the default position controller in the first terminal.

```bash
ros2 launch iiwa_bringup iiwa.launch.py
```

Choose one trajectory command in a second terminal, using the ID and input descriptions in the table below.

```bash
ros2 run ros2_kdl_package ros2_kdl_node
ros2 run ros2_kdl_package ros2_kdl_node 1
ros2 run ros2_kdl_package ros2_kdl_node 2
ros2 run ros2_kdl_package ros2_kdl_node 3
```

### Velocity control

Launch Gazebo with the velocity controller in the first terminal and start the node in a second terminal.

```bash
ros2 launch iiwa_bringup iiwa.launch.py command_interface:="velocity" robot_controller:="velocity_controller"
```

```bash
ros2 run ros2_kdl_package ros2_kdl_node --ros-args -p cmd_interface:=velocity
```

### Effort control

Launch Gazebo with the effort controller in the first terminal and start the node in a second terminal.

```bash
ros2 launch iiwa_bringup iiwa.launch.py command_interface:="effort" robot_controller:="effort_controller"
```

```bash
ros2 run ros2_kdl_package ros2_kdl_node --ros-args -p cmd_interface:=effort
```

For velocity or effort control, place a trajectory ID immediately after the executable name and before `--ros-args` to select a different trajectory. The published source selects operational-space inverse dynamics in effort mode. The joint-space recordings used the alternate compile-time setting, `choice=1`.

To view the commanded joint torques, open the plotting tool and select `/effort_controller/commands/data[0]` through `/effort_controller/commands/data[6]`.

```bash
ros2 run rqt_plot rqt_plot
```

## Trajectories and recordings

| ID | Trajectory | Terminal input | Joint-space video | Operational-space video |
| --- | --- | --- | --- | --- |
| `0` | Straight line, trapezoidal velocity | Acceleration time | [Video](VIDEO_JS/Traj_0.mp4) | [Video](VIDEO_OS/Traj_0.mp4) |
| `1` | Straight line, cubic profile | None | [Video](VIDEO_JS/Traj_1.mp4) | [Video](VIDEO_OS/Traj_1.mp4) |
| `2` | Circle, trapezoidal velocity | Acceleration time, then radius | [Video](VIDEO_JS/Traj_2.mp4) | [Video](VIDEO_OS/Traj_2.mp4) |
| `3` | Circle, cubic profile | Radius | [Video](VIDEO_JS/Traj_3.mp4) | [Video](VIDEO_OS/Traj_3.mp4) |

The original examples suggest an acceleration time of `0.5` s and a radius of `0.2` m when prompted.

## Results and recorded data

The report compares the seven commanded torques for each trajectory in both control spaces. Figures 3.1–3.4 cover joint space, and Figures 4.1–4.4 cover operational space.

Each video has a corresponding rosbag2 recording under [`bag_js/`](bag_js/) or [`bag_os/`](bag_os/), with `my_bag_0` through `my_bag_3` matching the trajectory IDs. Open the recordings with rosbag2 or MATLAB ROS Toolbox and select `/effort_controller/commands`. The included [MATLAB plotting script](plot_bag.m) expects a local bag path. Its horizontal axis represents sample index.

## Attribution

Developed as a group project for Robotics Lab. The project extends the course KDL starter package and uses the bundled iiwa ROS 2 stack.
