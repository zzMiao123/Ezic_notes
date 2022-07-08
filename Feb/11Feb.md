# 2.11工作日报

| 内容                                        | 时间                    |
| ------------------------------------------- | ----------------------- |
| 实现mros从上位机接受按键控制                | 10:00-11:30             |
| mros中加入控制IO口的代码                    | 11:30-12:00;14:00:14:30 |
| 上位机通过键盘控制基于mros的小车（LED模拟） | 14:30-16:00;17:00-18:00 |
| 拆解电机、了解驱动方法                      | 16:00-17:00             |
| 分析内存占用情况                            | 18:30-19:30             |
|                                             | 9:50上班/18:50下班      |

### 问题：~/WorkingNotes/MicroROS/learn_rcl

参考Kobuki

改成接收Twist消息，直接从turtle_teleon_key里订阅按键信息，用以控制机器人运动

但是不知道如何订阅外部话题，总会在自己节点中新建一个话题

5. 猜测可能需要通过micro_ros_agent订阅？

* 先看一下int32_pub_sub的节点关系
  * 不需要绕过micro_ros_agent，直接对话题publish就行
  * ![image-20220211101238526](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220211101238526.png)
  * 查看subscription_init函数，猜测可能是init_with_default自动创建一个新话题
    * best-effort是什么意思？查看ROS2文档，与服务质量有关
    * Reliability
      - *Best effort*: attempt to deliver samples, but may lose them if the network is not robust.
      - *Reliable*: guarantee that samples are delivered, may retry multiple times.
    * 并没有，init_with_default会调用init函数
    * qos_profile 服务质量配置文件
  * 查看ROS2文档，看subscriber是如何匹配topic_name的
  * 会不会turtle_teleop_key不是发布topic通信，而是service通信？
    * 打开turtle_node对比查看
    * ![image-20220211103155022](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220211103155022.png)
    * echo比较，看cmd_vel是否依赖于feedback和status
      * 不依赖
    * 尝试看一下sim的源码是怎么订阅的
      * sim的源码应该用的是rclcpp
      * 直接订阅topic：/turtle1/cmd_vel
      * 要加.h文件
        * 怎么加？找不到怎么办？看小吹牛演示
          * 看不懂，没教程
        * 要加吗？
          * 尝试一下，在命令行里创建一个话题
            * 收到了！
            * 如果去掉斜杠呢？
              * 也可以！
            * 如果改成string呢？
              * 更改头文件、消息类型
              * 收不到消息
            * 如果改成Twist呢？
              * 可以！
* 原来是测试不对，自己没按那个键。。。

**解决**

看一下micro-ros-agent输出含义



### MROS中控制IO口

考虑在int32_publisher中开发

先确认该程序工作正常

* 正常

对照blink程序

* 理解程序
  * 包含gpio.h
  * 定义gpio_num_t
  * gpio_reset_pin, gpio_set_direction
  * LOGOUTPUT:
    * gpio: GPIO[5]| InputEn: 0| OutputEn: 0| OpenDrain: 0| Pullup: 1| Pulldown: 0| Intr:0 

  * gpio_set_level


**通过！**



下面联通上下端

用两个灯表示两个电机

* 从int32_pub_sub中复制过来
  * build报错：工具链不匹配
  * 更改外层CMakeList中的项目名
  * 重新打开终端和vscode
  * clean,fullclean
  * 编译通过

需要用四个IO口来控制高低电平

* 是否有可能用两个？
* 编写驱动函数
  * 报错：error: invalid operands to binary > (have 'const double *' and 'double')
  * 搜索传入参数指针的用法
  * 看一下之前入门freertos的程序
    * 运算时要用 *forward_s 作为变量运算
* usleep(RCL_S_TO_NS(2))耗费时间极长
  * 计算结果是啥
  * 改用vTaskDelay()
* IO口更改完了没置回
* 出现负数时死循环：没取绝对值

**完成！**



### 内存分析

vTaskList()编译不通过：menuconfig里没选中formating什么的

key_drive_light : 

参考：

* \#define CONFIG_MICRO_ROS_APP_STACK 16000

* xTaskCreate(micro_ros_task,

​                "**uros_task**",

​                CONFIG_MICRO_ROS_APP_STACK,

​                NULL,

​                CONFIG_MICRO_ROS_APP_TASK_PRIO,

​                NULL);

![image-20220211191448212](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220211191448212.png)

![image-20220211191609460](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220211191609460.png)

### 驱动电机

查找拆掉的小车电机型号和接口，尝试驱动它

JUGETEK 公司

17HS0401A-18B

https://www.jugetek.com/product-page/nema-17-22mm-long-pancake-stepper-motor-0-8a-42x42-22

看电机驱动芯片：A4988

搜索A4988驱动步进电机例程：

* get: http://www.taichi-maker.com/homepage/reference-index/motor-reference-index/arduino-a4988-nema-stepper-motor/

