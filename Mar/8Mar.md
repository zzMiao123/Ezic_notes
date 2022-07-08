# 3.8工作日报

| 内容                        | 时间               |
| --------------------------- | ------------------ |
| 找机器人端导航的包          | 10:00-11:00        |
| 在PC上应用导航包            | 11:00-12:00        |
| 尝试移植PC上的包到Micro-ros | 14:00-18:00        |
| 讨论移植方法                | 19:00-20:30        |
| 研究crazyflie demo          | 20:30-22:00        |
| 浏览ros 包                  | 22:00-23:00        |
|                             | 9:50上班/22:50下班 |

简单无依赖包可以直接用（作为components）

那么launch.py可以修改成cpp文件，都放到库里，main里调这个函数就行了？

executable怎么用

先移植state_publisher



今天的历程：

先看了navigation2的官方教程，在PC端实现了TF状态发布

但想要在micro-ros上实现发布TF状态（用navigation2包），考虑采用navigation2子包robot_state_publisher，但是该包中有launch.py文件，无法在micro-ros中实现

经过和micro-ros组织和论坛里的人的沟通，得知了几点：

1. micro-ros不支持launch系统，未来一段时间也不考虑支持（可以在cpp中只调包定义的api）
2. 可以将ROS包放入micro_ros_espidf_component/extra packages 便能在idf中用ROS2包（测试一下）
3. 在micro_ros_setup/freertos/apps可以找到关于如何使用ROS包的示例（与2不同，是在micro_ros_setup这个烧录工具中）

看了crazyflie demo 得出结论是micro-ros只是改了通信层，硬件没改，所以没有参考价值。可以看看其他apps和自定义app的教程。

确定一个简单的包用于移植包的测试。
