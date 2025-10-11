# ldlidar_ros2

## Overview

> This SDK applies only to LiDAR products manufactured by **Shenzhen LDROBOT Co., LTD**.
> Supported product models:
>
> - **LDROBOT LiDAR LD06** (FIPATECH version)
> - **LDROBOT LiDAR LD19**
> - **LDROBOT LiDAR STL-27L**

---

## Step 0: Get the LiDAR ROS 2 package

```bash
cd ~
mkdir -p ldlidar_ros2_ws/src
cd ldlidar_ros2_ws/src

git clone https://github.com/FIPATECH/ldlidar_ros2.git
```

---

## Step 1: System setup

1. **Connect the LiDAR** to your system motherboard through:

   - an onboard serial port, or
   - a USB-to-serial converter (e.g. **CP2102** module).

2. **Set execution permission** for the serial device (example: `/dev/ttyUSB0`):

   ```bash
   cd ~/ldlidar_ros2_ws
   sudo chmod 777 /dev/ttyUSB0
   ```

3. **Edit the launch file** for your specific model and update the serial port path, for example in `launch/ld06.launch.py`:

   ```python
   {'port_name': '/dev/ttyUSB0'}
   ```

---

## Example launch file (`ld06.launch.py`)

```python
#!/usr/bin/env python3
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    # LDROBOT LiDAR publisher node
    ldlidar_node = Node(
        package='ldlidar_ros2',
        executable='ldlidar_stl_ros2_node',
        name='LD06',
        output='screen',
        parameters=[
            {'product_name': 'LDLiDAR_LD06'},
            {'topic_name': 'scan'},
            {'frame_id': 'base_laser'},
            {'port_name': '/dev/ttyUSB0'},
            {'port_baudrate': 230400},
            {'laser_scan_dir': True},
            {'enable_angle_crop_func': False},
            {'angle_crop_min': 135.0},
            {'angle_crop_max': 225.0}
        ]
    )

    # base_link to base_laser static transform
    base_link_to_laser_tf_node = Node(
        package='tf2_ros',
        executable='static_transform_publisher',
        name='base_link_to_base_laser_ld06',
        arguments=['0', '0', '0.18', '0', '0', '0', 'base_link', 'base_laser']
    )

    ld = LaunchDescription()
    ld.add_action(ldlidar_node)
    ld.add_action(base_link_to_laser_tf_node)

    return ld
```

---

## Step 2: Build

```bash
cd ~/ldlidar_ros2_ws
colcon build
```

---

## Step 3: Run

### 3.1 — Source environment variables

After compilation, source the environment setup file:

```bash
cd ~/ldlidar_ros2_ws
source install/setup.bash
```

To make this permanent, add it to your `.bashrc`:

```bash
echo "source ~/ldlidar_ros2_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

---

### 3.2 — Start the LiDAR node

#### LD06

```bash
ros2 launch ldlidar_ros2 ld06.launch.py
```

or with visualization:

```bash
ros2 launch ldlidar_ros2 viewer_ld06.launch.py
```

#### LD19

```bash
ros2 launch ldlidar_ros2 ld19.launch.py
```

or with visualization:

```bash
ros2 launch ldlidar_ros2 viewer_ld19.launch.py
```

#### STL-27L

```bash
ros2 launch ldlidar_ros2 stl27l.launch.py
```

or with visualization:

```bash
ros2 launch ldlidar_ros2 viewer_stl27l.launch.py
```

---

## Step 4: Test and visualize

> The code is compatible with **ROS 2 Foxy and later versions (tested under Humble)**.
> Visualization is supported via **Rviz2**.

To visualize the LiDAR output:

```bash
rviz2
```

Then open the `ldlidar.rviz` configuration file located in the `rviz2/` directory.
