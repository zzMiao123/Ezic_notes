# 3.7工作日报

| 内容                         | 时间                |
| ---------------------------- | ------------------- |
| 学会在pc上调包               | 14:00-15:00         |
| 创建一个ROS2包               | 15:00-15:30         |
| 将一个简单ROS2包烧录到板子上 | 15:30-17:00         |
| 再看esp系统构建那一页        | 17:00-18:00         |
| nav2包了解                   | 19:00-21:00         |
| 手机端通信设置               | 21:00-22:00         |
| 机器人项目启发               | 22:00-23:00         |
|                              | 13:50上班/22:50下班 |

Get-ros2 报错：

* 安装依赖项rosdep install --from-paths /opt/ros/foxy/share --ignore-src -y --skip-keys "cyclonedds fastcdr fastrtps rti-connext-dds-5.3.1 urdfdom_headers"

报错colcon找不到

* 根据官网教程重新下载安装https://docs.ros.org/en/foxy/Tutorials/Colcon-Tutorial.html
* 解决

## 使用一个下载下来的包

1. 在dev_ws/src中git clone 某个包的 foxy-devel版本

2. 解决依赖关系(重要)

   ```
   cd ..
   rosdep install -i --from-path src --rosdistro foxy -y
   ```

3. colcon build   (colcon build --packages-select my_package)
4. source install/setup.bash

## 创建一个新包

在工作区/src中

1. ros2 pkg create --build-type ament_cmake --node-name my_node my_package
2. 返回构建colcon build --packages-select my_package
3. . install/setup.bash
4. ros2 run my_package my_node

## 将创建的包烧进芯片中

1. 把包git clone 到components中

2. 改cmakelist 添加：

   idf_component_register(

     SRCS src/my_node.cpp

   INCLUDE_DIRS .

   REQUIRES micro_ros_espidf_component)

​	其他除了cmake_minimum_required都注释掉

3. 创建关于这个包中的函数的.h文件，并在c文件中include它
4. 在main中include这个头文件，调用函数
