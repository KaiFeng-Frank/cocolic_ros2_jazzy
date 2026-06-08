# Validation

This repository keeps validation reproducible without committing large datasets
or generated outputs to git.

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
