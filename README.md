# 🤖 Outdoor SLAM — Autonomous UGV Navigation

> 🎓 PFE Project | Enova Robotics & Tunisian Air Force (2022)  
> 👨‍ Ahmed Chtara | Intelligent Systems & AI Engineer  
> 📍 Novation City, Sousse, Tunisia

## 🎥 Live Demo
*[YouTube video will be embedded here — link pending]*  
*Autonomous navigation demo: Fast-LIO2 + LIO-SAM on P-Guard UGV in GPS-denied environments*

---

## 🎯 Problem Statement

**Why not just use GPS?** In urban environments, GPS fails due to:
- ❌ **Signal blackout** (urban canyons, tunnels)
- ❌ **Multi-path reflections** (buildings cause signal bounce)
- ❌ **Dense vegetation** (tree cover blocks satellites)
- ❌ **Accuracy degradation** (2-5m error unacceptable for precision navigation)

**Goal**: Enable reliable localization and mapping for the P-Guard UGV in GPS-denied outdoor environments using LiDAR-Inertial SLAM.

---

## 🧭 Solution Overview

### System Architecture
| Component | Technology |
|-----------|-----------|
| **Robot Platform** | P-Guard UGV (Enova Robotics) |
| **Sensors** | 3D LiDAR, IMU, Wheel Odometry |
| **SLAM Algorithms** | Fast-LIO2, LIO-SAM (LiDAR-Inertial Odometry) |
| **Sensor Fusion** | Extended Kalman Filter (EKF) for GPS→SLAM transition |
| **Middleware** | ROS Noetic, Gazebo simulation, RViz visualization |
| **OS** | Ubuntu 20.04 |

### Algorithm Comparison
| Algorithm | Key Innovation | Why Chosen |
|-----------|---------------|-----------|
| **Fast-LIO2** | Direct point registration + ikd-Tree mapping | High precision, low drift, real-time performance (52% CPU) |
| **LIO-SAM** | Tightly-coupled LiDAR-inertial optimization | Robust loop closure, factor-graph optimization |

---

## 🔑 Technical Deep Dive

### EKF for GPS→SLAM Transition
```pseudocode
// High-level EKF logic for mode switching
if GPS_signal_quality > threshold:
    state = GPS_MODE
    update_pose_with_GPS()
else:
    state = SLAM_MODE
    update_pose_with_LiDAR_IMU_fusion()
    apply_loop_closure_correction()
```
Why EKF (not standard Kalman Filter)?
GPS data is non-linear → Requires Extended Kalman Filter
EKF handles the transition between coordinate systems (GPS → Local SLAM frame)
Maintains uncertainty estimates for robust switching

## SLAM Pipeline
Data Association: Match LiDAR points to map features
Sensor Fusion: Combine LiDAR + IMU + Odometry
Loop Closure: Detect revisited locations to correct drift
Optimization: Minimize pose graph error (LIO-SAM) / Update ikd-Tree (Fast-LIO2)

## 📊 Results & Real-World Validation
### GPS vs. SLAM Performance Comparison
[https://raw.github.com/ahmedchtara30/ros-slam-ugv-navigation/blob/b4946505cfb358ca90c7c2f48126a5da1dc6228b/gps-vs-slam-comparison.png
](https://raw.githubusercontent.com/ahmedchtara30/ros-slam-ugv-navigation/main/gps-vs-slam-comparison.png)
