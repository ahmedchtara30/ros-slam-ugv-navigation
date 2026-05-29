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

![GPS vs Fast-LIO2 Comparison](https://raw.githubusercontent.com/ahmedchtara30/ros-slam-ugv-navigation/main/gps-vs-slam-comparison.png)
*Left: Satellite view of Enova Robotics test site (Novation City, Sousse). Right: Fast-LIO2 LiDAR point cloud map generated in real-time. The SLAM map accurately reconstructs building geometry, roads, and obstacles where GPS signal is degraded or absent.*

| Metric | Standard GPS | Fast-LIO2 SLAM |
|--------|-------------|----------------|
| **Urban Canyon Reliability** | ❌ Frequent dropout/multipath | ✅ Continuous tracking |
| **Positional Accuracy** | ~2–5m error | ~0.28m error |
| **Map Generation** | None | ✅ Real-time 3D point cloud |
| **Loop Closure** | N/A | ✅ Drift correction enabled |
| **CPU Load** | N/A | 52% (optimized for real-time) |

### Algorithm Performance Comparison

| Algorithm | Localization Error | Map Consistency | CPU Load | Real-time Feasibility |
|-----------|-------------------|-----------------|----------|----------------------|
| **LIO-SAM** | ~0.35m | High | 68% | ✅ |
| **Fast-LIO2** | ~0.28m | Very High | 52% | ✅✅ (Preferred) |

> 🔍 **Key Insight**: The SLAM map aligns with physical infrastructure (walls, roads, parking layout) proving successful sensor fusion and coordinate transformation without external GNSS dependency. Fast-LIO2 achieved superior accuracy with lower computational cost.


## 🛠️ Implementation Phases

### Phase 1: Configuration
- Set up ROS Noetic workspace on Ubuntu 20.04
- Configure P-Guard UGV URDF (robot description)
- Calibrate LiDAR-IMU extrinsics
- Set up Gazebo simulation environment

### Phase 2: Mapping Tests
- Deploy Fast-LIO2 for real-time map building
- Test LIO-SAM for loop closure validation
- Validate map consistency across multiple runs
- Optimize parameters for outdoor environments

### Phase 3: Autonomous Navigation
- Integrate `move_base` for path planning
- Configure global/local planners (NavFn, DWA)
- Test obstacle avoidance with dynamic obstacles
- Validate GPS→SLAM transition in real-time

---

## 💡 Lessons Learned

1. **Sensor Calibration is 80% of Success**  
   Precise LiDAR-IMU extrinsic calibration is critical. Even 1° misalignment causes significant drift.

2. **EKF Transition Logic Must Be Robust**  
   GPS dropout detection requires hysteresis to avoid rapid mode switching (chattering).

3. **ikd-Tree Dramatically Reduces Latency**  
   Fast-LIO2's incremental k-d tree enables real-time performance on embedded hardware.

4. **Loop Closure Requires Careful Tuning**  
   Too aggressive → false positives and map corruption. Too conservative → accumulated drift.

5. **Urban Environments Are Challenging**  
   Glass buildings, reflective surfaces, and dynamic objects require robust outlier rejection.

---

## 📁 Project Structure
