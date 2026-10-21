# UR5 Gesture-Controlled Pick-and-Place — MediaPipe + ROS 2

[![ROS 2 Humble](https://img.shields.io/badge/ROS%202-Humble-22314E?logo=ros)](https://docs.ros.org/en/humble/)
[![MediaPipe](https://img.shields.io/badge/MediaPipe-Holistic-0097A7)](https://developers.google.com/mediapipe)
[![Gazebo](https://img.shields.io/badge/Gazebo-Fortress-F58113)](https://gazebosim.org/docs/fortress)

Control a UR5 arm with a Robotiq 2F-85 gripper by moving your own arm in front of a webcam. MediaPipe tracks your shoulder, elbow and wrist and maps them to the UR5's joints; closing your fingers closes the gripper. Use it to pick a box from the table and place it elsewhere in Gazebo — no keyboard, no joystick.

## How it works

```
Webcam → hand_detection (MediaPipe Holistic)
           ├─ /arm_joints       6 joint angles from shoulder–elbow–wrist pose
           └─ /gripper_command  finger closure score (0 = open, 1 = closed)
                    ▼
         ur5_hand_controller → joint limits + smoothing → arm and gripper trajectory controllers → Gazebo
```

## Key features

- Body-pose to joint-space mapping with joint-limit clamping and smooth incremental trajectories (no hard jumps)
- Gripper hysteresis: close above 0.6, open below 0.4, so it doesn't chatter
- Annotated debug image on `/hand_image` and a status topic for diagnostics
- UR5 + Robotiq URDF with ros2_control, table, boxes and bin in a Gazebo world

## Software stack

ROS 2 Humble · ros2_control · Gazebo Fortress (`ros_gz`) · MediaPipe · OpenCV · Python

## Run

```bash
mkdir -p ~/ur5_ws/src && cd ~/ur5_ws/src
git clone https://github.com/MechaMind-Labs/ur5-gesture-pick-and-place.git .
cd ~/ur5_ws && rosdep install --from-paths src --ignore-src -r -y
pip install mediapipe opencv-python
colcon build --symlink-install && source install/setup.bash

ros2 launch ur5_bringup simulation.launch.py   # Gazebo + controllers + hand detection + controller node
```

## Related

- [SCARA conveyor cell](https://github.com/Curious-Utkarsh/scara-pick-and-place) — two-hand MediaPipe teleop on a real arm
- [Gesture Controlled Robot](https://github.com/MechaMind-Labs/microros-gesture-robot) — gesture control with an IMU and micro-ROS
- [Earlier UR5 work: ArUco + MoveIt pick-and-place, an e-Yantra competition task (video)](https://youtu.be/_vO71pkmfY4)
- [Franka colour sorting](https://github.com/MechaMind-Labs/franka-color-sorting-robot) — autonomous vision pick-and-place
- [Dual-Panda tic-tac-toe](https://github.com/MechaMind-Labs/dual-franka-tic-tac-toe) — two arms taking turns with MoveIt 2

---
[MechaMind Labs](https://www.mechamindlabs.com/) · [YouTube](https://www.youtube.com/@MechaMindLabs) · [LinkedIn](https://www.linkedin.com/company/mechamindlabs/)
