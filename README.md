# Coco-LIC ROS2 Jazzy

Unofficial ROS2 Jazzy port of [APRIL-ZJU/Coco-LIC](https://github.com/APRIL-ZJU/Coco-LIC), the continuous-time tightly-coupled LiDAR-Inertial-Camera odometry system.

The upstream Coco-LIC repository is a ROS1 Noetic/catkin package. This repository provides a standalone ROS2 Jazzy workspace so the Coco-LIC odometry port can be built and tested independently.

## Status

- ROS2 distribution: Jazzy only.
- Build system: `ament_cmake` / `colcon`.
- Runtime entry point: `ros2 run cocolic odometry_node <config.yaml>`.
- Included packages:
  - `cocolic`: continuous-time spline odometry, LiDAR/IMU/camera factors, rosbag2 input, ROS2 node wrapper.
  - `gaussian_lic_msgs`: minimal message package required by the ported feedback interfaces.
- License: GPL-3.0-or-later, following upstream Coco-LIC.

This is not an official APRIL-ZJU release.

## Build

```bash
cd cocolic_ros2_jazzy
source /opt/ros/jazzy/setup.bash
rosdep update
rosdep install --from-paths src --ignore-src -r -y
colcon build --symlink-install
source install/setup.bash
```

The port expects system Ceres, Eigen, PCL, OpenCV, yaml-cpp, glog, LZ4, and rosbag2 packages available through Ubuntu 24.04 / ROS2 Jazzy.

## Run

Edit `config/ct_odometry_lio.yaml` or `config/ct_odometry_lico.yaml`:

- `project_path: .` assumes you launch from the repository root.
- `bag_path` must point to a rosbag2 directory with per-point LiDAR timing.
- The FAST-LIVO2 calibration examples live in `config/fastlivo2/`.

Then run:

```bash
source install/setup.bash
ros2 run cocolic odometry_node config/ct_odometry_lio.yaml
```

For LiDAR-Inertial-Camera mode:

```bash
ros2 run cocolic odometry_node config/ct_odometry_lico.yaml
```

## Validation Snapshot

The ROS2 port has been checked against an upstream Coco-LIC-generated
FAST-LIVO2 `CBD_Building_01` reference trajectory. The validation uses yaw
alignment, `0.02 s` timestamp association, and the full `117.15 s` / `33.36 m`
reference path.

| Mode | Matched poses | Coverage | ATE RMSE | Mean error | Max error | Path drift |
|------|---------------|----------|----------|------------|-----------|------------|
| LIO | 1231 / 1231 | 100% | 2.12 cm | 1.93 cm | 5.29 cm | 0.414% |
| LICO | 1231 / 1231 | 100% | 2.27 cm | 2.03 cm | 5.63 cm | 0.397% |
| LICO camera-fixed probe | 1231 / 1231 | 100% | 2.81 cm | 2.37 cm | 8.25 cm | 0.740% |

These are trajectory-validation numbers for the standalone odometry port. The
large bags, TUM trajectories, and long-form reports are not committed to git;
publish them through releases or external artifact storage when needed.

## Validation Notes

This repository keeps source, configs, and CI in git. Large reproducibility artifacts are intentionally not committed here: rosbag2 datasets, generated trajectories, point clouds, maps, and long-form release reports should live in GitHub Releases, external storage, or Git LFS.

The key porting details preserved here are:

- ROS1 `ros::Time` arithmetic replaced with explicit ROS2-compatible timestamp handling.
- rosbag input moved from `rosbag::View` to `rosbag2_cpp` with storage auto-detection.
- Ceres 2.0 local parameterization migrated to Ceres 2.2 manifolds.
- ROS1 publishers/subscribers and launch assumptions replaced with explicit ROS2 node/config surfaces.
- QoS and callback behavior are kept conservative for deterministic odometry replay.

## Repository Layout

```text
src/cocolic/           ROS2 port of Coco-LIC
src/gaussian_lic_msgs/ Minimal shared messages
config/                Example odometry configs
scripts/               Utility scripts, including TUM trajectory comparison
```

## Citation

Please cite the original Coco-LIC paper when using this port:

```bibtex
@article{lang2023coco,
  title={Coco-LIC: continuous-time tightly-coupled LiDAR-inertial-camera odometry using non-uniform B-spline},
  author={Lang, Xiaolei and Chen, Chao and Tang, Kai and Ma, Yukai and Lv, Jiajun and Liu, Yong and Zuo, Xingxing},
  journal={IEEE Robotics and Automation Letters},
  year={2023},
  publisher={IEEE}
}
```
