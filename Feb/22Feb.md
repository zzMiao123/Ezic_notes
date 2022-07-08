# 2.22工作日报

| 内容                      | 时间                |
| ------------------------- | ------------------- |
| 整理推进思路              | 10:30-11:00         |
| 试用S3步进电机例程        | 11:00-12:00         |
| 用BOX控制步进电机         | 14:00-14:30         |
| 调试细分下的idf例程       | 14:30-16:30         |
| 找惯性传感器资料          | 16:30-17:00         |
| 理解rviz2可视化过程       | 17:00-18:00         |
| 了解描述机器人相关接口    | 19:00-19:30         |
| 了解ROS2其他通信机制      | 19:30-20:30         |
| 了解launch文件使用        | 20:30-21:00         |
| 建立RVIZ2可视化机器人模型 | 21:00-22:30         |
| 采购清单                  | 22:30-23:00         |
|                           | 10:30上班/23:00下班 |

## 项目进度梳理

目前已实现：

1. 上位机键盘控制四麦轮小车移动/转动
2. 小车通过超声感知外界障碍并反馈移动

待完善：

1. 步进电机控制噪音大，不顺滑
   1. 换用S3/BOX
   2. 电容能否去掉？       
      1. 电容作用：[滤波作用吧，如果没有其他较为敏感的电路与电机供电电源公用该电容可以不接的。](https://www.geek-workshop.com/thread-15473-1-1.html)
2. 反应有延迟
   1. 修改控制逻辑



下一步目标：

1. 上位机节点编写
2. Rviz2可视化实现
3. 调试cam

***

## S3 box驱动电机

### 1.先用idf里step motor的例程试试看

不接电容c



检查接线

没有保存新接口

没有连接驱动板电源

速度设的太快，丢步了

细分调小一点

### 2. BOX驱动

![img](https://github.com/espressif/esp-box/raw/master/docs/_static/hardware_pmod.png)

5V驱动电压连上去就没了

* 换别的开发板供电
  * 可以了

### 3.调试多细分例程

问题：电机在不细分下能够连续转动，但在加减速阶段会发出噪声；在4、16细分下不能转动，一直来回抖动。

* 检查线路
  * 没问题
* 可能是电流0.3A过小，调到0.8A附近
  * 仍然没解决
* 有可能是这块板子被烧坏了
  * 可以换块板子测试
* 自己重写加减速函数
  * 有空再说
* **暂时放弃用库，先用自己写的函数**



## Rviz2可视化过程

micro-ros节点发布TF话题

由rviz2节点订阅显示

不需要上位机再运行其他节点



### 机器狗rviz上位机如何实现

* 该软件由 ROS 框架中的 C++ 和 Python 节点组成。

![现货微走](https://github.com/mike4192/spotMicro/raw/master/assets/state_machine.png)

默认步态是由 8 个阶段组成的步行式步态，一次仅摆动一条腿，并在两腿摆动之间移动身体以使身体在保持在地面上的 3 条腿上保持平衡。软件中没有实现速度或角度命令限制，但逆运动学模型确实对三角域函数进行了粗略的限制，以避免数学错误。

yaml 配置文件用于保存各种软件配置设置，包括伺服配置字典。可以使用servo_move_keyboard 节点校准伺服，通过眼睛校准角度足以获得合理的性能。

- **i2cpwm_board**：控制 pca 9685 伺服控制板的节点。主要在比例控制模式下运行，但也可以在绝对控制模式下命令伺服系统怠速
- **spot_micro_keyboard_command**：通过键盘向运动控制节点发送状态、运动和速率命令的节点
- **spot_micro_joy**：发送与keyboard_command_node 相同的命令，但由由joy_node 发出的sensor_msgs/Joy 控制。默认情况下，它配置为 PS4 按钮布局。在尝试之前，请务必查看[操纵杆控制](https://github.com/mike4192/spotMicro/blob/master/docs/joystick_control.md)文档。
- **lcd_monitor**：在液晶显示器上显示基本状态信息和控制值的节点
- **spot_micro_plot**：通过 matplotlib 显示机器人的线框图，并从 spot_micro_motion_cmd 接收到状态数据。如果 spot_micro_motion_cmd 节点独立运行并且 debug_mode 参数设置为 true，则可以使用此绘图节点代替真实机器人来测试运动。
- **servo_move_keyboard**：一个 python 节点，可以与 i2cpwm_board 节点结合使用，通过键盘控制手动命令单个伺服。可用于伺服校准，建立伺服配置字典。
- **spot_micro_rviz**：一个节点，用于启动 RVIZ 并显示点微模型的可视化，以及未来的映射和导航元素。`show_and_move_model_via_gui`启动文件可以独立启动，以通过 GUI 滑块显示手动可移动的点微模型。

请注意，伺服控制节点`i2cpwm_board`一次只能由一个节点指挥。因此`spot_micro_motion_command`并且`servo_move_keyboard`应该排他性地运行；一次只能运行一个。

- **spot_micro_launch**：不是节点，而是纯粹用于收集高级启动文件的启动包。启动文件适用于更高级的用例，例如运行 SLAM。



机器人状态转换通过 TF2 发布。一些主要的兴趣帧是`base_footprint`和`base_link`， 和`lidar_link`。`base_footprint`是机器人坐标系底部高度为零的坐标系。`base_link`是固定在机器人身体中心的坐标系，随着身体运动而移动和旋转。`lidar_link`是与已安装的激光雷达对齐的坐标系。

![RVIZ](https://github.com/mike4192/spotMicro/raw/master/assets/rviz_animation.gif)



## 机器人描述格式——URDF

使用XML定义的一个最简单的URDF模型可以像下面这样

```xml
<?xml version="1.0"?>
<robot name="fishbot">
  <link name="base_link">
    <visual>
      <geometry>
        <cylinder length="0.18" radius="0.06"/>
      </geometry>
    </visual>
  </link>
</robot>
```

### 1.URDF的组成

URDF由`一个声明信息`和`两种关键组件`共同组成

### 2.Link标签定义

- visual 显示形状

  - ```
    <geometry> 
    ```

    (几何形状)

    - ```
      <box>
      ```

       

      长方体

      - 标签属性: `size`-长宽高
      - 举例：`<box size="1 1 1" />`

    - ```
      <cylinder>
      ```

       

      圆柱体

      - 标签属性:`radius` -半径 `length`-高度
      - 举例：`<cylinder radius="1" length="0.5"/>`

    - ```
      sphere
      ```

       

      球体

      - 属性：`radius` -半径
      - 举例：`<sphere radius="0.015"/>`

    - ```
      mesh
      ```

       

      第三方导出的模型文件

      - 属性：filename
      - 举例:` <mesh filename="package://robot_description/meshes/base_link.DAE"/>`

  - origin (可选：默认在物体几何中心)

    - 属性 `xyz`默认为零矢量 `rpy`弧度表示的翻滚、俯仰、偏航
    - 举例：`<origin xyz="0 0 0" rpy="0 0 0" />`

  - material 材质

    - 属性

       

      ```
      name
      ```

       

      名字

      - color
        - 属性 `rgba` a代表透明度
        - 举例：`<material name="white"><color rgba="1.0 1.0 1.0 0.5" /> </material>`

- collision 碰撞属性，仿真章节中讲解

- inertial 惯性参数 质量等，仿真章节中讲解

### Joint标签定义

joint属性

* name 关节的名称
* type 关节的类型
  * revolute: 旋转关节，绕单轴旋转,角度有上下限,比如舵机0-180
  * continuous: 旋转关节，可以绕单轴无限旋转,比如自行车的前后轮
  * fixed: 固定关节，不允许运动的特殊关节
  * prismatic: 滑动关节，沿某一轴线移动的关节，有位置极限
  * planer: 平面关节，允许在xyz，rxryrz六个方向运动
  * floating: 浮动关节，允许进行平移、旋转运动

joint的子标签

* parent 父link名称
  * <parent link="base_link" />
* child子link名称
  * <child link="laser_link" />
* origin 父子之间的关系xyz rpy
  * <origin xyz="0 0 0.014" />
* axis 围绕旋转的关节轴
  * <axis xyz="0 0 1" />

### 4.RVIZ2可视化URDF模型

URDF可视化的步骤如下：

1. 建立机器人描述功能包
2. 建立`urdf`文件夹编写urdf文件
3. 建立`launch`文件夹，编写launch文件
4. 修改`setup.py`配置，编译测试

报错：launch.invalid_launch_file_error.InvalidLaunchFileError: Caught exception when trying to load file of format [py]: "package 'fishbot_description' not found, searching: ['/home/maoyuxuan/turtlebot_ws/src/turtlebot_description/install/turtlebot_description', '/opt/ros/foxy']"



* #### 编译包的流程：

1. cd xxx_ws/src

2. ros2 pkg create xxxxxxxx --build-type ament_python --dependencies rclpy

3. 创建节点文件 .py

4. 修改setup.py

5. ```
       entry_points={
           'console_scripts': [
               "li4_node = village_li.li4:main"
           ],
       },
   )
   ```

6. 在工作空间里colcon build

7. source install/setup.bash

8. ros2 run 包名 节点名



先用github上已有的文件编译，通过，效果如下

![image-20220222220036358](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220222220036358.png)

![image-20220222214735453](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220222214735453.png)

### 5.launch文件

**launch文件允许我们同时启动和配置多个包含 ROS 2 节点的可执行文件**

