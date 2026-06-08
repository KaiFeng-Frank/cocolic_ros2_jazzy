# Validation Provenance

This standalone repository was extracted from the Coco-LIC ROS2 port developed
inside `gaussian_lic_ros2`.

The source port was validated there against an upstream Coco-LIC-generated
FAST-LIVO2 `CBD_Building_01` reference trajectory. The archived GL2 workspace
reported cm-scale parity for the faithful LIO/LICO frontend path and used
hash-locked evidence gates to prevent trajectory drift.

Large inputs and outputs are intentionally not committed in this standalone
repository:

- rosbag2 / MCAP datasets
- generated TUM trajectories
- point clouds and maps
- hash-locked release reports from the larger GL2 workspace

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
