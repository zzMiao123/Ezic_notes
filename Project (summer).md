# MYX 项目开题讨论

### 一、相关项目

1. [Stanford Quadruped](https://pupper.readthedocs.io/en/latest/)

<img src="https://camo.githubusercontent.com/afa53da66af8f1f3345dcd5a1e3afa948b88299e9d3b375ffcebad9103f68dc5/68747470733a2f2f6c6976652e737461746963666c69636b722e636f6d2f36353533352f34393631343639303735335f373865646361383362635f346b2e6a7067" alt="Pupper CC Max Morse" style="zoom: 25%;" />

碳纤维+3D打印+标准件

树莓派4+自行开发软件

优点：机械设计巧妙；英文文档齐全

缺点：以python为主，移植可能存在问题

2. [Spot Micro](https://github.com/mike4192/spotMicro)

![Spot Micro Walking](https://github.com/mike4192/spotMicro/raw/master/assets/spot_micro_walking.gif)

3D打印

### 二、开发计划

v1

| 内容                             |   6.27-7.1   |   7.4-7.8    |  7.11-7.15   |  7.18-7.22   |  7.25-7.29   |      |   8.8-8.12   |  8.15-8.19   |  8.22-8.26   |
| -------------------------------- | :----------: | :----------: | :----------: | :----------: | :----------: | ---- | :----------: | :----------: | :----------: |
| **结构方案和电控方案设计、采购** | :green_book: |              |              |              |              |      |              |              |              |
| *追踪*                           |              |              |              |              |              |      |              |              |              |
| **装配和软硬件集成**             |              | :green_book: | :green_book: |              |              |      |              |              |              |
| *追踪*                           |              |              |              |              |              |      |              |              |              |
| **运行原装代码**                 |              | :green_book: | :green_book: |              |              |      |              |              |              |
| *追踪*                           |              |              |              |              |              |      |              |              |              |
| **开发32适配代码**               |              | :green_book: | :green_book: | :green_book: |              |      |              |              |              |
| *追踪*                           |              |              |              |              |              |      |              |              |              |
| **调参优化**                     |              |              |              | :green_book: | :green_book: |      |              |              |              |
| *追踪*                           |              |              |              |              |              |      |              |              |              |
| **语音控制功能开发与调试**       |              |              |              |              |              |      | :green_book: |              |              |
| *追踪*                           |              |              |              |              |              |      |              |              |              |
| **表情UI设计**                   |              |              |              |              |              |      |              | :green_book: |              |
| *追踪*                           |              |              |              |              |              |      |              |              |              |
| **机动时间**（待定）             |              |              |              |              |              |      |              |              | :green_book: |
| *追踪*                           |              |              |              |              |              |      |              |              |              |



v2

| 内容                                              |   6.27-7.1   |            7.4-7.8            |   7.11-7.15   |  7.18-7.22   |  7.25-7.29   |      |   8.8-8.12   |  8.15-8.19   |  8.22-8.26   |
| ------------------------------------------------- | :----------: | :---------------------------: | :-----------: | :----------: | :----------: | ---- | :----------: | :----------: | :----------: |
| **结构方案和电控方案设计、采购**                  | :green_book: |                               |               |              |              |      |              |              |              |
| *追踪*                                            |     :ok:     |                               |               |              |              |      |              |              |              |
| **装配和软硬件集成**                              |              |         :green_book:          |               |              |              |      |              |              |              |
| *追踪*                                            |              | :stop_button:(舵机控制板未到) |     :ok:      |              |              |      |              |              |              |
| **运行原装代码**                                  | :green_book: |         :green_book:          |               |              |              |      |              |              |              |
| *追踪*                                            |     :ok:     |         :stop_button:         |     :ok:      |              |              |      |              |              |              |
| **开发32适配代码**                                | :green_book: |         :green_book:          | :green_book:  |              |              |      |              |              |              |
| *追踪*                                            |     :ok:     |             :ok:              | :stop_button: |              |              |      |              |              |              |
| **语音控制功能开发与调试**                        |              |                               | :green_book:  | :green_book: |              |      |              |              |              |
| *追踪*                                            |              |                               |     :ok:      |              |              |      |              |              |              |
| **调参优化**                                      |              |                               | :green_book:  | :green_book: | :green_book: |      |              |              |              |
| *追踪*                                            |              |                               | :stop_button: |              |              |      |              |              |              |
| **低成本设计优化（结构、PCB、元件）、打样**       |              |                               |               | :green_book: | :green_book: |      |              |              |              |
| *追踪*                                            |              |                               |               |              |              |      |              |              |              |
| **表情UI设计、人机交互设计**                      |              |                               |               |              |              |      | :green_book: | :green_book: |              |
| *追踪*                                            |              |                               |               |              |              |      |              |              |              |
| **结合ROS模拟、强化学习、SLAM**（可作为后续方向） |              |                               |               |              |              |      |              |              | :green_book: |
| *追踪*                                            |              |                               |               |              |              |      |              |              |              |



***

参考：

stanford pupper: https://pupper.readthedocs.io/en/latest/

pupper mini: https://github.com/mangdangroboticsclub/QuadrupedRobot
国人改进：https://github.com/blackbox114/Blacky
