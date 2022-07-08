# 1.20工作日报

| 内容                                | 时间        |
| ----------------------------------- | ----------- |
| 了解esp-iot-solution                | 10:00-11:00 |
| 了解芯片知识                        | 11:00-12:00 |
| 了解esp-idf configure menu          | 14:00-15:30 |
| 了解ESP芯片                         | 15:30-16:00 |
| 了解ESP模组                         | 16:00-16:30 |
| 讨论项目构想                        | 16:30-17:00 |
| 了解ESP开发板                       | 17:00-17:30 |
| 了解相关解决方案                    | 17:30-18:30 |
| 阅读ESP32S3硬件设计说明、技术说明书 | 18:30-19:00 |

***

### 一、关于ESP-IOT-solution

ESP-IoT-Solution 包含物联网系统开发中常用的外设驱动和代码框架，可作为 ESP-IDF 的补充组件，方便用户实现更简单的开发，其中包含的内容如下：

- 传感器、显示屏、音频设备、输入设备、执行机构等设备驱动；
- 低功耗、安全加密、存储方案等代码框架或说明文档；
- 从实际应用的角度出发，为乐鑫开源解决方案提供了入口指引。

**适配ESP32-S3**



### 二、芯片

[芯片官方介绍](https://www.espressif.com/zh-hans/products/socs)

[芯片和模组介绍贴](https://blog.csdn.net/Mark_md/article/details/120576979)

| 型号     | 描述                                                     |
| -------- | -------------------------------------------------------- |
| ESP8266  | **32-bit MCU & 2.4 GHz Wi-Fi**                           |
|          | 单核CPU 160MHz                                           |
|          | 天线输出功率 +19.5 dBm                                   |
|          | 睡眠电流 20 uA                                           |
|          | 32 管脚                                                  |
| ESP32    | **32-bit MCU & 2.4 GHz Wi-Fi & Bluetooth/Bluetooth LE**  |
|          | 双核CPU 80-240 MHz                                       |
|          | 低功耗蓝牙                                               |
|          | 睡眠电流 5 uA                                            |
|          | 48 管脚                                                  |
| ESP32-C3 | **32-bit RISC-V MCU & 2.4 GHz Wi-Fi & Bluetooth 5 (LE)** |
|          | 单核CPU 160MHz                                           |
|          | 32 管脚                                                  |
| ESP32-S2 | **32-bit MCU & 2.4 GHz Wi-Fi**                           |
|          | 单核CPU 240MHz                                           |
|          | 56 管脚                                                  |
|          | SRAM较小                                                 |
| ESP32-S3 | **32-bit MCU & 2.4 GHz Wi-Fi & Bluetooth 5 (LE)**        |
|          | Xtensa® 32 位 LX7 双核处理器  240 MHz                    |
|          | 加速神经网络计算和信号处理                               |
|          | 56 管脚                                                  |



### 二、模组

IPEX天线：外接天线，

* 方向指向性好，效率高，抗干扰能力强，能远离主板上的干扰，而且不用过多的进行调试匹配
* 成本高

PCB板载天线：

* 成本低，不需要单独组装天线，不易触碰损坏且组装方便
* 易受到主板上的干扰。

| 型号           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| ESP32-S3-WROOM | 通用型 Wi-Fi + 蓝牙 MCU 模组                                 |
| MINI           | 通用型 Wi-Fi 和低功耗蓝牙                                    |
| ESP32-S2-SOLO  |                                                              |
| WROVER         | SOLO的前代。`­WROVER­` 将 `WROOM` 的底部引脚，从中分两列均匀放置到了两侧。 |



### 三、开发板

| 型号            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| **S3**          |                                                              |
| DevKitC         | WROOM                                                        |
|                 | 按键、LED                                                    |
| DevKitM         | MINI                                                         |
|                 | 按键、LED                                                    |
| BOX             | AI语音开发套件                                               |
|                 | LCD、触控面板、麦克风、扬声器、按键、LED                     |
| EYE             | 图像识别、音频处理                                           |
|                 | 摄像头、LCD、麦克风、按键、TF卡                              |
| USB-OTG         | USB外设（WIFI图传、4G）                                      |
|                 | LCD、LED、按键                                               |
| Korvo1          | 语音识别                                                     |
|                 | 按键、TF卡、LED、麦克风                                      |
| Korvo2          | 语音识别、视频流处理                                         |
| **S2**          |                                                              |
| Kaluga          | 多媒体开发板                                                 |
|                 | 触控板、LCD、麦克风、扬声器、摄像头                          |
| Saola           |                                                              |
| **C3**          |                                                              |
| AWS-ExpressLink |                                                              |
| **32**          |                                                              |
| WROVER-KIT      |                                                              |
| PICO-KIT        |                                                              |
| LyraT           | 语音交互                                                     |
| LyraT-Mini      |                                                              |
| LyraTD-MSC      |                                                              |
| LyraTD-SYNA     |                                                              |
| LyraTD-DSPG     |                                                              |
| Vaquita-DSPG    | 语音识别                                                     |
| LCDKit          | HMI 相关开发与评估                                           |
| Ethernet-Kit    | 以太网测试                                                   |
| **8266**        |                                                              |
| Launcher        |                                                              |
| **其他**        |                                                              |
| Sense           | 触摸传感器                                                   |
| MeshKit-Sense   | 集成了温湿度传感器、环境亮度传感器等外设，并且可外接屏幕     |
| Prog            | 开发调试工具，具有自动下载固件、串口通信、JTAG 在线调试等功能。 |



### 四、相关解决方案

[ESP-USB](https://www.bilibili.com/video/BV12y4y1g7CH)

[ESP-LEDStrip](https://www.bilibili.com/video/BV1UY411W7Ci?spm_id_from=333.999.0.0)

ESP-Touch Sensor

[ESP-CSI](https://www.bilibili.com/video/BV1ui4y1o7fz?spm_id_from=333.999.0.0)



M5STACK

### 五、ESP-S3芯片手册

![Screenshot from 2022-01-20 18-57-11](/home/maoyuxuan/Pictures/Screenshot from 2022-01-20 18-57-11.png)

![image-20220120190159176](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220120190159176.png)

![image-20220120190225489](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220120190225489.png)



[选型参考ESP32-C3](https://blog.csdn.net/ami82/article/details/117228988)



