# 3.1工作日报

| 内容                          | 时间                    |
| ----------------------------- | ----------------------- |
| 确认ROS配置                   | 10:30-11:00             |
| 熟悉ROS1文件系统              | 11:00-12:00             |
| 熟悉ROS1通信框架              | 14:00-17:00             |
| ROS1创建包、应用通信          | 17:00-18:00;19:00-21:00 |
| 讨论项目方向                  | 21:00-22:00             |
| 查找可以用于机器人运行的ROS包 | 22:00-23:00             |
|                               | 10:20上班/23:20下班     |

## ROS配置

catkin_make报错：找不到empy

* 确认了安装流程，重新创建工作空间

## ROS文件系统

[roscd](http://wiki.ros.org/roscd)与其他 ROS 工具一样，*只会查找*[ROS_PACKAGE_PATH](http://wiki.ros.org/ROS/EnvironmentVariables#ROS_PACKAGE_PATH)中列出的目录中的 ROS 包。

自己编写的ROS包如果没放进去就找不到

roscd

roscd log

rosls

### catkin包组成

对于要被视为 catkin 包的包，它必须满足一些要求：

- 包必须包含[符合 catkin 的 package.xml](http://wiki.ros.org/catkin/package.xml)文件。

  - 该 package.xml 文件提供有关包的元信息。

- 该包必须包含一个[使用 catkin 的 CMakeLists.txt](http://wiki.ros.org/catkin/CMakeLists.txt)。

  - 如果它是[catkin 元包](http://wiki.ros.org/catkin/package.xml#Metapackages)，它必须具有相关的样板 CMakeLists.txt 文件。

- 每个包必须有自己的文件夹

  

  - 这意味着没有嵌套的包，也没有多个包共享同一个目录。

![img](https://www.guyuehome.com//Uploads/Editor/202112/20211218_83755.png)

使用 catkin 包的推荐方法是使用[catkin 工作区](http://wiki.ros.org/catkin/workspaces)，但您也可以独立构建 catkin 包。一个简单的工作区可能如下所示：

```
workspace_folder/ -- 工作空间
  src/ -- 源空间
    CMakeLists.txt -- 'Toplevel' CMake 文件，由 catkin 提供
    包_1/
      CMakeLists.txt -- package_1 的 CMakeLists.txt 文件
      package.xml -- package_1 的包清单
    ...
    包_n/
      CMakeLists.txt -- package_n 的 CMakeLists.txt 文件
      package.xml -- package_n 的包清单
```

| 操作                     | 命令                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 创建包                   | catkin_create_pkg <package_name> [depend1] [depend2] [depend3] |
| build包                  | 在工作空间级catkin_make                                      |
| 将工作区添加到ROS环境中  | . ~/<workspacename>/devel/setup.bash                         |
| 查看包的依赖（直接依赖） | rospack depends1 <packagename>                               |
| （间接依赖）             | rospack depends <packagename>                                |

## ROS节点

一个节点实际上只不过是 ROS 包中的一个可执行文件。ROS 节点使用 ROS 客户端库与其他节点通信。节点可以发布或订阅主题。节点还可以提供或使用服务。

注意：如果您仍然在列表中看到 /turtlesim，这可能意味着您使用`ctrl-C`停止了终端中的节点而不是关闭窗口，或者您没有按照[网络中的描述定义 $ROS_HOSTNAME 环境变量设置 - 单机配置](http://www.ros.org/wiki/ROS/NetworkSetup#Single_machine_configuration)。您可以尝试使用以下命令清理 rosnode 列表： $ rosnode 清理

| 操作             | 命令                     |
| ---------------- | ------------------------ |
| 重命名节点       | __name:=<yournodename>   |
| 测试节点是否启动 | rosnode ping </nodename> |
| 清理死节点       | rosnode cleanup          |
|                  |                          |

rqt报错：ModuleNotFoundError: No module named 'rosdep2'

* 退出conda即可解决

## ROS 消息

[msg](http://wiki.ros.org/msg)文件是用于指定消息数据结构的简单文本文件。这些文件存储在包的`msg`子目录中。有关这些文件的更多信息，包括类型规范，请参阅[msg 格式](http://wiki.ros.org/msg)。

消息类型使用标准 ROS[命名](http://wiki.ros.org/Names)约定：包的名称 + / + `.msg`文件的名称

| 操作                       | 命令                                                         |
| -------------------------- | ------------------------------------------------------------ |
| 返回主题的消息类型         | rostopic type </topicname>                                   |
| 查看消息类型的组成         | rosmsg show <msgsname>                                       |
| 控制台发送主题             | rostopic pub </topicname> <msgs_type> <args>                 |
|                            | rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]' |
| 直接显示主题的消息类型组成 | rostopic type </topicname> \| rosmsg show                    |

此选项（双破折号）告诉选项解析器以下参数都不是选项。如果您的参数带有前导破折号`-`，例如负数，则需要这样做。

消息名直接写字母

## ROS 主题

主题被[命名为](http://wiki.ros.org/Names)[节点](http://wiki.ros.org/Nodes)交换[消息](http://wiki.ros.org/Messages)的总线。主题具有匿名发布/订阅语义，将信息的生产与消费分离。一般来说，节点不知道他们正在与谁通信。相反，对数据感兴趣的节点*订阅*相关主题；生成数据的节点*发布*到相关主题。一个主题可以有多个发布者和订阅者。

主题旨在用于单向流式通信。需要执行远程过程调用（即接收对请求的响应）的节点应该使用[服务](http://wiki.ros.org/Services)来代替。还有用于维护少量状态的[参数服务器。](http://wiki.ros.org/Parameter Server)

| 操作               | 命令                     |
| ------------------ | ------------------------ |
| 查看话题发布的频率 | rostopic hz </topicname> |
|                    |                          |

节点和主题名前面加/



rosout 是 ROS 中控制台日志报告机制的名称。它可以被认为包括几个组件：

- 用于订阅、记录和重新发布消息的[“rosout”节点。](http://wiki.ros.org/rosout#rosapi)
- [/](http://wiki.ros.org/rosout#rosout_topic) rosout主题
- 用于订阅聚合提要的[/rosout_agg主题](http://wiki.ros.org/rosout#rosout_agg)
- [rosgraph_msgs/Log](http://docs.ros.org/en/api/rosgraph_msgs/html/msg/Log.html)消息类型，它定义了标准字段以及[详细级别](http://wiki.ros.org/Verbosity Levels)。
- [客户端 API](http://wiki.ros.org/rosout#client_apis)，便于轻松使用 rosout 报告机制
- GUI 工具，例如[rqt_console](http://wiki.ros.org/rqt_console)，用于查看控制台日志消息。

## ROS服务

服务是节点可以相互通信的另一种方式。服务允许节点发送**请求**并接收**响应**。

| 操作                     | 内容                                          |
| ------------------------ | --------------------------------------------- |
| 查看服务列表             | rosservice type </servicename>                |
| 命令行调用服务           | rosservice call </servicename> <args>         |
| 查看服务输入输出参数类型 | rosservice type </servicename> \| rossrv show |

```
rosservice list         print information about active services
rosservice call         call the service with the provided args
rosservice type         print service type
rosservice find         find services by service type
rosservice uri          print service ROSRPC uri
```

## ROS参数

| 操作                   | 命令                                      |
| ---------------------- | ----------------------------------------- |
| 设置参数               | rosparam set </paramname> <args>          |
| 读取参数               | rosparam get </paramname>                 |
| 储存参数               | rosparam dump <filename.yaml>             |
| 加载参数到新的名字空间 | rosparam load <filename.yaml> <spacename> |

**改变了参数值，现在我们必须调用清除服务以使参数更改生效**

```
rosparam set            set parameter
rosparam get            get parameter
rosparam load           load parameters from file
rosparam dump           dump parameters to file
rosparam delete         delete parameter
rosparam list           list parameter names
```



## rqt_console 

 ROS 可以使用[rqt_console](http://wiki.ros.org/rqt_console)和[rqt_logger_level](http://wiki.ros.org/rqt_logger_level)进行调试



## roslaunch

![img](https://www.guyuehome.com//Uploads/Editor/202112/20211218_35830.png)

最后一个节点也来自`turturlesim`包，但是是一个不同的可执行文件：`mimic`。该节点以重映射的形式添加了配置细节。

`mimic`的`/input/pose`话题被重映射到`/turtlesim1/turtle1/pose`，而它的`/output/cmd_vel`话题被映射到`/turtlesim2/turtle1/cmd_vel`。这意味着`mimic`将订阅`/turtlesim1/sim`的`pose`话题，并将其重新发布为`/turtlesim2/sim`的速度控制话题。换句话说，`tuertlesim2`会模仿`turtlesim1`的动作。

## rosed

rosed <packagename> <filename>

## 创建msg和srv

- [msg](http://wiki.ros.org/msg)：msg 文件是描述 ROS 消息字段的简单文本文件。它们用于为不同语言的消息生成源代码。
- [srv](http://wiki.ros.org/srv)：一个 srv 文件描述一个服务。它由两部分组成：请求和响应。
- `Header`，头部包含了ROS中常用的时间戳和坐标系信息。你会经常看到 msg 文件的第一行有`Header header`。

需要确保将 msg 文件转换为 C++、Python 和其他语言的源代码：

打开`package.xml`，并确保这两行在其中[且未注释](http://www.htmlhelp.com/reference/wilbur/misc/comment.html)：

```
  <build_depend>message_generation</build_depend>
  <exec_depend>message_runtime</exec_depend>
```

请注意，在构建时，我们需要“message_generation”，而在运行时，我们只需要“message_runtime”。

1. 创建msg或srv文件（srv通常用roscp复制过来）
2. 在xml文件中添加message_generation和message_runtime
3. 在cmakelist中添加find_package message_generation 添加catkin_package CATKIN_DEPENDS message_runtime;添加msg和srv文件到add_message_files和add_service_srv
4. catkin_make

msg 目录中的任何 .msg 文件都将生成用于所有受支持语言的代码

## 动手编写

### 发布者节点

`ros/ros.h`是一个方便的包含，包括使用 ROS 系统最常见的公共部分所需的所有头文件。

ros::init(argc, argv, "talker");这允许 ROS 通过命令行进行名称重新映射——目前并不重要。这也是我们指定节点名称的地方。节点名称在正在运行的系统中必须是唯一的。

> 结构：
>
> #include "ros/ros.h"
>
> #include "std_msgs/String.h"
>
> ...
>
> callback function
>
> main
>
> ​	ros::init(argc, argv, "nodename");
>
> ​	ros::NodeHandle nodehandle;
>
> ​	定义发布者和订阅者
>
> ​	while (ros::ok())
>
> ​		发数据
>
> ​	ros::spin();
>
> ​	return 0;

CmakeLIst对回车敏感