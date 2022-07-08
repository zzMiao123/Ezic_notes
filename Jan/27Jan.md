# 1.27 工作日报
| 内容             | 时间                |
| ---------------- | ------------------- |
| FreeRTOS任务操作 | 10:30-12:00;        |
| FreeRTOS队列     | 14:00-15:30         |
| 激光雷达小车拆解 | 15:30-16:00         |
| FreeRTOS队列     | 16:00-17:00         |
| 尝试部署MicroROS | 17:00-18:30         |
| 构建ROS2         | 19:00-19:30         |
|                  | 10:08上班/19:08下班 |



## 七层网络结构

### 应用层

网络服务与最终用户的一个接口。

协议有：**HTTP FTP TFTP SMTP SNMP DNS TELNET HTTPS POP3 DHCP**

### 表示层

数据的表示、安全、压缩。（在五层模型里面已经合并到了应用层）

格式有，**JPEG、ASCll、EBCDIC、加密格式等** [2] 

### 会话层

建立、管理、终止会话。（在五层模型里面已经合并到了应用层）

**对应主机进程，指本地主机与远程主机正在进行的会话**

### 传输层

定义传输数据的协议端口号，以及流控和差错校验。

协议有：**TCP UDP，数据包一旦离开网卡即进入网络传输层**

### 网络层

进行逻辑地址寻址，实现不同网络之间的路径选择。

协议有：**ICMP IGMP IP（IPV4 IPV6）**

### 数据链路层

建立逻辑连接、进行硬件地址寻址、差错校验 [3] 等功能。（由底层网络定义协议）

将比特组合成字节进而组合成帧，用MAC地址访问介质，错误发现但不能纠正。

### 物理层

建立、维护、断开物理连接。（由底层网络定义协议）

![这里写图片描述](https://img-blog.csdn.net/20170822222325781?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2lsZW5jZU9P/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 部署MicroROS

### idf-component-microros 使用说明

* 安装依赖环境

`get-idf`

`pip3 install catkin_pkg lark-parser empy colcon-common-extensions importlib-resources`

* clone[组件](https://github.com/micro-ROS/micro_ros_espidf_component.git)到esp/esp-idf/component
* 重新get-idf
* 测试示例程序int32_publisher
  * set-target esp32s3报错：工具链不匹配
    * 关闭终端重开（包括vscode里的和shell）
    * rm -rf build/ sdkconfig

### MicroROS教程

在计算机中安装 ROS 2 后，安装 micro-ROS 构建系统

安装ROS Noetic构建系统的工作流程分为四步：

- **创建步骤：**此步骤负责下载特定硬件平台所需的所有代码库和交叉编译工具链。在这些存储库中，它还将下载一组可立即使用的 micro-ROS 应用程序。
  - 修改教程中 stm32固件为esp32 `ros2 run micro_ros_setup create_firmware_ws.sh freertos esp32`

- **配置步骤：**在此步骤中，用户可以选择工具链将要交叉编译的应用程序。在此步骤中还将选择一些其他选项，例如传输、代理的 IP 地址/端口（用于 UDP 传输）或设备 ID（用于串行连接）。
- **构建步骤：**在这里进行交叉编译并生成特定于平台的二进制文件。
- **Flash 步骤：**将上一步生成的二进制文件闪存到硬件平台内存中，以允许执行 micro-ROS 应用程序。有关 micro-ROS 构建系统的更多信息，请参见 [此处](https://github.com/micro-ROS/micro_ros_setup/tree/dashing/micro_ros_setup)。



### 总结：有两种将 micro ros 与 esp32 一起使用的方法：

* **首先是在 microros_ws 中[安装和创建 esp env](https://discourse.ros.org/t/micro-ros-porting-to-esp32/16101)**

  * 在计算机上安装ROS2

  * 建立microros工作目录，下载microros源码

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

  * 编译、烧写固件

  > ros2 run micro_ros_setup create_firmware_ws.sh freertos esp32 
  >
  > ros2 run micro_ros_setup configure_firmware.sh int32_publisher -t udp -i 192.168.1.105 -p 8888 
  >
  > 
  >
  > ros2 run micro_ros_setup build_firmware.sh menuconfig 
  >
  > *# Now go to the micro-ROS Transport Settings → WiFi Configuration menu and fill your WiFi SSID and password. Save your changes, exit the interactive menu, and run:* 
  >
  > ros2 run micro_ros_setup build_firmware.sh 
  >
  > *# Connect your ESP32 to the computer with a micro-USB cable, and run:* 
  >
  > ros2 run micro_ros_setup flash_firmware.sh

* **在 espidf 中使用[micro ros 作为组件](https://github.com/micro-ROS/micro_ros_espidf_component)。**

  * 先不要在shell中构建ROS2环境

  * 安装依赖项

    * >. $IDF_PATH/export.sh
      >pip3 install catkin_pkg lark-parser empy colcon-common-extensions importlib-resources

  * clone[组件](https://github.com/micro-ROS/micro_ros_espidf_component.git)到esp/esp-idf/component

  * 重新get-idf

  * 测试示例程序int32_publisher

    * set-target esp32s3报错：工具链不匹配
      * 关闭终端/vscode重开
      * rm -rf build/ sdkconfig

## 在计算机上安装ROS2

ros2 foxy 版本默认需要ubuntu 20.04支持，因此在 18.04 上无法直接通过 apt 安装，需要从源码去编译。

参考：https://zhuanlan.zhihu.com/p/364745633

* 安装依赖
* 下载源码
* 编译源码
  * 报错：CMake Error at CMakeLists.txt:7 (cmake_minimum_required):
      CMake 3.11 or higher is required.  You are running version 3.10.2
  * 考虑更新CMake版本 现有：cmake version 3.10.2
  * 中止
* 升级系统至20.04LTS，准备重装ROS2
* 升级后python3-lark包安装出错，通过sudo dpkg -i --force-overwrite <filename>后sudo apt-get -f install 可以修复
* 设置临时环境变量source /opt/ros/foxy/setup.bash

## 安装ROS Noetic

为了之后能够运行ROS  的程序，还需安装ROS，并考虑两者共存

参考idf的设置方式，将source脚本命令在/etc/bash.bashrc中保存为get-ros和get-ros2。
