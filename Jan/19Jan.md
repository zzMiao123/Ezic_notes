# 1.19工作日报
| 内容                                              | 时间        |
| ------------------------------------------------- | ----------- |
| 安装VS Code IDE                                   | 11:00-12:00 |
| 学习用vscode烧录blink并监视                       | 14:00-15:00 |
| 了解idf debugger                                  | 15:00-15:30 |
| 尝试用vscode建立新的工程                          | 15:00-16:00 |
| 配置Eclipse                                       | 16:00-17:00 |
| 学习用Eclipse烧录、监视                           | 17:00-17:30 |
| 读blink_example                                   | 17:30-18:00 |
| 看相关项目(moonlight)、比赛(中国青少年机器人大赛) | 18:00-19:00 |
| 整理笔记，梳理简单工程创建流程                    | 19:00-20:30 |

***

### 一、VS Code IDE

[VSCode参考资料目录](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/get-started/vscode-setup.html)

[ESP-IDF for VSCode使用指南](https://github.com/espressif/vscode-esp-idf-extension/blob/master/docs/tutorial/toc.md)

[Terminal输出解释](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32s3/api-reference/system/log.html)

| 操作                | 详细步骤                                                     |
| ------------------- | ------------------------------------------------------------ |
| 安装VSCode          | 下载VSCode                                                   |
| 安装ESP-IDF         | 在拓展市场搜索`esp-idf`并安装                                |
|                     | `F1`->`ESP-IDF: Configure ESP-IDF extension`                 |
|                     | USE EXISTING SETUP                                           |
|                     | *修改python路径为python3，File->Add Floder to Workspace*     |
|                     | 安装成功                                                     |
| 复制一个example工程 | `ESP-IDF: Show Examples Projects`                            |
|                     | choose idf path                                              |
|                     | choose a example -> create project using example `xxx`       |
|                     | choose the copy path                                         |
| Build               | 打开工程                                                     |
|                     | set device target to `esp32s3` (底栏)                        |
|                     | ESP-IDF: Build your project                                  |
| Flash               | set device port                                              |
|                     | ESP-IDF: Flash (UART) your project <br />or ESP-IDF: Flash (with JTag) |
| Monitor             | 底栏                                                         |
| Debug               | ESP-IDF: Select OpenOCD Board Configuration                  |
|                     | F5                                                           |
|                     | View -> Output -> OpenOCD.                                   |
|                     | **报错**：需要用调试器，小板子没引出接口，以后需要再学习     |
|                     | 先用打印输出，debugger先不用                                 |
| 创建一个新工程      |                                                              |



### 二、Eclipse

[Eclipse教程](https://github.com/espressif/idf-eclipse-plugin/blob/master/README_CN.md)

| 操作           | 详细步骤                                                |
| -------------- | ------------------------------------------------------- |
| 安装Eclipse    | 解压后双击exe安装                                       |
|                | 确认依赖资源                                            |
| 安装插件       | Installing IDF Plugin using update site URL             |
| 安装ESP-IDF    | 无反应                                                  |
| 安装IDF Tools  | `Espressif` > `ESP-IDF Tools Manager` > `Install Tools` |
|                | provide paths                                           |
| 创建一个新项目 | `File` > `New` > `Espressif IDF Project`                |
|                | 可以选择是否从模板创建                                  |
|                | 确认芯片                                                |
| Build          | 第一个图标                                              |
| 编译并烧录     | 第二个图标                                              |
| 监视           | 打开Terminal                                            |
| 设置           | 双击sdkconfig文件                                       |
|                | `Ctrl`+`S` 保存                                         |

![img](https://github.com/espressif/idf-eclipse-plugin/raw/master/docs/images/9_cmake_build.png)



### 三、手动安装Espressif IoT Development Framework

| 操作             | 步骤                                                         |
| ---------------- | ------------------------------------------------------------ |
| 下载依赖资源     | sudo apt-get install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0 |
| 下载ESP-IDF      | mkdir -p ~/esp<br/>cd ~/esp<br/>git clone --recursive https://github.com/espressif/esp-idf.git |
| 下载相关工具     | cd ~/esp/esp-idf<br/>./install.sh esp32s3                    |
|                  | cd ~/esp/esp-idf<br/>export IDF_GITHUB_ASSETS="dl.espressif.com/github_assets"<br/>./install.sh |
| **设置环境变量** | . $HOME/esp/esp-idf/export.sh                                |
| *或*             | *在`/etc/bash.bashrc`中加入一行`alias get-idf='. $HOME/esp/esp-idf/export.sh'`以创建别名后重启终端* |
|                  | **get-idf** （每个终端都要输一次）                           |
| 且               | export IOT_SOLUTION_PATH=~/esp/esp-iot-solution              |
| *或*             | *在`/etc/bash.bashrc`中加入一行`alias get-solution='export IOT_SOLUTION_PATH=~/esp/esp-iot-solution'`以创建别名* |



### 四、创建一个简单的工程

*编译环境选择：~~VS Code &~~ ESP-IDF*

1. 从hello_world创建工程

cp /home/maoyuxuan/esp/esp-idf/examples/get-started/hello_world my_new_project2 -r

2. get-idf

2. get-slt (使用esp-iot-solution需要在CMakeLists中加入`include($ENV{IOT_SOLUTION_PATH}/component.cmake)`)

3. cd my_new_project2

4. idf.py set-target esp32s3

5. idf.py -p /dev/ttyUSB1 flash monitor

   *Ctrl + ]* 退出



### 五、设置菜单

[设置选项解释](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32s3/api-reference/kconfig.html)

* Build type: 构建二进制文件的方式
* Application manager
  * 时间戳
* Bootloader config
  * Bootloader是嵌入式系统在加电后执行的第一段代码，在它完成CPU和相关硬件的初始化之后，再将操作系统映像或固化的嵌入式应用程序装载到内存中然后跳转到操作系统所在的空间，启动操作系统运行。
* Security features
* Boot ROM Behavior
* Serial flasher config
  * Flash SPI
  * 自动复位
  * 设置波特率
* Partition Table
  * 分区表决定了应用程序、数据和其他资源预计会在哪里找到。
* **Compiler options**
  * （每个example也有设置）
  * 编译器优化级别
  * 替换二进制文件中的ESP-IDF和项目路径
  * 发现那些试图写入字符串常量的代码。
* Component config
  * 蓝牙
  * 常见的ESP相关
    * 主频
    * 指令缓存
    * 外部、SPI连接的RAM
    * 驱动设置
  * ESP系统设置
  * 以太网
  * 硬件设置
  * HTTP服务器
  * LCD和触摸面板
  * 电源管理
  * 无线网络



### 六、工程结构

ESP-IDF 中的所有软件均以“组件”的形式提供，比如操作系统、网络协议栈、Wi-Fi 驱动程序、以及 HTTP 服务器等中间件等等。在这种基于“组件”的架构下，你可以轻松使用更多自己研发或第三方提供的组件。

<img src="https://docs.espressif.com/projects/espressif-esp-moonlight/zh_CN/latest/_images/app_structure.png" alt="Application’s Structure" style="zoom:50%;" />

- CMakeLists.txt 和 Makefile 文件，用于控制工程的编译过程。
- components 文件夹，包含该项目的组件文件夹。
- main 文件夹，一个特殊的组件，默认编译这里面的代码，应用程序必须包含一个 **main** 组件。
- 一个可选的 *sdkconfig.defaults* 文件，存放应用程序默认的 SDK 配置。

在编译完成后会生成以下文件:

- build 文件夹，存放编译输出的文件。
- sdkconfig 文件，定义项目的所有配置。这个文件无需手动修改，编译时会自动从你在 menuconfig 中的设置来更新该文件。

### 七、Flash分区

<img src="https://docs.espressif.com/projects/espressif-esp-moonlight/zh_CN/latest/_images/flash_partitions_intro.png" alt="Flash Partitions" style="zoom: 33%;" />

通过分区表将 flash 划分为多个逻辑分区

创建此分区文件后，我们还需在 menuconfig 中配置使用该自定义分区，而非默认分区。你可以在 `Partition Table ---> Partition Table` 中选择 `Custom partition table CSV`，同时在下面指定分区表文件名。 

固件升级：

- 在 menuconfig 中的 `Example Configuration ---> firmware upgrade url endpoint` 进行配置。示例中使用的是本地的 http server，所以这里的 IP 地址需改成本机的。
- 使用 `esp_http_client_config_t` 配置 OTA 升级源，包括升级地址的 URL，用于验证服务器的 CA 证书（升级从此服务器处获取）。
- 然后执行 `esp_https_ota()` API 启动固件升级，固件升级成功后将设备重启。



*OTA 升级过程*：

![OTA Workflow](https://docs.espressif.com/projects/espressif-esp-moonlight/zh_CN/latest/_images/ota_workflow.png)
