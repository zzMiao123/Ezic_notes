# 2月7日工作日报

| 内容               | 时间               |
| ------------------ | ------------------ |
| 学习ROS2机制       | 9:30-10:30         |
| MicroROS教程       | 10:30-12:30        |
| RCL/RCLC编程       | 14:00-16:30        |
| ROS2与MicroROS通信 | 17:30-18:30        |
|                    | 9:14上班/18:14下班 |

***



## ROS2 深入学习



参考资料：http://d2lros2foxy.fishros.com/?error=redirect_uri_mismatch&error_description=The+redirect_uri+MUST+match+the+registered+callback+URL+for+this+application.&error_uri=https%3A%2F%2Fdocs.github.com%2Fapps%2Fmanaging-oauth-apps%2Ftroubleshooting-authorization-request-errors%2F%23redirect-uri-mismatch#/chapt1/1.2ROS%E4%B8%8EROS2%E5%AF%B9%E6%AF%94

### 1. 节点

每一个节点也是只负责一个单独的模块化的功能（比如一个节点负责控制车轮转动，一个节点负责从激光雷达获取数据、一个节点负责处理激光雷达的数据、一个节点负责定位等等）

一共四种通信方式:

- 话题-topics
- 服务-services
- 动作-Action
- 参数-parameters

<img src="http://d2lros2foxy.fishros.com/chapt3/3.1ROS2%E8%8A%82%E7%82%B9%E4%BB%8B%E7%BB%8D/imgs/Nodes-TopicandService.gif" alt="Nodes-TopicandService" style="zoom:50%;" />



启动节点：`ros2 run <package_name> <executable_name>`

CLI（Command-Line Interface）和GUI（Graphical User Interface）

- GUI（Graphical User Interface）就是平常我们说的图形用户界面，大家用的Windows是就是可视化的，我们可以通过鼠标点击按钮等图形化交互完成任务。
- CLI（Command-Line Interface）就是命令行界面了，我们所用的终端，黑框框就是命令行界面，没有图形化。

查看节点列表：`ros2 node list`

查看节点信息：`ros2 node info <node_name>`

重映射节点名称：`ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle`

### 2. 工作空间

即IDF中的项目文件夹

### 3.通信机制



## [RCL/RCLC编程](https://micro.ros.org/docs/tutorials/programming_rcl_rclc/overview/)

RCL（ROS Client Library）ROS客户端库，其实就是ROS的一种API，提供了对ROS话题、服务、参数、Action等接口。

### 1. 节点

对照int32_publisher例程理解

* 创建分配器和支持

  * `rcl_get_default_allocator()`
  * `rclc_support_init(&support, argc, argv, &allocator)`

* 创建节点

  * `rcl_node_t node`
  * `rclc_node_init_default(&node, node_name, namespace, &support)`

* 删除节点

  * 先删除节点拥有的实体：publisher, subscriber, service
    * `rcl_publisher_fini(&publisher, &node)`
  * `rcl_node_fini(&node)`

* 生命周期

  * 初始化

    * 创建state_machine

    * 创建lifecycle_node 

    * ```
      rcl_ret_t rc = rclc_make_node_a_lifecycle_node(
        &my_lifecycle_node,
        &node,
        &state_machine,
        &allocator);
      ```

  * 回调

  * 运行

  * 清除

### 2. Publisher&Subscriber

对照int32_sub_pub例程理解

* 初始化

  * ```
    rcl_publisher_t publisher;
    ```

  * ​    RCCHECK(rclc_publisher_init_default(

    ​        &publisher,

    ​        &node,

    ​        ROSIDL_GET_MSG_TYPE_SUPPORT(std_msgs, msg, Int32),

    ​        "int32_publisher"));

* 发送消息

  * `rcl_publish(&publisher, &msg, NULL)`

* 一旦订阅者和执行者被初始化，订阅者回调必须被添加到执行者中，以便在其旋转后接收传入的发布：

* ```
  // Message object to receive publisher data
  std_msgs__msg__Int32 msg;
  
  // Add subscription to the executor
  rcl_ret_t rc = rclc_executor_add_subscription(
    &executor, &subscriber, &msg,
    &subscription_callback, ON_NEW_DATA);
  
  if (RCL_RET_OK != rc) {
    ...  // Handle error
    return -1;
  }
  
  // Spin executor to receive messages
  rclc_executor_spin(&executor);
  ```

### 3.Service

* 服务器初始化

  * `rcl_service_t service`

  * rcl_service_t service = rcl_get_zero_initialized_service();

  * RCCHECK(

    ​    rclc_service_init_default(

    ​      &service, &node,

    ​      ROSIDL_GET_SRV_TYPE_SUPPORT(example_interfaces, srv, AddTwoInts), "/addtwoints"));

* 一旦请求到达，执行器将使用请求和响应消息作为参数调用配置的回调。

* 初始化服务和执行程序后，必须将服务回调添加到执行程序，以便在执行程序旋转后处理传入的请求

* 

* 客户端初始化

  * rcl_client_t client

  * rcl_client_t client = rcl_get_zero_initialized_client();

  * RCCHECK(

    ​    rclc_client_init_default(

    ​      &client, &node,

    ​      ROSIDL_GET_SRV_TYPE_SUPPORT(example_interfaces, srv, AddTwoInts), "/addtwoints"));

* 

### 4.参数服务器



## [高级教程](https://micro.ros.org/docs/tutorials/advanced/overview/)









































