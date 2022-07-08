# 2.8工作日报

| 内容                           | 时间                |
| ------------------------------ | ------------------- |
| 解决编译环境出现的问题         | 10:00-11:30         |
| 重新构建microros环境           | 11:30-12:00;        |
| 解决mros程序build报错问题      | 14:00-14:30         |
| mros概念拓展                   | 14:30-16:00         |
| 解决烧录出错问题               | 16:00-17:00         |
| 调通单片机与上位机通信(ping通) | 17:00-19:00         |
|                                | 10:02上班/19:03下班 |

***

报错问题解决方案已汇总到codingnotes里

## Conceptions of M-ROS

### 1.客户端库

执行管理

micro-ROS 中的 Executor 的目标是为机器人专家提供实用且易于使用的实时机制，为以下问题提供解决方案：

- 确定性执行
- 实时保证
- 在一个平台上集成实时和非实时功能
- 对 RTOS 和微控制器的特定支持

![img](https://micro.ros.org/img/micro-ROS_architecture.png)

随着 ROS 2 中 DDS（数据分发服务）的引入，消息被缓存在 DDS 中。在 ROS 2 中，引入了 Executor 概念来支持执行管理。在 rcl 层，*等待集*配置有要接收的句柄，然后在第二步中，从 DDS 队列中获取句柄。句柄是 rcl 层中定义的用于计时器、订阅、服务、客户端和保护条件的通用术语。



现代机器人软件架构通常遵循分层方法。具有SLAM、基于视觉的物体识别、运动规划等核心算法的层通常被称为*技能层*或*功能层*。为了执行一项复杂的任务，这些技能由一个或多个称为*执行层和计划层*的上层组织。其他常见名称是*任务和任务层*或*审议层*。在下文中，我们使用了后一个术语。

我们观察到要在审议层处理的三个不同但紧密交织的方面：

1. **任务处理**：实际任务的编排，*直接*、*无错误的*流程。
2. **应急处理**：处理特定于任务的突发事件，例如，预期的重试和失败尝试、障碍、电池电量不足。
3. **系统错误处理**：异常处理，例如传感器/执行器故障。

### 2.中间件

**eProsima Micro XRCE-DDS**是一个开源有线协议，它实现了 OMG DDS for e **X** **tremely** Resource C **onstrained** **Environment**标准( [DDS-XRCE](https://www.omg.org/spec/DDS-XRCE/) )。DDS-XRCE 协议的目的是提供从资源受限的设备对 DDS 全局数据空间的访问。这要归功于**客户端-服务器**架构，其中称为*XRCE 客户端*的低资源设备连接到称为*XRCE 代理*的服务器，该服务器代表其在 DDS 全局数据空间中的客户端运行。

Micro XRCE-DDS 由两个主要元素组成：

- [Micro XRCE-DDS Agent](https://github.com/eProsima/Micro-XRCE-DDS-Agent)：一个**C++11 开箱即用的应用程序**，它实现了 XRCE Agent 功能。
- [Micro XRCE-DDS Client](https://github.com/eProsima/Micro-XRCE-DDS-Client)：一个实现 XRCE 客户端功能的**C99 库。**

此外，Micro XRCE-DDS 还使用了其他两个组件：

- [Micro CDR](https://github.com/eProsima/Micro-CDR)：客户端库中使用的反**序列化引擎。**
- [Micro XRCE-DDS Gen](https://github.com/eProsima/Micro-XRCE-DDS-Gen)：用于从 IDL 源生成*Micro CDR*反序列化功能和客户端应用程序示例的**代码生成器工具。**

主要特点：

* 低资源消耗

该库的[最新](https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/latest)版本的内存消耗不到**75 KB 的闪存**和大约**3 KB 的 RAM**，供完整的发布者和订阅者应用程序处理大约 512 B 的消息大小. 关于内存消耗作为消息大小、实体数和中间件库内部内存管理的函数的更多详细信息，请参阅[Micro XRCE-DDS 内存分析](https://micro.ros.org/docs/concepts/middleware/memo_prof/)部分。

* 多传输支持

最新版本的 Micro XRCE-DDS 支持：**UDP**、**TCP**和自定义**串行**传输协议。

* 多平台支持

XRCE 客户端支持作为嵌入式 RTOS的**FreeRTOS**、**Zephyr**和**NuttX 。**此外，它还可以在**Windows**和**Linux**上运行。另一方面，XRCE 代理支持**Windows**和**Linux**。



[DDS-XRCE和MQTT](https://micro.ros.org/docs/concepts/middleware/IoT/)

|                      |        DDS-XRCE        | MQTT                   |
| :------------------: | :--------------------: | ---------------------- |
|     **架构模式**     |      客户端服务器      | 客户端服务器           |
|     **消息模式**     |       发布-订阅        | 发布-订阅              |
|     **传输协议**     |  UDP/TCP/串行/自定义   | TCP                    |
|       **安全**       |   DTLS/TLS（非规范）   | TLS（非规范）          |
|       **类型**       |        标准 IDL        | 不透明，用户选择       |
|     **服务质量**     |     可靠且尽力而为     | 3 消息传递             |
|     **面向会话**     |          是的          | 是的                   |
|     **最小开销**     |        12 B***         | 5 B* **                |
|     **最大开销**     |        16 B***         | 2^16 乙*               |
| **最大有效载荷大小** |         64 KB          | 256 MB                 |
|       **拓扑**       | 1 到 0、1 到 1、1 到 n | 1 到 0、1 到 1、1 到 n |
|     **发现支持**     |          是的          | 不                     |



## MicroROS Agent

Micro-ROS 的默认实现基于 eProsima 的 Micro XRCE-DDS 中间件，由客户端应用程序组成，这些客户端应用程序通过代理与 ROS 2 世界进行交互。该代理跟踪通过在微控制器端执行的请求创建的实体，并使用它们与 ROS 2 数据空间进行通信。

参考：https://snapcraft.io/micro-ros-agent

* 下载：sudo snap install micro-ros-agent
* sudo snap set micro-ros-agent daemon=true



将 snap 与串行设备一起使用时，需要采取一些步骤才能建立成功的连接：

- 刷新 snap`core`包的本地安装：

  ```
   $ sudo snap refresh core --edge
  ```

- 启用热插拔功能并重新启动`snapd`守护程序：

  ```
   $ sudo snap set core experimental.hotplug=true
   $ sudo systemctl restart snapd
  ```

- 将微控制器插入串口后，执行`snap interface serial-port`命令。您应该看到如下内容：

  ```
     name:    serial-port
     summary: allows accessing a specific serial port
     plugs:
       - micro-ros-agent
     slots:
     - snapd:cp2102cp2109uartbrid (allows accessing a specific serial port)
  ```

- 将您的快照图像连接到所需的串行端口（相应地替换）：

  ```
   $ snap connect micro-ros-agent:serial-port snapd:cp2102cp2109uartbrid
  ```

  在此之后，您可以像往常一样使用`micro-ros-agent serial -d <serial-dev>`.



* 此后每次调用只需：

get-ros2

get-mros

ros2 run micro_ros_agent micro_ros_agent udp4 --port 8888

