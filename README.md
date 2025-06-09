# tools-OmniNxtSimulator

[OmniNxt](https://hkust-aerial-robotics.github.io/OmniNxt/) simulator based on Isaac Lab

## Version
- Isaac Sim: 4.5.0
- Isaac Lab: 2.0
- ROS2: Humble

## Setup

### Requirements
- Docker
- Nvidia-container-toolkit
- Git-LFS

### Clone the repository with Git-LFS

```bash
# if not installed
sudo apt install git-lfs
# if you have a previous system-wide configuration
sudo git config --system --remove-section filter.lfs
# if you have a previous user-wide configuration
git config --global --remove-section filter.lfs

git clone https://github.com/UAV-Swarm/tools-OmniNxtSimulator.git
cd tools-OmniNxtSimulator
git lfs install
git config lfs.url "https://public:Public123@repo.hkust-uav.org/artifactory/api/lfs/swarm-lfs"

# Pull the models
git lfs pull
```

### Fetch the submodules and build the docker images

```bash
./scripts/init.sh
./scripts/start.sh
```

## Run the simulator

1. Attach to the simulator container via VSCode Remote-Container extension

2. Run the simulator application with GUI or Native Streaming

```bash
# GUI
cd user_apps
./gui.sh 00_single_uav.py

# Streaming
cd user_apps
./stream.sh 00_single_uav.py
```

3. Control the UAVs

Use the ros2 topic to control the UAVs

```bash
# Example to control robot_0
# Position control
ros2 topic pub /robot_0/control_cmd std_msgs/Float32MultiArray "{data: [0, x, y, z, yaw]}"

# Velocity control
ros2 topic pub /robot_0/control_cmd std_msgs/Float32MultiArray "{data: [1, vx, vy, vz, yaw]}"

# Thrust and Angular velocity control
ros2 topic pub /robot_0/control_cmd std_msgs/Float32MultiArray "{data: [2, avx, avy, avz, thrust]}"
```
Data format:

[0] - control mode | 0: position control, 1: velocity control, 2: angular velocity control

[1:4] - x, y, z, yaw | position control

[1:4] - vx, vy, vz, yaw | velocity control

[1:4] - avx, avy, avz, thrust | angular velocity control


## ROS2 data interface

| Topic | Message Type | Description |
| --- | --- | --- |
| /robot_0/odom | nav_msgs/Odometry | Odometry of robot_0 |
| /robot_0/control_cmd | std_msgs/Float32MultiArray | Control command of robot_0 |
| /robot_0/debug_angular_velocity | nav_msgs/Odometry | Debug angular velocity of robot_0 |
| /robot_0/prop_force | std_msgs/Float32MultiArray | Propeller force of robot_0 |
| /robot_0/prop_torque | std_msgs/Float32MultiArray | Propeller torque of robot_0 |

## ROS2 <-> ROS1 communication bridge

This bridge will automatically map the ROS2 topics to ROS1 topics. This is useful for the ROS1 packages that are not yet ported to ROS2.

```bash
cd ros_bridge
docker-compose up -d
```

## Use cases

| Script | Description | Visualization |
| --- | --- | --- |
| apps/00_single_uav.py | Single UAV simulation with position control and ROS2 interface | ![00_single_uav](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/picgo/20250116-014615%402x.png) |
| apps/01_multi_uav.py | Multi-UAV simulation with position control and ROS2 interface | ![01_multi_uav](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/picgo/20250116-015052%402x.png)
|apps/data_apps/00_object_based_sdg.py | Object-based SDG Generatior for 6D pose estimation | ![00_object_based_sdg](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/picgo/000038_overlay.png) |
| apps/data_apps/01_path_tracking_sdg.py | Path tracking SDG Generator for perception tasks | ![01_path_tracking_sdg](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/picgo/20250116-020557.png) |
