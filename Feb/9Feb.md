# 2.9工作日报

| 内容                               | 时间                    |
| ---------------------------------- | ----------------------- |
| MicroROS API学习(debug)            | 10:00-12:30             |
| 基于mros编程实践                   | 14:00-15:30             |
| 尝试在一个芯片上启动多节点         | 15:30-16:00             |
| 实现单片机与上位机用mros_agent通信 | 16:00-18:00;19:00-20:00 |
|                                    | 10:00上班/19:00下班     |

API学习内容汇总到[7Feb.md](/home/maoyuxuan/WorkingNotes/Feb/7Feb.md)中

mros_agent 不重启的话，上次创建的图不会消失

注意：如果想使用超过2个发布者和订阅者，需要更改component中的colcon.meta里的

​	line38-45:

                "-DRMW_UXRCE_XML_BUFFER_LENGTH=400",
                "-DRMW_UXRCE_TRANSPORT=udp",
                "-DRMW_UXRCE_MAX_NODES=1",
                "-DRMW_UXRCE_MAX_PUBLISHERS=2",
                "-DRMW_UXRCE_MAX_SUBSCRIPTIONS=2",
                "-DRMW_UXRCE_MAX_SERVICES=1",
                "-DRMW_UXRCE_MAX_CLIENTS=1",
                "-DRMW_UXRCE_MAX_HISTORY=1"





### mros程序结构(main.c)：

* include头文件

* 宏定义

* 全局变量（某些publisher、msg）

* callback functions

* micro_ros_task(void * arg)

  * 初始化rcl
    * allocator, support, init_options
  * 初始化node, publisher, subscribers, timer
  * 设置发送数据
  * 设置executor
    * 初始化
    * add objects(subscription, timer)
  * spin executor
  * 释放动态内存

* app_main 主程序入口

  * void app_main(void)

    {   

    \#ifdef UCLIENT_PROFILE_UDP

    ​    // Start the networking if required

    ​    ESP_ERROR_CHECK(uros_network_interface_initialize());

    \#endif  // UCLIENT_PROFILE_UDP

    

    ​    //pin micro-ros task in APP_CPU to make PRO_CPU to deal with wifi:

    ​    xTaskCreate(micro_ros_task, 

    ​            "uros_task", 

    ​            CONFIG_MICRO_ROS_APP_STACK, 

    ​            NULL,

    ​            CONFIG_MICRO_ROS_APP_TASK_PRIO, 

    ​            NULL); 

    }

