# 1.21 工作日报

| 内容                         | 时间               |
| ---------------------------- | ------------------ |
| 阅读ESP-WHO入门指南          | 10:00-11:00        |
| human_face_detection例程烧录 | 11:00-12:00        |
| color_dectetion例程烧录(S3)  | 14:00-15:00        |
| motion_detection例程烧录     | 15:00-15:30        |
| 了解ESP-BOX                  | 15:30-16:00        |
| 了解ESP-Drone                | 16:00-17:00        |
| 了解ESP-Moonlight及其实现    | 17:00-19:00        |
|                              | 9:50上班/18:50下班 |

代码看不懂，需要补充FreeRTOS知识

***

### 一、ESP-WHO

#### 1.硬件组成

![ESP-EYE image](https://github.com/espressif/esp-who/raw/master/docs/_static/get-started/esp-eye_callout.png)

#### 2. 软件开发

***

**初次尝试**

- 进入例程human_face_detection/terminal
- get-idf
- idf.py set-target esp32
- idf.py menuconfig -> component config -> ESP-WHO configuration 设置开发板
- idf.py -p /dev/ttyUSB0 flash

*build出错*

***

**解决方案：切换idf分支到稳定版**

* 进入idf路径
* git branch *查看分支*
* git checkout release/v4.4 *切换分支*
* git submodule update --init --recursive

*提示缺少py插件*

* ./install.sh
* 可以正常build、flash

*因为没录入人脸，所以没有显示，考虑在带屏版本*上尝试

***



#### 3. ESP32-S3-EYE与ESP32-EYE

| 特性               | ESP32-S3-EYE                                 | ESP-EYE         |
| ------------------ | -------------------------------------------- | --------------- |
| 内置芯片           | ESP32-S3                                     | ESP32           |
| PSRAM              | 8 MB 八线 PSRAM                              | 8 MB 四线 PSRAM |
| Flash              | 8 MB flash                                   | 4 MB flash      |
| LCD 显示屏         | 有                                           | 无              |
| 加速度传感器       | 有                                           | 无              |
| 可选电源           | 外接锂电池                                   | 无              |
| USB 至 UART 桥接器 | 不需要，由 ESP32-S3 USB Serial/JTAG 接口提供 | 需要            |
| 天线连接器         | 不需要，由 ESP32-S3-WROOM-1 模组提供         | 需要            |



### 二、ESP-BOX

语音唤醒、识别



### 三、ESP-Drone

#### 1. 亮点：

* 支持自稳定模式 (Stabilize mode)：自动控制机身水平，保持平稳飞行。
* 支持 PC 上位机调试：使用 cfclient 上位机进行静态/动态调试。
* 支持 APP 控制：使用手机 APP 通过 Wi-Fi 轻松控制。
* 模块化设计：主控板搭载模组和基础飞行传感器，扩展板搭载扩展传感器

#### 2. 移植系统[Crazyflie](https://www.bitcraze.io/) ：

- 支持多种传感器组合，可以轻松实现定高模式、定点模式等高级飞行模式。
- 基于 FreeRTOS 编写，将复杂的无人机系统，分解成多个具有不同优先级的软件任务。
- 设计了功能完备的 cfclient 上位机和 CRTP 通信协议，便于实现调试、测量和控制。

#### 3. 文件树

```
.
├── components                        | 项目组件目录
│   ├── config                              | 系统 task 配置
│   │   └── include
│   ├── core                                 | 系统内核目录
│   │   └── crazyflie                  | crazyflie 内核
│   │       ├── hal                         | 硬件抽象代码 
│   │       └── modules             |  飞行控制代码 
│   ├── drivers                            | 硬件驱动目录
│   │   ├── deck                         | 硬件扩展接口驱动
│   │   ├── general                    | 一般设备目录
│   │   │   ├── adc                     | ADC 驱动，用于电压监测
│   │   │   ├── buzzer              | 蜂鸣器驱动，用于状态反馈
│   │   │   ├── led                     | LED 驱动，用于状态反馈
│   │   │   ├── motors             | 电机驱动，用于推力输出
│   │   │   └── wifi                    | Wi-Fi 驱动，用于通信
│   │   ├── i2c_bus                   | I2C 驱动
│   │   ├── i2c_devices           | I2C 设备目录
│   │   │   ├── eeprom           | eeprom 驱动，用于参数存储
│   │   │   ├── hmc5883l         | hmc5883l 磁罗盘传感器
│   │   │   ├── mpu6050          | mpu6050 陀螺仪加速度计传感器
│   │   │   ├── ms5611             | ms5611 气压传感器
│   │   │   ├── vl53l0                 | Ivl53l0 激光传感器（最大测距 2 m）
│   │   │   └── vl53l1                 |  Ivl53l1 激光传感器（最大测距 4 m）
│   │   └── spi_devices           | SPI 设备目录
│   │       └── pmw3901           | pmw3901 光流传感器
│   ├── lib                                      | 外部库目录
│   │   └── dsp_lib                    | dsp 库
│   ├── platform                         | 用于支持多平台
│   └── utils                                  | 工具函数目录
├── CMakeLists.txt                    | 工具函数
├── LICENSE                                | 开源协议
├── main                                       | 入口函数
├── README.md                        | 项目说明
└── sdkconfig.defaults            | 默认参数
```

#### 4. 代码

看不太懂，补充FreeRTOS知识后再读



### 四、[ESP-Moonlight](https://docs.espressif.com/projects/espressif-esp-moonlight/zh_CN/latest/introduction.html)

#### 1. 驱动程序

* [LEDC](https://docs.espressif.com/projects/esp-idf/zh_CN/release-v4.0/api-reference/peripherals/ledc.html) 是 ESP32 中主要用于控制 LED 亮度和颜色的一个外设，也可以产生 PWM 信号用于其他用途。
  *  `led_rgb_create()` 函数初始化了三个通道分别控制 RGB 三色灯，在参数中指定了 PWM 的频率是 20 KHz ，分辨率为 8 位。
  * `ESP_ERROR_CHECK(g_leds->set_hsv(g_leds, a, b, c));`,`ESP_ERROR_CHECK(g_leds->set_rgb(g_leds, a, b, c));`分别以 HSV 参数和 RGB 参数两种方式控制 LED 灯。
* 按钮连接至 ESP32 的 GPIO0
  * `configure_push_button()` 函数配置按钮功能：创建 button 对象，指定 GPIO 输出端及其有效电平用于检测按钮动作；为按钮注册事件回调函数，松开按钮时，就会在 esp-timer 线程中调用 `button_press_cb()` 函数。
* 震动开关在静止时为开路状态，当受到外力触碰时两个引脚导通，外力消失后恢复到开路状态
  * `sensor_vibration_init()` 进行初始化，指定震动传感器的 IO 口，其后注册一个传感器触发的回调函数，当震动传感器输出的电平发生变化即会进行回调。
* ESP32 集成有两个 12 位的逐次逼近型 [ADC](https://docs.espressif.com/projects/esp-idf/zh_CN/release-v4.0/api-reference/peripherals/adc.html)，一共支持 18 个模拟通道输入。电池的电压经过电阻 1/2 分压后输入到 ESP32 ADC 的一个通道。 ADC 内部的参考电压为 1100 mv ，内部还有一个可调的衰减系数，增大了 ADC 的输入范围。
  * `adc1_config_width()` 配置 ADC 的分辨率为 12 位
  *  `adc1_config_channel_atten()` 设置内部衰减为 11 DB，也就是大约原来的 1/3.6 ，再加上外部电阻的衰减才能保证电压动态范围不超过 ADC 的量程
  *  `xTaskCreatePinnedToCore()` 在 CPU1 上创建了一个用于电池监测的任务。
* 主程序
  * 5 ~ 10 行初始化 NVS (Non-volatile storage)，Wi-Fi 需要保存一些参数到 NVS 中。
  * 第 21 行调用 `xTaskCreate()` 创建了一个任务来控制 LED 灯实现呼吸效果，表示正在进行连接。
  * `wifi_init_sta()` 连接 Wi-Fi ，连接结束后函数才会返回。

#### 2. WIFI连接

**ESP32 的 Wi-Fi 具有以下模式：**

- 基站模式（即 STA 模式或 Wi-Fi 客户端模式），此时 ESP32 连接到接入点 (AP)。
- AP 模式（即 Soft-AP 模式或接入点模式），此时基站连接到 ESP32。
- AP-STA 共存模式（ESP32 既是接入点，同时又作为基站连接到另外一个接入点）。
- 使用混杂模式监控 IEEE802.11 Wi-Fi 数据包。

**ESP只支持到2.4GWIFI**



* 把 Wi-Fi 初始化为 sta 模式：

  - 调用 `xEventGroupCreate()` 来创建一个事件标志组，它是操作系统提供的功能， 使用参见 [Event Group API](https://docs.espressif.com/projects/esp-idf/zh_CN/v4.0/api-reference/system/freertos.html#event-group-api)。

  - 调用 `esp_event_handler_register()` 向 Wi-Fi 组件注册一些事件通知。

  - 调用 `tcpip_adapter_init()` 来初始化 TCP/IP 堆栈。

  - 通过 `WIFI_INIT_CONFIG_DEFAULT` 读取一个 Wi-Fi 的默认配置。

  - 调用 `esp_wifi_init()` 、`esp_wifi_set_config()` 和 `esp_wifi_set_mode()` 来初始化 Wi-Fi 子系统及其 station 接口。将连接的 Wi-Fi 名称和密码分别是 `EXAMPLE_ESP_WIFI_SSID` 和 `EXAMPLE_ESP_WIFI_PASS`。

  - 30 ~ 52 行代码是无限等待事件标志组被置位。当 `WIFI_CONNECTED_BIT` 或 `WIFI_FAIL_BIT` 被置位后，打印出信息并删除一些变量且注销事件通知。

* 注册到 Wi-Fi 组件的回调函数:

  * 当调用 `esp_wifi_start()` 后会产生一个 `WIFI_EVENT_STA_START` 事件，随即调用 `esp_wifi_connect()` 函数开始连接过程。
  * 当连接上后，会产生 `IP_EVENT_STA_GOT_IP` 事件，这时读取 event_data 来获得 ip 地址，并调用 `xEventGroupSetBits()` 来设置事件标志组的 `WIFI_CONNECTED_BIT` 位。
  * 当 Wi-Fi 断开连接后将产生 `WIFI_EVENT_STA_DISCONNECTED` 事件，这时将执行 9 ~ 15 行的代码进行重连。

**自定义配网方式**

- **SoftAP 配网**：需要用户手动连接到 ESP32 的热点网络；不过这种方式很可靠，设备端的代码也简单。
- **Bluetooth Low Energy 配网**：用户无需切换 Wi-Fi 网络，但是需要打开蓝牙；但是，需要在设备端加入蓝牙相关代码，这会增加固件的大小，并在配网完成前占用一定内存。
- **Smartconfig 配网**：这种方式不需要建立任何通信链路，手机端通过发送不同长度的 UDP 广播包来表示 Wi-Fi 信息，ESP32 在混杂模式监听信号覆盖范围内的所有数据帧，通过一定算法得到 Wi-Fi 信息。缺点是配网成功率受环境的影响较大。
- **WEB 配网**：在 ESP32 上建立热点，使用手机连接上后在浏览器打开配置网页，在网页中完成配网，这种方式很可靠，而且允许在电脑端完成配网，缺点是需要在设备端占用空间来嵌入网页。

**BluFi配网**

* `blufi_init()` 进行 BluFi 的初始化，`blufi_wait_connect()` 函数则一直等待配网完成。 在 components/blufi 文件夹中存放了 BluFi 的相关代码。
* 工程中新增文件的作用：
  * `components/blufi/blufi.c`：关于 BluFi 的应用代码。
  * `components/blufi/blufi_security.c`：关于 BluFi 安全加密相关。
  * `sdkconfig.default`：保存了项目的默认配置，用于指定某些配置项。
  * `partitions.csv`：ESP32 flash 的分区表，默认的分区表中留给 factory 应用程序的空间对于本节程序是不足的，自定义的分区表将 factory 分区增大到了 2 MB。

**WIFI信息的存储**

这里使用 NVS 进行保存。NVS(Non-volatile storage) 是一种软件组件，用于永久储存键值对，即便设备重启或断电，这些信息也不会丢失。NVS 在 flash 中有一个专门的分区来储存这些信息。

默认情况下，Wi-Fi 组件会自动帮我们在 NVS 中保存上一次连接的 Wi-Fi 信息

#### 3. 远程控制

连接方式：

* 将设备接入云，手机通过云平台的 API 来进行控制‘
* 设备在局域网内连接，不经过外网

使用 UDP 进行通信，使 ESP32 设备端作为 UDP Server，手机作为 Client 端，手机上使用微信小程序作为控制端。 当在微信小程序中点击颜色块时，手机会以 UDP 广播的方式发送 JSON 数据包，ESP32 接收到数据后进行解析，将得到的颜色值发送给 LED 灯。

* 1 ～ 26 行为 UDP 的通信配置过程
* 在循环中不断调用 `recvfrom()` 来接收数据
* 将接收到的数据使用 `cJSON_Parse()` 进行解析得到 LED 灯的颜色值
* 最后用解析出的颜色值去控制 LED 灯

#### 4. 语音识别

**ESP-SR 包含三个主要模块：**

- 唤醒词识别模型：WakeNet
- 语音命令识别模型：MultiNet
- 声学算法：麦克风阵列语音增强（Mic-Array Speech Enhancement，简称 MASE）、回声消除（Acoustic Echo Cancellation，简称 AEC）、语音端点检测（Voice Activity Detection，简称 VAD）、自动增益控制（Automatic Gain Control，简称 AGC）、噪声抑制（Noise Suppression，简称 NS）

**调用语音识别模型：**

- 首先调用 `i2s_read()` 从麦克风读取一段音频数据，然后进行数据格式的调整。
- 根据 `enable_wn` 变量来控制使用唤醒识别还是命令词识别。
- 调用 `detect()` 函数将音频数据送入对应的识别网络进行识别。
- 在识别命令词时，当识别的帧数达到最大（即 mn_count=mn_num）时回到唤醒词识别状态。

**命令词定义：**

```
CONFIG_CN_SPEECH_COMMAND_ID0="da kai dian deng"
CONFIG_CN_SPEECH_COMMAND_ID1="kai deng"
```

***

### Q&As

1.  像这样烧得很慢正常吗？

   ![image-20220121115612514](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220121115612514.png)

*答：正常。因为AI的程序都比较大。*

2. 开发板上两个按键是什么意思？

*答：RESET是重新启动程序，BOOT是芯片启动模式（长按着上电进入下载程序的模式，由于开发板有烧录电路，因此不用这样给开发板烧录）。*

3. Monitor报这样的错是什么意思？![image-20220121115956211](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220121115956211.png)

![image-20220121115937203](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220121115937203.png)

*答：正常。可以把整个大项目在VSCode中打开，搜索这个错误，发现是正常工作会显示的。*

4. 如何在网页WIFI显示输出？

*答：在menuconfig中配置WIFI SSID和密码，monitor中会输出http地址，复制到网页中即可查看*



build出错: rm -rf build/ sdkconfig sdkconfig.old

code ./ 在vscode中打开这个文件夹

