# 2.10工作日报

| 内容                                   | 时间                    |
| -------------------------------------- | ----------------------- |
| 解决mros程序中无法初始化的问题         | 9:30-11:00              |
| 尝试解决mros程序接受上位机字符串的问题 | 11:00-12:00;14:00-16:00 |
| ros_rqt入门                            | 16:00-17:00             |
| 尝试从上位机接受复合变量的问题         | 17:00-19:30             |
|                                        | 9:35上班/18:35下班      |

***

运行报错：多数是agent出错

1. 先检查WIFI和agent ip是否正确
2. 重启agent



## 编程历程：



#### 问题：~/WorkingNotes/MicroROS/learn_rcl

可以publish，可以subscribe到ros2命令行发布的 空std_msgs/msg/String，但不能接收到非空字符串

相关：~/WorkingNotes/MicroROS/int32_sub_pub可以publish和subscribe整数（int32）

1. 命令行发布的数据格式是否有误？

* 查资料ros2 string、yaml、topic echo、rqt messsage publisher等大致确定发布方式正确
  * 发布格式：ros2 topic pub /HelloExample/My_subscriber std_msgs/msg/String 'data: hello'
  * "data: hello"
  * data: 'hello'
  * data: "hello"
  * "{data: "hello"}"
  * "{data: 'hello'}"
  * '{data: 'hello'}'
  * data:\ "hello"

2. subscriber是否订阅上了？

* 能收到空字符，说明订阅ok

尚未解决。

#### 问题：~/WorkingNotes/MicroROS/learn_rcl

参考Kobuki

改成接收Twist消息，直接从turtle_teleon_key里订阅按键信息，用以控制机器人运动

但是不知道如何订阅外部话题，总会在自己节点中新建一个话题

1. 参考Kobuki程序，差异不大，

2. 参考CSDN帖子https://blog.csdn.net/m_zhangJingDong/article/details/78844052，仍然会新建话题

3. 其他MROS例程多数为订阅本节点的话题

4. 网上资料也多为rclcpp订阅方法

