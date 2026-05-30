# MoveIt 2 + NVIDIA Isaac Sim Integration (ROS 2 Jazzy)

A complete guide for integrating MoveIt 2 with NVIDIA Isaac Sim using ROS 2 Jazzy and the Franka Panda robot. This repository demonstrates motion planning, trajectory execution, ros2_control integration, controller debugging, and troubleshooting of execution failures.

---

## 📌 Overview

This project provides a working integration between:

- ROS 2 Jazzy
- MoveIt 2
- NVIDIA Isaac Sim
- Franka Panda Manipulator
- ros2_control
- TopicBasedSystem Hardware Interface

The system enables:

- Motion planning in RViz
- Trajectory generation using MoveIt 2
- Real-time execution in Isaac Sim
- Joint state feedback
- Controller-based robot control

---

## 🏗 System Architecture

```text
Isaac Sim
    │
    ▼
/isaac_joint_states
    │
    ▼
Joint State Relay
    │
    ▼
/joint_states
    │
    ▼
MoveIt 2
    │
    ▼
panda_arm_controller
    │
    ▼
topic_based_ros2_control
    │
    ▼
Isaac Sim
```

---

## 🛠 Prerequisites

### Operating System

- Ubuntu 24.04

### Software

- ROS 2 Jazzy
- NVIDIA Isaac Sim
- MoveIt 2
- RViz2

### Hardware

- NVIDIA GPU (recommended)

---

## 📂 Workspace Structure

```text
IsaacSim-ros_workspaces/
└── jazzy_ws/
    ├── src/
    ├── build/
    ├── install/
    └── log/
```

---

## 🚀 Installation

### Source Environment

```bash
source /opt/ros/jazzy/setup.bash
source ~/IsaacSim-ros_workspaces/jazzy_ws/install/setup.bash
```

### Verify Packages

```bash
ros2 pkg list | grep isaac_moveit
```

Expected:

```text
isaac_moveit
```

---

## 🤖 Launch Isaac Sim

1. Open Isaac Sim
2. Load the Franka Panda example
3. Start simulation using **Play**

Verify joint states:

```bash
ros2 topic hz /isaac_joint_states
```

Expected:

```text
~60 Hz
```

---

## 🔧 Joint State Relay

MoveIt expects:

```text
/joint_states
```

Isaac Sim publishes:

```text
/isaac_joint_states
```

Create relay:

```bash
ros2 run topic_tools relay /isaac_joint_states /joint_states
```

Verify:

```bash
ros2 topic hz /joint_states
```

---

## 🎯 Launch MoveIt 2

```bash
source /opt/ros/jazzy/setup.bash
source ~/IsaacSim-ros_workspaces/jazzy_ws/install/setup.bash

ros2 launch isaac_moveit isaac_moveit.launch.py
```

---

## 📋 Controller Verification

Check controllers:

```bash
ros2 control list_controllers
```

Expected:

```text
joint_state_broadcaster   active
panda_arm_controller      active
```

Check trajectory action server:

```bash
ros2 action info /panda_arm_controller/follow_joint_trajectory
```

Expected:

```text
Action servers: 1
```

---

## 🧪 Motion Planning

1. Open RViz
2. Select MotionPlanning panel
3. Drag the interactive marker
4. Click **Plan**

Expected:

- Green trajectory appears
- Planning successful

---

## ▶️ Trajectory Execution

Click:

```text
Plan & Execute
```

Expected:

- MoveIt sends trajectory
- Panda arm moves
- Isaac Sim updates robot pose

---

## 🐞 Troubleshooting

### Problem: Planning Works but Execute Fails

Symptoms:

```text
Plan: Success
Execute: Failed
```

Error:

```text
Action client not connected to action server:
panda_arm_controller/follow_joint_trajectory
```

### Root Cause

The ros2_control hardware interface plugin was not loaded correctly.

Specifically:

```text
topic_based_ros2_control
```

was not built or installed correctly.

As a result:

```text
MoveIt
   ↓
Controller Manager
   ↓
Missing Hardware Plugin
   ↓
No Action Server
   ↓
Execution Failure
```

### Solution

Rebuild:

```bash
colcon build \
  --packages-select topic_based_ros2_control \
  --cmake-args -DBUILD_TESTING=OFF
```

Restart:

```bash
source install/setup.bash
ros2 launch isaac_moveit isaac_moveit.launch.py
```

Verify:

```bash
ros2 control list_controllers
```

Expected:

```text
joint_state_broadcaster active
panda_arm_controller active
```

---

## 📷 Screenshots

### RViz Motion Planning

Add image:

```markdown
![RViz Planning](screenshots/rviz_planning.png)
```

### Successful Execution

```markdown
![Execution](screenshots/execution_success.png)
```

### Isaac Sim Integration

```markdown
![Isaac Sim](screenshots/isaac_sim.png)
```

---

## 📊 Key Lessons Learned

- Planning success does not guarantee execution success.
- MoveIt and ros2_control are separate subsystems.
- Action servers are required for trajectory execution.
- Controller activation is critical.
- Joint state feedback must be available continuously.

---

## 📚 References

- MoveIt 2 Documentation
- ROS 2 Jazzy Documentation
- NVIDIA Isaac Sim Documentation
- ros2_control Documentation

---

## 👨‍💻 Author

**Jeffin G Joseph**

Robotics | ROS 2 | MoveIt 2 | Isaac Sim | Autonomous Systems
