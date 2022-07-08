# 1.24 工作日报

| 内容                        | 时间                    |
| --------------------------- | ----------------------- |
| 下载安装ROS for Ubuntu      | 11:30-12:30             |
| 了解MicroROS                | 14:30-15:00             |
| 了解ROS基本概念和操作       | 15:00-17:00             |
| Project background research | 17:00-18:00;19:00-21:00 |
|                             | 11:30上班/20:30下班     |



## 一、ROS melodic for ubuntu 18.04 安装与配置

根据[官方安装教程](http://wiki.ros.org/cn/melodic/Installation/Ubuntu)安装ROS melodic

* 网络连接失败，考虑换下载源：

![image-20220124133156765](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220124133156765.png)

* 找不到命令rosdep，搜索得到需安装python-rosdep

  `sudo apt-get install python-rosdep`

  `sudo rosdep init `

  `rosdep update`



* 如果你在查找和使用ROS软件包方面遇到了问题，请确保已经正确配置了环境。有个好办法可以检查，确保[ROS_ROOT](http://wiki.ros.org/ROS/EnvironmentVariables#ROS_ROOT)和[ROS_PACKAGE_PATH](http://wiki.ros.org/ROS/EnvironmentVariables#ROS_PACKAGE_PATH)这两个[环境变量](http://wiki.ros.org/ROS/EnvironmentVariables)正确设置：

```
	$ printenv | grep ROS
```

控制台输出：

ROS_ETC_DIR=/opt/ros/melodic/etc/ros
ROS_ROOT=/opt/ros/melodic/share/ros
ROS_MASTER_URI=http://localhost:11311
ROS_VERSION=1
ROS_PYTHON_VERSION=2
ROS_PACKAGE_PATH=/opt/ros/melodic/share
ROSLISP_PACKAGE_DIRECTORIES=
ROS_DISTRO=melodic

* 创建catkin空间

```
catkin_make
```

* 要保证工作区被安装脚本正确覆盖，需确定`ROS_PACKAGE_PATH`环境变量包含你当前的工作空间目录：

`source devel/setup.bash`

`echo $ROS_PACKAGE_PATH`



* 为ros提供文件权限

  ```
  sudo chmod 777 -R ~/.ros/
  ```

每次打开新终端需要 `source ~/.bashrc`

`roscore`

只需要有一个roscore在运行就够了



## 二、ROS概念介绍

### 1. 文件系统

介绍ROS文件系统的概念，包括如何使用roscd、rosls和[rospack](http://wiki.ros.org/rospack)命令行工具。

- ROS中大量代码分散在众多的packages和stacks之中.
- 命令工具ls和cd已不能满足检索要求。
- 因此ROS提供了一套自己的命令工具来完成与这些命令相似的工作.



* **Packages**: Package是ROS系统中最底层最基本的组织，里面存放各种文件:库、工具、可执行文件等.

* **Manifest**: 一个package描述xml文件，定义package之间的依赖关系.

* **Stacks**: package的集合，比package这个概念高一级别

* **Stack Manifest**: 作用类似package manifest文件，但是它是针对stack的

<img src="http://images.ncnynl.com/ros/2016/NavigatingTheFilesystemaction=AttachFile&do=get&target=filesystem_layout.png" alt="filesystem_layout.png" style="zoom:70%;" />

| 操作                                       | 命令                            |
| ------------------------------------------ | ------------------------------- |
| 用rospack查找软件包目录                    | `rospack find [package_name]`   |
| 用roscd定位到软件包路径                    | `roscd [package_name]`          |
| 直接进入环境变量 $ROS_ROOT定义的目录.      | `roscd`                         |
| roscd log命令切换到ROS储存日志文件的目录。 | `roscd log`                     |
| 列出指定的package或stack中的文件及目录     | `rosls [locationname[/subdir]]` |
|                                            |                                 |
|                                            |                                 |

**命令roscd及其它的ROS命令仅适用于环境变量$ROS_PACKAGE_PATH中存在的目录路径.**



### 2. 建立和编译ROS程序

- 一个程序包要想称为catkin程序包必须符合以下要求：
  - 该程序包必须包含catkin compliant package.xml文件，这个package.xml文件提供有关程序包的元信息。
  - 程序包必须包含一个catkin 版本的CMakeLists.txt文件，而Catkin metapackages中必须包含一个对CMakeList.txt文件的引用。
  - 每个目录下只能有一个程序包。这意味着在同一个目录下不能有嵌套的或者多个程序包存在。

| 操作                         | 命令                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| 创建工作空间                 | 在新建的目录`catkin_make`                                    |
| 保证工作区被安装脚本正确覆盖 | `source devel/setup.bash`                                    |
| 进入源码目录                 |                                                              |
| 创建新程序包                 | catkin_create_pkg <package_name> [depend1] [depend2] [depend3] |
|                              | 在工作目录内`catkin_make`                                    |



### 3. 节点

**图概念概述**

- Nodes:节点,一个节点即为一个可执行文件，它可以通过ROS与其它节点进行通信。
- Messages:消息，消息是一种ROS数据类型，用于订阅或发布到一个话题。
- Topics:话题,节点可以发布消息到话题，也可以订阅话题以接收消息。
- Master:节点管理器，ROS名称服务 (比如帮助节点找到彼此)。
- rosout: ROS中相当于stdout/stderr。
- roscore: 主机+ rosout + 参数服务器 (参数服务器会在后面介绍)。

**客户端库**

- ROS客户端库允许使用不同编程语言编写的节点之间互相通信:
  - rospy = python 客户端库
  - roscpp = c++ 客户端库
  - rosjs = javascripts客户端库
  - rosjava = java客户端库

| 操作                              | 命令                              |
| --------------------------------- | --------------------------------- |
| 运行所有ROS程序前首先要运行的命令 | roscore                           |
| 列出活跃的节点                    | rosnode list                      |
| 关于一个特定节点的信息            | rosnode info `/rosout`            |
| 使用包名直接运行一个包内的节点    | rosrun [package_name] [node_name] |
|                                   |                                   |

### 4. 话题

**ROS Topics**

- turtlesim_node节点和turtle_teleop_key节点之间是通过一个ROS话题来互相通信的。
- turtle_teleop_key在一个话题上发布按键输入消息，而turtlesim则订阅该话题以接收该消息。
- 下面让我们使用rqt_graph来显示当前运行的节点和话题。
- 注意：如果你使用的是electric或更早期的版本，那么rqt是不可用的，请使用rxgraph代替。

**使用 rqt_graph**

- rqt_graph能够创建一个显示当前系统运行情况的动态图形。
- rqt_graph是rqt程序包中的一部分。

`rosrun rqt_graph rqt_graph`

**rostopic介绍**

- rostopic命令工具能让你获取有关ROS话题的信息。

* **rostopic pub**可以把数据发布到当前某个正在广播的话题上。

| 操作                         | 命令                                    |
| ---------------------------- | --------------------------------------- |
| 查看消息                     | rostopic echo /turtle1/command_velocity |
| 列出所有当前订阅和发布的话题 | rostopic list                           |
| 查看所发布话题的消息类型     | rostopic type [topic]                   |
| 查看数据发布的频率           | rostopic hz /turtle1/pose               |

```
$ rostopic pub -1 /turtle1/command_velocity turtlesim/Velocity  -- 2.0  1.8
```

去单个破折号）这个参数选项使rostopic发布一条消息后马上退出。

**绘制话题**

```
$ rosrun rqt_plot rqt_plot
```



 ### 5. 服务和参数

**ROS Services**

- 服务（services）是节点之间通讯的另一种方式。
- 服务允许节点发送请求（request） 并获得一个响应（response）

**使用rosservice**

- rosservice可以很轻松的使用 ROS 客户端/服务器框架提供的服务。
- rosservice提供了很多可以在topic上使用的命令，如下所示：
- 使用方法:

```
rosservice list         输出可用服务的信息
rosservice call         调用带参数的服务
rosservice type         输出服务类型
rosservice find         依据类型寻找服务find services by service type
rosservice uri          输出服务的ROSRPC uri 
```

直接在终端调用服务即可

 **rosparam**

- rosparam使得我们能够存储并操作ROS参数服务器（Parameter Server）上的数据。
- 参数服务器能够存储整型、浮点、布尔、字符串、字典和列表等数据类型。（类似matlab工作区）

### 6. 常用工具

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125151654028.png" alt="image-20220125151654028" style="zoom:50%;" />

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125151746231.png" alt="image-20220125151746231" style="zoom:50%;" />

<img src="https://pic3.zhimg.com/80/v2-8644223a16426700b94d158a67c1bbd6_720w.jpg" alt="img" style="zoom:50%;" />

<img src="https://www.theconstructsim.com/wp-content/uploads/2018/03/ROS-QA-108-Visualize-inflation-layer-in-RViz-screen.png" alt="img" style="zoom:50%;" />

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125152045586.png" alt="image-20220125152045586" style="zoom:50%;" />

<img src="http://wiki.ros.org/rqt?action=AttachFile&do=get&target=ros_gui.png" alt="alt Screen shot of ROS GUI" style="zoom: 25%;" />

| 工具      | 功能                     | Micro ROS |
| --------- | ------------------------ | --------- |
| Gazebo    | 仿真                     | No        |
| Webots    | 仿真                     | (ROS2)    |
| RViz      | 传感器数据可视化(上位机) | yes       |
| Crazyfile | 飞控系统(下位机)         | yes       |
| Moveit2   | 机械臂控制               | yes       |
| rqt       | 传感器显示               | yes       |



## 二、MicroRos

**微型ROS是一组分层的库，它们可以直接重用ROS 2的库，也可以使其适应资源受限设备的功能和需求。**

目前，micro-ROS 只支持 ROS 2 API 的一部分(发布、订阅、类型支持和等待集目前都支持)

![img](http://p5.itc.cn/q_70/images03/20200916/046d8402c1c64f01b417bde1f6f07bbf.png)

1: **先从ROS基础进行原型开发，开发成熟之后再考虑代码移植**（ROS->ROS2）或者平台移植（Linux->Windows）

2: 如果已经在 ROS1 中为一个或多个机器人建立了代码库，或者对于一个拥有数十个开发人员的完整组织，则**切换到 ROS2 可能代表大量工作**。





## 三、ROS2



[ROS到ROS2(Micro-ROS)的迁移指南](https://docs.ros.org/en/foxy/Contributing/Migration-Guide.html)



