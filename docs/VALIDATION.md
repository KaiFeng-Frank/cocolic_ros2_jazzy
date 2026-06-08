# Validation

This repository keeps validation reproducible without committing large datasets
or generated outputs to git.

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

Large inputs and outputs are intentionally not committed in this standalone
repository:

- rosbag2 / MCAP datasets
- generated TUM trajectories
- point clouds and maps
- long-form release reports

For this repository, keep the git history focused on source, configs, and CI.
When publishing large validation artifacts, prefer GitHub Releases, external
storage, or Git LFS.

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
