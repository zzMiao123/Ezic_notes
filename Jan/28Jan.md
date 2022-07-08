# 1.28 工作日报

| 内容                       | 时间                    |
| -------------------------- | ----------------------- |
| 安装ROS2                   | 11:30-12:00;13:30-14:30 |
| 安装ROS，解决冲突          | 14:30-15:00             |
| 补充shell知识（20.04 LTS） | 15:00-15:30             |
| 安装MicroROS               | 15:30-17:30             |
| 在MicroROS上构建程序       | 17:30-18:00;19:00-20:30 |
|                            | 11:08上班/20:08下班     |

***

## MicroROS教程

### 1. 基于Linux构建一个应用

* 安装ROS2 Foxy
* 构建系统

```
# Source the ROS 2 installation
source /opt/ros/$ROS_DISTRO/setup.bash

# Create a workspace and download the micro-ROS tools
mkdir microros_ws
cd microros_ws
git clone -b $ROS_DISTRO https://github.com/micro-ROS/micro_ros_setup.git src/micro_ros_setup

# Update dependencies using rosdep
sudo apt update && rosdep update
rosdep install --from-path src --ignore-src -y

# Install pip
sudo apt-get install python3-pip

# Build micro-ROS tools and source them
colcon build
source install/local_setup.bash
```

* 设置环境变量get-mros
* 创建新的固件区

```
# Create firmware step
ros2 run micro_ros_setup create_firmware_ws.sh host
```

* （主机无需配置固件）
* 构建固件（安装客户端和代理）

```
# Build step
ros2 run micro_ros_setup build_firmware.sh
source install/local_setup.bash
```

* 让 micro-ROS 访问 ROS 2 数据空间

```
# Run a micro-ROS agent
ros2 run micro_ros_agent micro_ros_agent udp4 --port 8888
```

* 在另一个命令行中运行 micro-ROS 节点

```
get-ros2
get-mros

# Use RMW Micro XRCE-DDS implementation
export RMW_IMPLEMENTATION=rmw_microxrcedds

# Run a micro-ROS node
ros2 run micro_ros_demos_rclc ping_pong
```

* 报错Error in rcl_publisher_init: Undefined type support, at
  * 要在工作空间内打开终端
  * get-ros2与get-mros顺序不可变
* 测试应用

### 2. 基于ESP32构建一个应用

firmware唯一，Linux版和ESP版不能共存，建议改名

* 创建固件工作区

```
# Create step
ros2 run micro_ros_setup create_firmware_ws.sh freertos esp32
```

* 配置固件

```
# Configure step
ros2 run micro_ros_setup configure_firmware.sh [APP] [OPTIONS]
```

此配置步骤可用的选项包括：

- `--transport`或`-t`: `udp`，`serial`或任何特定于硬件的传输标签
- `--dev`或`-d`：类串行传输中的代理字符串描述符
- `--ip`或`-i`：类网络传输中的代理 IP
- `--port`或`-p`：类网络传输中的代理端口

```
# Configure step with ping_pong app and serial transport
ros2 run micro_ros_setup configure_firmware.sh ping_pong -t serial -d /dev/ttyUSB0
```

* 构建固件

```
# Build step
ros2 run micro_ros_setup build_firmware.sh
```

* 烧录固件（暂不支持esp32s3，用esp32测试）

```
# Flash step
ros2 run micro_ros_setup flash_firmware.sh
```

* 创建并安装mROS代理

```
# Download micro-ROS-Agent packages
ros2 run micro_ros_setup create_agent_ws.sh
# Build step
ros2 run micro_ros_setup build_agent.sh
source install/local_setup.bash
```

* 运行程序

```
ros2 run micro_ros_agent micro_ros_agent serial --dev /dev/ttyUSB0
```

* 收听ping主题时报错

WARNING: topic [/microROS/ping] does not appear to be published yet
Could not determine the type for the passed topic

考虑用idf烧程序->依然找不到话题

### 3. 节点

ROS 2 节点是 ROS 2 生态系统的主要参与者。它们将使用发布者、订阅、服务等在彼此之间进行通信。

* 创建节点（默认配置）

> rcl_allocator_t
>
> rclc_support
>
> rcl_ret_t
>
> rcl_node_t
>
> rc = rclc_node_init_default(&node, node_name, namespace, &support);

```
// Initialize micro-ROS allocator
rcl_allocator_t allocator = rcl_get_default_allocator();

// Initialize support object
rclc_support_t support;
rcl_ret_t rc = rclc_support_init(&support, argc, argv, &allocator);

// Create node object
rcl_node_t node;
const char * node_name = "test_node";

// Node namespace (Can remain empty "")
const char * namespace = "test_namespace";

// Init default node
rc = rclc_node_init_default(&node, node_name, namespace, &support);
if (rc != RCL_RET_OK) {
  ... // Handle error
  return -1;
}
```

* 创建节点（自定义）

```
// Initialize allocator and support objects
...

// Create node object
...

// Node namespace (Can remain empty "")
...

// Get default node options and modify them
rcl_node_options_t node_ops = rcl_node_get_default_options();

// Set node ROS domain ID to 10
node_ops.domain_id = (size_t)(10);

// Init node with custom options
rc = rclc_node_init_with_options(&node, node_name, namespace, &support, &node_ops);

if (rc != RCL_RET_OK) {
  ... // Handle error
  return -1;
}
```

* 销毁节点

该节点拥有的所有实体（发布者、订阅者、服务……）必须在节点本身之前被销毁

```
// Destroy created entities (Example)
rcl_publisher_fini(&publisher, &node);
...

// Destroy the node
rcl_node_fini(&node);
```

* 生命周期

### 4. Publishers & subscribers



