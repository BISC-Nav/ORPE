# OpenVINS (ORPE)

**An Orthogonal Ray Projection-based Visual-Inertial Estimator**

[![ROS 1 Workflow](https://github.com/rpng/open_vins/actions/workflows/build_ros1.yml/badge.svg)](https://github.com/rpng/open_vins/actions/workflows/build_ros1.yml)
[![ROS Free Workflow](https://github.com/rpng/open_vins/actions/workflows/build.yml/badge.svg)](https://github.com/rpng/open_vins/actions/workflows/build.yml)

**Original Authors:** [Patrick Geneva](https://pgeneva.com/), Kevin Eckenhoff, Woosik Lee, Yulin Yang, Guoquan Huang (University of Delaware).
**ORPE Integration:** Implementation of the Orthogonal Ray Projection model for the MSCKF update.

This repository contains **OpenVINS (ORPE)**, a modified version of the [OpenVINS](https://github.com/rpng/open_vins) state-of-the-art filter-based visual-inertial estimator.

### Key Innovations & Methodology

The standard reprojection error in MSCKF-based VIO suffers from high nonlinearity due to perspective division (dividing by depth), particularly when dealing with distant features or large depth uncertainty. This degradation often leads to estimator inconsistency and scale drift.

To address this, **OpenVINS (ORPE)** reformulates the visual measurement model:

* **Tangent Space Formulation:** We define the measurement residual in the 2D tangent space of the observation ray, rather than on the image plane.
* **Depth Independence:** By minimizing the orthogonal distance between the estimated feature and the measurement ray, the residual becomes **linear with respect to the feature position**.
* **Improved Consistency:** This formulation significantly reduces linearization errors, leading to better estimator consistency (lower NEES) and reduced scale drift, especially in large-scale outdoor environments.
* **Efficiency:** The integration maintains the computational efficiency of the original MSCKF update, capable of running in real-time.

---

## Related Publications

If you use this specific implementation (ORPE), please cite our paper:

* **Orthogonal Ray Projection: A Tangent-Space Visual Measurement Model for Robust Visual-Inertial Odometry** (Submitted 2025).

If you use the base OpenVINS functionalities, please cite the original authors:

* **OpenVINS: A Research Platform for Visual-Inertial Estimation**, *P. Geneva, K. Eckenhoff, W. Lee, Y. Yang, and G. Huang*, ICRA 2020. [PDF](https://pgeneva.com/downloads/papers/Geneva2020ICRA.pdf)

---

## 1. Getting Started

### Prerequisites
* **OS:** Ubuntu 20.04 (Focal) or 22.04 (Jammy).
* **ROS:** ROS Noetic (for Ubuntu 20.04) or ROS 2 Humble (for Ubuntu 22.04).
* **OpenCV:** 3.x or 4.x.
* **Eigen:** 3.3.x or higher.

### Building OpenVINS (ORPE)

We recommend using `catkin` (ROS 1) or `colcon` (ROS 2) for building.

**1. Clone the repository:**
```bash
# Create a workspace
mkdir -p catkin_ws/src
cd catkin_ws/src

# Clone this repository (ensure you use the correct directory name)
git clone [https://github.com/XXX/ORPE/OpenVINS-ORPE.git]
```

**2. Build**
```bash
cd ..
catkin build
source devel/setup.bash
```
## 2. Running OpenVINS (ORPE)
The interface for running the estimator remains identical to the standard OpenVINS. The ORPE formulation is applied automatically during the MSCKF measurement update step.
**EuRoC MAV Dataset (Indoor)**
1. Download a sequence (e.g., MH_01_easy) from the EuRoC Dataset.
2. Launch the estimator using the provided launch file:
```bash
# Make sure to set the path to your dataset correctly
roslaunch ov_msckf subscribe.launch config_path:=$(rospack find ov_msckf)/conf/euroc_mav.yaml bag_file:=/PATH/TO/EuRoC/MH_01_easy.bag
```

**RPNG / Large-Scale Outdoor Datasets**
ORPE demonstrates its full potential in outdoor environments with far-range features (e.g., RPNG OpenVINS Dataset sequences neighbor_01, neighbor_02).
1. Download the dataset from the OpenVINS Dataset Page.
2. Run with the outdoor configuration:
```bash
# Example for neighbor_01
roslaunch ov_msckf rpng_subscribe.launch config_path:=$(rospack find ov_msckf)/conf/rpng_outdoor.yaml bag_file:=/PATH/TO/RPNG/neighbor_01.bag
```

## 3. Configuration & Calibration
Calibration: Accurate camera intrinsics are vital for ORPE as the tangent space basis is constructed from the normalized observation rays. Ensure your intrinsics in the YAML config match your sensor perfectly.
