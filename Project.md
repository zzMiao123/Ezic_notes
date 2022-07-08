# MYX 项目开题讨论

## 一、需求分析

设计一款机器人平台，并开发应用示例，体现ESP32-S3芯片算力。



### **目标：**（1、2、3为基本目标，4可选）

1. 将MicroROS系统移植到ESP32-S3，开发一款移动系统主控平台
2. 开发一款移动平台（如小车），实现简单控制
3. 结合AI功能，开发有趣的应用示例
4. 增加结构和类型（机械臂、多足......）



### **要求：**

1. **成本低** -> 低于其他替代方案
2. **开发者友好** -> 文档、接口丰富
3. **（主控平台）通用性好** -> 与结构插接即可
4. **尽量榨干芯片算力**
5. **不要出现别的控制芯片** -> 不用舵机等
6. 结合AI -> 语音识别、图像识别、SLAM、路径规划......



## 二、背景调研

### 1. 之前做过的项目

| 项目（移动机电系统）              | 控制平台                                                     | 结构                                            | 功能                               | 评价                         |
| --------------------------------- | ------------------------------------------------------------ | ----------------------------------------------- | ---------------------------------- | ---------------------------- |
| 跳跃机器人                        | STM32F1裸                                                    | 双轮、跳跃垫板、弹簧、凸轮                      | 移动、跳跃、姿态控制               | 机械工艺要求高，弹跳效果一般 |
| TowerTakeover竞赛小车             | [VEX Brain](https://www.vexrobotics.com/276-4810.html)(FPGA) | 链轮、夹头、货架                                | 移动、夹取、储存、堆叠方块         | 功能较限制                   |
| 蛇形机械臂                        | Arduino Uno裸                                                | 18自由度串联机械臂                              | 按形状拟合、复杂腔道内运动         | 不方便用于移动平台           |
| *多机器人小车系统*                | *Arduino Uno裸*                                              | *TurtleBot*                                     | *移动、分布式控制*                 | *适合slam和车载机械臂*       |
| *机械臂送餐小车(见`小R科技小车`)* | *Arduino Mega裸*                                             | *履带、3自由度串联机械臂、储仓、摄像头等传感器* | *移动、抓取、储存、放置、颜色识别* | *国内常见的构型*             |



#### 1.1 跳跃机器人

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125112930159.png" alt="image-20220125112930159" style="zoom:67%;" />

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125112824699.png" alt="image-20220125112824699" style="zoom:67%;" />

跳跃机器人  STM32F1裸

双轮、跳跃垫板、弹簧、凸轮

移动、跳跃、姿态控制

机械工艺要求高，弹跳效果一般

#### 1.2 [VEX小车](https://www.bilibili.com/video/BV1K7411z7KV?from=search&seid=13744581929295231384&spm_id_from=333.337.0.0)

![image-20220125113702055](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125113702055.png)

<img src="https://i.ytimg.com/vi/QJI29QI5COM/maxresdefault.jpg" alt="VEX 2019 Tower Takeover RI3D Reveal - YouTube" style="zoom: 50%;" />

TowerTakeover竞赛小车  [VEX Brain](https://www.vexrobotics.com/276-4810.html)(FPGA)  

链轮、夹头、货架  

移动、夹取、储存、堆叠方块  

功能较限制

#### 1.3 蛇形机械臂

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125113607650.png" alt="image-20220125113607650" style="zoom:50%;" />

蛇形机械臂  Arduino Uno裸  

18自由度串联机械臂  

按形状拟合、复杂腔道内运动  

不方便用于移动平台



### 2. 相关项目

#### 2.1 [TurtleBot](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/)



<img src="https://www.turtlebot.com/assets/images/turtlebot_family.png" alt="TurtleBot Family" style="zoom: 100%;" />	

* 低成本的个人机器人工具包与开源软件

* TurtleBot 套件包括一个移动基座，2D/3D 距离传感器，单片机，以及 TurtleBot 安装硬件套件。
* [开源](https://github.com/ROBOTIS-GIT/turtlebot3.git)，用户还可以从 ROS wiki 下载 TurtleBot SDK。
* 易于购买，建造和组装，使用现成的消费品和零件，可以很容易地从标准的材料。
* 社区维护，许多大学正在使用 TurtleBot 来教授机器人入门课程

**功能：**

* 核心是 SLAM 和导航
* 可以通过笔记本电脑、控制器或基于 android 的智能手机进行远程控制；还可以在人们走进房间时跟着他们的腿走。
*  ROS 机械臂导航能力



#### 2.2 小R科技WIFI机械臂小车



<figure class="half">
    <img src="https://gd3.alicdn.com/imgextra/i3/94674554/TB2duJ1ruuSBuNjSsziXXbq8pXa_!!94674554.jpg" style="zoom:40%;"/>
    <img src="https://gd2.alicdn.com/imgextra/i3/94674554/O1CN01vKgOER1jVmQ3ljKzN_!!94674554-0-picasso.jpg" alt="img" style="zoom:40%;" />
</figure>
*机械臂送餐小车(见`小R科技小车`)*  *Arduino Mega裸*  

*履带、3自由度串联机械臂、储仓、摄像头等传感器*  

*移动、抓取、储存、放置、颜色识别* 

 *国内常见的构型*



#### 2.3 Cozmo智能电子宠物

![finder](https://github.com/whatrocks/cozmo-tensorflow/raw/master/assets/cozmo-detective.gif)

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220126172928581.png" alt="image-20220126172928581" style="zoom:50%;" />

![Screenshot from 2022-07-08 16-24-37](/home/maoyuxuan/Pictures/Screenshot from 2022-07-08 16-24-37.png)

#### 2.4 JetRacer

![img](https://user-images.githubusercontent.com/4212806/67442981-ce459e00-f5b7-11e9-9c8a-14ab360decb8.gif)



#### 2.5 斯坦佛小狗

![img](https://pro18cda46f-pic3.ysjianzhan.cn/upload/MiniPupper.jpg)



#### 3. 其他资料

市面AI小车价格均在400元以上。

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220124205227845.png" alt="image-20220124205227845" style="zoom:50%;" />



### 三、概念设计

#### 3.1 构型

| 方案              | 实现难度-机械控制 | 实现难度-AI | 资源情况                                                     |
| ----------------- | ----------------- | ----------- | ------------------------------------------------------------ |
| ESP-Racer         | 中(2)             | 高(1)       | MIT License<br />基于Jetson Nano(GPU)<br />[Jupyter](https://github.com/NVIDIA-AI-IOT/jetracer.git)<br />资料少 |
| **ESP-TurtleBot** | 低(3)             | 中(3)       | Apache-2.0 License (few BSD3, GPL3)<br />基于树莓派<br />Python ROS/[MicroROS](https://github.com/micro-ROS/micro-ROS_kobuki_demo) |
| ESP-Puppy         | 中(1)             | 中(2)       | MIT License<br />基于树莓派4/3B<br />[Python](https://github.com/stanfordroboticsclub/StanfordQuadruped)/[ROS Kinetic](https://github.com/mike4192/spotMicro)<br />硬件全开源<br />有上位机 |
|                   |                   |             |                                                              |

**MicroROS是一组分层的库，它们可以直接重用ROS 2的库，也可以使其适应资源受限设备的功能和需求。**



#### 3.2 功能

<img src="/home/maoyuxuan/.config/Typora/typora-user-images/image-20220125120848494.png" alt="image-20220125120848494" style="zoom:50%;" />

* 基本移动
* 控制（上位机、手机、遥控器）
* 感知：避障、图传、Slam、自动跟随
* AI：颜色识别、人脸识别、语音交互、手势识别、自动驾驶（规划和避障）

### 四、开发计划

#### 4.1 任务一览：

**阶段一**：

- 部署MicroROS到ESP32S3上，评估内存、CPU、flash情况
- 测试 led/ lcd、IO口、通信等底层功能
- 学习、调试上位机（RViz, rqt）

**阶段二**

- Wifi 学习和测试
- AI 算法（语音、图传、图像识别）

**阶段三**

* LCD UI 优化

* 机器人学控制算法优化

**阶段四**

* 客户对接
* 方案评估
* 结构优化
* 成本优化
* 丰富构型

- 开发机械臂及其控制（moveit）
- 多机器人应用

#### 4.2 项目甘特图（Demo）

| 内容                                              | 2.7-2.13 | 2.14-2.27 | 2.28-3.6 | 3.7-3.20 | 3.21-4.3 | 4.4-4.17 | 4.18-5.1 | 5.2-5.15 |
| ------------------------------------------------- | :------: | :-------: | -------- | :------: | :------: | :------: | :------: | :------: |
| 部署MicroROS到ESP32S3上，评估内存、CPU、flash情况 |   :o:    |           |          |          |          |          |          |          |
| 测试 led/ lcd、IO口、通信等底层功能               |          |    :o:    |          |          |          |          |          |          |
| 学习、调试上位机（RViz, rqt）                     |   :o:    |    :o:    |          |          |          |          |          |          |
| **阶段一效果评估**                                |          |           |          |          |          |          |          |          |
| Wifi 学习和测试                                   |          |           | :o:      |   :o:    |          |          |          |          |
| AI 算法（语音、图传、图像识别）                   |          |           |          |   :o:    |   :o:    |          |          |          |
| **阶段二效果评估**                                |          |           |          |          |          |          |          |          |
| LCD UI优化                                        |          |           |          |          |   :o:    |   :o:    |          |          |
| 机器人学控制算法优化                              |          |           |          |          |   :o:    |   :o:    |          |          |
| **阶段三效果评估**                                |          |           |          |          |          |          |          |          |



***

参考：

[机器人学算法py实现](https://github.com/AtsushiSakai/PythonRobotics.git)



### 五、进度梳理

#### 1March

问题：ROS上很多包
