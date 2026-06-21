# Validation

This repository keeps validation reproducible without committing large datasets
or generated outputs to git.

## Public Artifact State

The current public repository contains source code, configs, CI, comparison
scripts, and the validation snapshot below. It does not yet attach a GitHub
Release bundle with the raw bag, generated TUM files, or long-form run reports.

For a public reproduction bundle, publish these files outside normal git history
and link the release from this document:

- FAST-LIVO2 `CBD_Building_01` rosbag2/MCAP input with per-point LiDAR timing
- upstream Coco-LIC reference trajectory in TUM format
- ROS2 LIO output trajectory in TUM format
- ROS2 LICO output trajectory in TUM format
- trajectory comparison JSON or text report for each mode

## Trajectory Snapshot

The port has been checked against an upstream Coco-LIC-generated FAST-LIVO2
`CBD_Building_01` reference trajectory. The association window is `0.02 s`, the
alignment mode is yaw-only, and the reference contains `1231` poses over
`117.15 s` / `33.36 m`.

| Mode | Matched poses | Coverage | ATE RMSE | Mean error | Max error | Path drift |
|------|---------------|----------|----------|------------|-----------|------------|
| LIO | 1231 / 1231 | 100% | 2.12 cm | 1.93 cm | 5.29 cm | 0.414% |
| LICO | 1231 / 1231 | 100% | 2.27 cm | 2.03 cm | 5.63 cm | 0.397% |
| LICO camera-fixed probe | 1231 / 1231 | 100% | 2.81 cm | 2.37 cm | 8.25 cm | 0.740% |

These numbers validate the odometry port. They are not mapping, rendering, or
Gaussian-splatting quality metrics.

## Local Smoke Test

```bash
source /opt/ros/jazzy/setup.bash
rosdep install --from-paths src --ignore-src -r -y
colcon build --symlink-install
colcon test --packages-select cocolic gaussian_lic_msgs
```

## Trajectory Comparison

The included `scripts/trajectory_compare.py` compares two TUM trajectories:

```bash
python3 scripts/trajectory_compare.py \
  --baseline /path/to/reference.tum \
  --current /path/to/current.tum \
  --align yaw
```
