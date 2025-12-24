# ORB-SLAM3 (ORPE)

**An Orthogonal Ray Projection-based Visual-Inertial Odometry System**

**Original Authors:** Carlos Campos, Richard Elvira, Juan J. Gómez Rodríguez, [José M. M. Montiel](http://webdiis.unizar.es/~josemari/), [Juan D. Tardos](http://webdiis.unizar.es/~jdtardos/).

This repository contains the implementation of **ORB-SLAM3 (ORPE)**, a modified version of the state-of-the-art ORB-SLAM3 framework. This version replaces the standard geometric reprojection error with the **Orthogonal Ray Projection Error (ORPE)**.

### Key Innovations & Methodology

The standard reprojection error in VIO suffers from high nonlinearity due to perspective division, particularly under large depth uncertainty. To address this, we introduce:

* **Tangent Space Formulation:** The visual measurement model is formulated in the tangent space of the observation ray. By minimizing the orthogonal distance between the estimated landmark and the measurement ray, we eliminate explicit dependency on scalar depth.
* **Linearity:** ORPE renders the residual function linear with respect to the feature position. This significantly widens the convergence basin for the Levenberg-Marquardt solver.
* **Orthogonal Ray Factor:** We introduce a novel factor in the graph optimization to replace the standard reprojection edge connecting MapPoint and KeyFrame vertices.
* **Robustness:** This method improves trajectory accuracy and estimator consistency in weak-parallax scenarios and large-scale outdoor environments.

<a href="https://youtu.be/HyLNq-98LRo" target="_blank"><img src="https://img.youtube.com/vi/HyLNq-98LRo/0.jpg" 
alt="ORB-SLAM3" width="240" height="180" border="10" /></a>

### Related Publications

If you use this specific implementation (ORPE), please cite our paper:

* **Orthogonal Ray Projection: A Tangent-Space Visual Measurement Model for Robust Visual-Inertial Odometry** (Submitted 2025).

If you use the base ORB-SLAM3 functionalities, please cite the original authors:

* [ORB-SLAM3] Carlos Campos, Richard Elvira, Juan J. Gómez Rodríguez, José M. M. Montiel and Juan D. Tardós, **ORB-SLAM3: An Accurate Open-Source Library for Visual, Visual-Inertial and Multi-Map SLAM**, *IEEE Transactions on Robotics 37(6):1874-1890, Dec. 2021*. **[PDF](https://arxiv.org/abs/2007.11898)**.

# 1. License

ORB-SLAM3 (ORPE) is released under [GPLv3 license](https://github.com/UZ-SLAMLab/ORB_SLAM3/LICENSE).

# 2. Prerequisites

The system has been tested and validated on **Ubuntu 20.04**.

## C++11 or C++0x Compiler
We use the new thread and chrono functionalities of C++11.

## Pangolin
We use [Pangolin](https://github.com/stevenlovegrove/Pangolin) for visualization and user interface. Download and install instructions can be found at: https://github.com/stevenlovegrove/Pangolin.

## OpenCV
We use [OpenCV](http://opencv.org) to manipulate images and features. Download and install instructions can be found at: http://opencv.org. **Required at least 3.0. Tested with OpenCV 4.2.0**.

## Eigen3
Required by g2o. Download and install instructions can be found at: http://eigen.tuxfamily.org. **Required at least 3.1.0**.

## DBoW2 and g2o (Included in Thirdparty folder)
This repository includes modified versions of DBoW2 and g2o. The **g2o** library has been adapted to support the custom **Orthogonal Ray Factor** edges used in the Local Bundle Adjustment.


# 3. Building ORB-SLAM3 (ORPE)

Clone the repository:
```bash
git clone [https://github.com/XXX/ORPE.git]
```
We provide a script build.sh to build the Thirdparty libraries and ORB-SLAM3. Execute:
```bash
cd ORPE/ORB-SLAM3-ORPE
chmod +x build.sh
./build.sh
```
This will create libORB_SLAM3.so at the lib folder and the executables in the Examples folder.

# 4. Running ORB-SLAM3 (ORPE)

This version maintains the same interface as the original ORB-SLAM3 but uses the ORPE formulation internally for optimization.

## EuRoC Examples
The proposed method has been evaluated on the EuRoC MAV Dataset (indoor, room-scale).

1. Download a sequence (ASL format) from http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets
2. Execute the Monocular-Inertial examples. The optimizer will automatically minimize the orthogonal distance.
```bash
# Example for Monocular-Inertial (V1_01_easy)
./Examples/Monocular-Inertial/mono_inertial_euroc ./Vocabulary/ORBvoc.bin ./Examples/Monocular-Inertial/EuRoC.yaml "$pathDatasetEuroc"/V101 ./Examples/Monocular-Inertial/EuRoC_TimeStamps/V101.txt dataset-V101_monoi
```

## Large-Scale Outdoor Examples (RPNG)

ORPE demonstrates significant advantages in large-scale outdoor environments, such as the RPNG OpenVINS Dataset (e.g., sequences neighbor_01, neighbor_02).
In these scenarios, the method effectively mitigates scale drift and improves consistency by decoupling measurement error from feature depth.

1. Download a rosbag from [http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets](https://docs.openvins.com/gs-datasets.html#gs-data-rpng), and tranform into EuRoC dataset format.
2. Execute the Monocular-Inertial examples. The optimizer will automatically minimize the orthogonal distance.
```bash
# Example for Monocular-Inertial (neighbor_01)
./Examples/Monocular-Inertial/mono_inertial_euroc ./Vocabulary/ORBvoc.bin ./Examples/Monocular-Inertial/RPNG.yaml "$pathDatasetRPNG"/neighbor_01 ./Examples/Monocular-Inertial/neighborhood_01.txt dataset-neighbor_01
```

# 5. Calibration
For optimal performance with ORPE, accurate sensor calibration is critical. The system relies on precise intrinsics to construct the tangent space basis from the observation ray. Ensure your YAML configuration files are strictly unified with your sensor's calibration parameters.
