mbot_bringup
==========================================

ROS2 robot bringup package for Makeblock mbot

![Licence](https://img.shields.io/badge/License-Apache-2.0-blue.svg)

# Quick Start Instructions

If you are familiar with ROS2, here are the quick-and-dirty build instructions.

  ```
  cd $COLCON_WS
  git clone git@github.com:mbot_bringup/mbot_bringup.git src/mbot_bringup
  vcs import src --input src/mbot_bringup/mbot_bringup.<ros-distro>.repos
  rosdep install --ignore-src --from-paths src -y -r
  colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release    # Faster and more efficient build type
  cd ..
  ```
If you end up with missing dependencies, install them using commands from [Setup ROS Workspace](#setup-ros-workspace) section.

# How to use this Package and ROS Introduction

 - [Install and Build](#install-and-build)
   * [Install ROS Humble and Development Tooling](#install-ros-$ros_distro$-and-development-tooling)
   * [Setup ROS Workspace](#setup-ros-workspace)
   * [Configure and Build Workspace](#configure-and-build-workspace)
 - [Running Executables](#running-executables)
   * [Using the Local Workspace](#using-the-local-workspace)
 - [Testing and Linting](#testing-and-linting)
 - [Creating a new ROS2 Package](#creating-a-new-ros2-package)
 - [References](#references)

## Install and Build

### Install ROS Humble and Development Tooling

These instructions assume you are running Ubuntu 20.04:

1. [Install ROS2 Humble](https://index.ros.org/doc/ros2/Installation/Humble/Linux-Install-Debians/).
   You can stop following along with the tutorial after you complete the section titled: [Environment setup](https://index.ros.org/doc/ros2/Installation/Humble/Linux-Install-Debians/#environment-setup).
   Make sure you setup your environment with:
   ```
   source /opt/ros/$ros_distro$/setup.bash
   ```

   > **NOTE:** You may want to add that line to your `~/.bashrc`

   > **NOTE:** There is also a `zsh` version of the setup script.

1. [Install ROS2 Build Tools](https://index.ros.org/doc/ros2/Installation/Humble/Linux-Development-Setup/#install-development-tools-and-ros-tools).
   You do not need to build ROS2 from source.
   Simply install the tooling under the section titled "Install development tools and ROS tools".

1. Install `ccache`:
   ```
   sudo apt install ccache
   ```

1. Setup `colcon mixin` [Reference](https://github.com/colcon/colcon-mixin-repository) for convenience commands.
   ```
   sudo apt install python3-colcon-mixin
   colcon mixin add default https://raw.githubusercontent.com/colcon/colcon-mixin-repository/master/index.yaml
   colcon mixin update default
   ```

### Setup ROS Workspace

1. Create a colcon workspace:
   ```
   export COLCON_WS=~/workspace/ros_ws_foxy
   mkdir -p $COLCON_WS/src
   ```

   > **NOTE:** Feel free to change `~/workspace/ros_ws_foxy` to whatever absolute path you want.

   > **NOTE:** Over time you will probably have multiple ROS workspaces, so it makes sense to them all in a subfolder.
     Also, it is good practice to put the ROS version in the name of the workspace, for different tests you could just add a suffix to the base name `ros_ws_foxy`.

1. Download the required repositories and install package dependencies:
   ```
   cd $COLCON_WS
   git clone git@github.com:mbot_bringup/mbot_bringup.git src/mbot_bringup
   vcs import src --input src/mbot_bringup/mbot_bringup.<ros_distro>.repos
   rosdep install --ignore-src --from-paths src -y -r       # install also is there are unreleased packages
   ```

   Sometimes packages do not list all their dependencies so `rosdep` will not install everything.
   If you are getting missing dependency errors, try manually install the following packages:
   ```
   sudo apt install ros2-foxy-forward_command_controller ros2-foxy-joint_state_broadcaster ros2-foxy-joint_trajectory_controller ros2-foxy-xacro
   ```

### Configure and Build Workspace:
To configure and build workspace execute following commands:
  ```
  cd $COLCON_WS
  colcon build --symlink-install --mixin rel-with-deb-info compile-commands ccache
  ```

## Running Executable

See `README.md` files of the packages for information regarding running executables.

<Add here some concrete data about current repository>

### Using the Local Workspace

To use the local workspace you have to source it by using local setup script:
  ```
  source $COLCON_WS/install/local_setup.bash
  ```
Since there are many errors one unintentionally do with wrong sourcing, please check also [Notes on Sourcing ROS Workspace](#notes-on-sourcing-ros-workspace).

#### Notes on Sourcing ROS Workspace

Sourcing of a workspace appends the binary and resource directories to appropriate environment variables.
It is important that you do not run the build command in the same terminal that you have previously sourced your local workspace.
This can cause dependency resolution issues.
Here is some advice copied from [Official ROS Workspace Tutorial](https://index.ros.org/doc/ros2/Tutorials/Workspace/Creating-A-Workspace/) on this:

Before sourcing the overlay, it is very important that you open a new terminal, separate from the one where you built the workspace.
Sourcing an overlay in the same terminal where you built, or likewise building where an overlay is sourced, may create complex issues.

Sourcing the local_setup of the overlay will only add the packages available in the overlay to your environment.
`setup` sources the overlay as well as the underlay it was created in, allowing you to utilize both workspaces.

So, sourcing your main ROS 2 installation’s setup and then the dev_ws overlay’s local_setup, like you just did, is the same as just sourcing dev_ws’s setup, because that includes the environment of the underlay it was created in.


## Testing and Linting

To test the packages packages built from source, use the following command with [colcon](https://colcon.readthedocs.io/en/released/).
In order to run tests and linters you will have had to already built the workspace.
To run the tests use following commands:
  ```
  cd $COLCON_WS
  colcon test
  colcon test-result
  ```

There are `--mixin` arguments that can be used to control testing with linters, specifically `linters-only` and `linters-skip`.

## Creating a new ROS2 Package

If you need to create a new ros2 package it is helpful to start with the official boilerplate for a ros2 package.
The command `ros2 pkg` can be used to generate the boilerplate details.
For example to create a new ros2 package called `example_package` with a node called `example_node` and library called `example_library` use this command:
  ```
  ros2 pkg create --build-type ament_cmake --node-name example_node --library-name example_library example_package
  ```

## References

Here are some useful references for developing with ROS2:

 - [Official ROS2 Tutorials](https://index.ros.org/doc/ros2/Tutorials/)
   * [Luanchfile](https://index.ros.org/doc/ros2/Tutorials/Launch-Files/Creating-Launch-Files/)
   * [Package](https://index.ros.org/doc/ros2/Tutorials/Creating-Your-First-ROS2-Package/)
   * [Parameters](https://index.ros.org/doc/ros2/Tutorials/Parameters/Understanding-ROS2-Parameters/)
   * [Workspace](https://index.ros.org/doc/ros2/Tutorials/Workspace/Creating-A-Workspace/)
 - [Example ROS packages](https://github.com/ros2/examples)
 - [Colcon Documentation](https://colcon.readthedocs.io/en/released/#)
 - [ROS2 Design Documentation](https://design.ros2.org/)
 - [ROS2 Launch Architecture](https://github.com/ros2/launch/blob/master/launch/doc/source/architecture.rst)


## General details about robot bringup packages

A bringup package holds config und launch files for starting different scenarios with a robot.

The general package structure is the following:

```
<robot_name>_bringup/                              # Launch and config files for starting the robot using ros2_control
├── [CMakeLists.txt]                               # if ament_cmake is used (recommended)
├── package.xml
├── [setup.py]                                     # if amend_python is used
├── [setup.cfg]                                    # if amend_python is used
├── config/
│   ├── <robot_name>_controllers.yaml              # Controllers' configuration for ros2_control
│   ├── <robot_name>_forward_position_publisher.yaml  # Setup test publisher for forward position controller
│   └── <robot_name>_joint_trajectory_publisher.yaml  # Setup test publisher for joint trajectory controller
└── launch/
    ├── <robot_name>.launch.py                     # Robot's default launch file
    ├── test_forward_position_controller.launch.py # Start test publisher for forward position controller
    └── test_joint_trajectory_controller.launch.py # Start test publisher for joint trajectory controller

```

**NOTE**: Most of the following steps are equivalent to the description in [ros2_control_demos](https://github.com/ros-controls/ros2_control_demos) repository.
Consult the repository and [ros2_control documentation](https://ros-controls.github.io/control.ros.org/) for more details.


## Testing the *mock* robot using ros2_control-framework

**ATTENTION**: if the package is not build and sourced do this first

1. Start robot's hardware and load controllers (default configuration starts mock hardware)
   ```
   ros2 launch mbot_bringup mbot_ranger_dashing_raptor.launch.py
   ```

2. Open another terminal and check if your hardware is loaded properly:
   ```
   ros2 control list_hardware_interfaces
   ```
   You should see list of *command*- and *state* interfaces for your hardware.

3. Now you should load controllers as described in [loading controllers](#loading-controllers) section.

4. Check the result as described in the [result](#result) section.


## Testing the real robot using ros2_control-framework

**TBD**


## Loading Controllers

To move the robot you should load and start contorllers.
To get feedback about robot's state `JointStateController` is used.
to send command to the robot `ForwardCommandController` (direct goals) or `JointTrajectoryController` (interpolates trajectory).
The sections below describe their usage.

### Joint State Controller
Joint state Controllers provides output of robot's internal states to `/joint_states` and `/dynamic_joint_states` ROS2-topics.

In a new terminal with sourced ROS2 environment load, configure and start `joint_state_broadcaster`:
  ```
  ros2 control load_start_controller joint_state_broadcaster
  ```
Check if controller is loaded properly:
 ```
 ros2 control list_controllers
 ```
You should get similar response to:
 ```
 joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
 ```

Now you should also see your robot represented correctly in the `rviz2`.

### Using Forward Command Controllers

1. If you want to test hardware with `ForwardCommandController` first load and configure it. Controller types are e.g., "position", "velocity", and depend on configured names in the [`config/mbot_ranger_dashing_raptor_controllers.yaml`](config/mbot_ranger_dashing_raptor_controllers.yaml):
   ```
   ros2 control load_configure_controller forward_<controller_type>_controller
   ```
   Check if controller is loaded properly:
   ```
   ros2 control list_controllers
   ```
   You should get the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   forward_<controller_type>_controller[forward_command_controller/ForwardCommandController] inactive
   ```

2. Now start the controller:
   ```
   ros2 control switch_controllers --start-controllers forward_<controller_type>_controller
   ```
   Check if controllers are activated:
   ```
   ros2 control list_controllers
   ```
   You should get `active` in the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   forward_<controller_type>_controller[forward_command_controller/ForwardCommandController] active
   ```

**NOTE**: You can do this in only one step by using `load_start_controller` verb instead of `load_configure_controller`.

3. Send command to the controller, either:

   a. Manually using ros2 cli interface:
   ```
   ros2 topic pub /forward_<controller_type>_controller/commands std_msgs/msg/Float64MultiArray "data:
   - 0.5
   - 0.5
   - 0.5
   - 0.5
   - 0.5
   - 0.5"
   ```
   b. Or you can start demo node which sends two goals every 5 seconds in a loop (**Only with position controller!**):
   ```
   ros2 launch mbot_bringup test_forward_position_controller.launch.py
   ```

### Using JointTrajectoryController (**Not working yet!**)

1. If a `ForwardCommandController` is started you should stop it first by using:
   ```
   ros2 control switch_controllers --stop-controllers forward_<controller_type>_controller
   ```
   Check if controllers are activated:
   ```
   ros2 control list_controllers
   ```
   You should get `active` in the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   forward_<controller_type>_controller[forward_command_controller/ForwardCommandController] inactive
   ```

2. If you want to test hardware with `JointTrajectoryController` first load, configure and start it:
   ```
   ros2 control load_start_controller joint_trajectory_controller
   ```
   Check if controllers are activated:
   ```
   ros2 control list_controllers
   ```
   You should get `active` in the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   joint_trajectory_controller[joint_trajectory_controller/JointTrajectoryController] active
   ```

3. Send command to the controller using test node:
   ```
   ros2 launch ros2_control_demo_robot test_joint_trajectory_controller.launch.py
   ```

**NOTE**: You can switch contorllers (step 1 and 2) also with one command:
```
ros2 control switch_controllers --stop-controllers forward_<controller_type>_controller --start-controllers joint_trajectory_controller
```


## Result

1. If you echo the `/joint_states` or `/dynamic_joint_states` topics you should changing values when the robot is moving values.
   ```
   ros2 topic echo /joint_states
   ros2 topic echo /dynamic_joint_states
   ```

3. The robot should also move in `rviz2`.


## General details about robot bringup packages

A bringup package holds config und launch files for starting different scenarios with a robot.

The general package structure is the following:

```
<robot_name>_bringup/                              # Launch and config files for starting the robot using ros2_control
├── [CMakeLists.txt]                               # if ament_cmake is used (recommended)
├── package.xml
├── [setup.py]                                     # if amend_python is used
├── [setup.cfg]                                    # if amend_python is used
├── config/
│   ├── <robot_name>_controllers.yaml              # Controllers' configuration for ros2_control
│   ├── <robot_name>_forward_position_publisher.yaml  # Setup test publisher for forward position controller
│   └── <robot_name>_joint_trajectory_publisher.yaml  # Setup test publisher for joint trajectory controller
└── launch/
    ├── <robot_name>.launch.py                     # Robot's default launch file
    ├── test_forward_position_controller.launch.py # Start test publisher for forward position controller
    └── test_joint_trajectory_controller.launch.py # Start test publisher for joint trajectory controller

```

**NOTE**: Most of the following steps are equivalent to the description in [ros2_control_demos](https://github.com/ros-controls/ros2_control_demos) repository.
Consult the repository and [ros2_control documentation](https://ros-controls.github.io/control.ros.org/) for more details.


## Testing the *mock* robot using ros2_control-framework

**ATTENTION**: if the package is not build and sourced do this first

1. Start robot's hardware and load controllers (default configuration starts mock hardware)
   ```
   ros2 launch mbot_bringup mbot_ranger_dashing_raptor.launch.py
   ```

2. Open another terminal and check if your hardware is loaded properly:
   ```
   ros2 control list_hardware_interfaces
   ```
   You should see list of *command*- and *state* interfaces for your hardware.

3. Now you should load controllers as described in [loading controllers](#loading-controllers) section.

4. Check the result as described in the [result](#result) section.


## Testing the real robot using ros2_control-framework

**TBD**


## Loading Controllers

To move the robot you should load and start contorllers.
To get feedback about robot's state `JointStateController` is used.
to send command to the robot `ForwardCommandController` (direct goals) or `JointTrajectoryController` (interpolates trajectory).
The sections below describe their usage.

### Joint State Controller
Joint state Controllers provides output of robot's internal states to `/joint_states` and `/dynamic_joint_states` ROS2-topics.

In a new terminal with sourced ROS2 environment load, configure and start `joint_state_broadcaster`:
  ```
  ros2 control load_start_controller joint_state_broadcaster
  ```
Check if controller is loaded properly:
 ```
 ros2 control list_controllers
 ```
You should get similar response to:
 ```
 joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
 ```

Now you should also see your robot represented correctly in the `rviz2`.

### Using Forward Command Controllers

1. If you want to test hardware with `ForwardCommandController` first load and configure it. Controller types are e.g., "position", "velocity", and depend on configured names in the [`config/mbot_ranger_dashing_raptor_controllers.yaml`](config/mbot_ranger_dashing_raptor_controllers.yaml):
   ```
   ros2 control load_configure_controller forward_<controller_type>_controller
   ```
   Check if controller is loaded properly:
   ```
   ros2 control list_controllers
   ```
   You should get the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   forward_<controller_type>_controller[forward_command_controller/ForwardCommandController] inactive
   ```

2. Now start the controller:
   ```
   ros2 control switch_controllers --start-controllers forward_<controller_type>_controller
   ```
   Check if controllers are activated:
   ```
   ros2 control list_controllers
   ```
   You should get `active` in the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   forward_<controller_type>_controller[forward_command_controller/ForwardCommandController] active
   ```

**NOTE**: You can do this in only one step by using `load_start_controller` verb instead of `load_configure_controller`.

3. Send command to the controller, either:

   a. Manually using ros2 cli interface:
   ```
   ros2 topic pub /forward_<controller_type>_controller/commands std_msgs/msg/Float64MultiArray "data:
   - 0.5
   - 0.5
   - 0.5
   - 0.5
   - 0.5
   - 0.5"
   ```
   b. Or you can start demo node which sends two goals every 5 seconds in a loop (**Only with position controller!**):
   ```
   ros2 launch mbot_bringup test_forward_position_controller.launch.py
   ```

### Using JointTrajectoryController (**Not working yet!**)

1. If a `ForwardCommandController` is started you should stop it first by using:
   ```
   ros2 control switch_controllers --stop-controllers forward_<controller_type>_controller
   ```
   Check if controllers are activated:
   ```
   ros2 control list_controllers
   ```
   You should get `active` in the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   forward_<controller_type>_controller[forward_command_controller/ForwardCommandController] inactive
   ```

2. If you want to test hardware with `JointTrajectoryController` first load, configure and start it:
   ```
   ros2 control load_start_controller joint_trajectory_controller
   ```
   Check if controllers are activated:
   ```
   ros2 control list_controllers
   ```
   You should get `active` in the response:
   ```
   joint_state_broadcaster[joint_state_broadcaster/JointStateController] active
   joint_trajectory_controller[joint_trajectory_controller/JointTrajectoryController] active
   ```

3. Send command to the controller using test node:
   ```
   ros2 launch ros2_control_demo_robot test_joint_trajectory_controller.launch.py
   ```

**NOTE**: You can switch contorllers (step 1 and 2) also with one command:
```
ros2 control switch_controllers --stop-controllers forward_<controller_type>_controller --start-controllers joint_trajectory_controller
```


## Result

1. If you echo the `/joint_states` or `/dynamic_joint_states` topics you should changing values when the robot is moving values.
   ```
   ros2 topic echo /joint_states
   ros2 topic echo /dynamic_joint_states
   ```

3. The robot should also move in `rviz2`.
