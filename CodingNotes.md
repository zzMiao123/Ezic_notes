[风格规范](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/contribute/style-guide.html)



* 头文件加斜杠：头文件路径

* 修改完了要保存，编译的才是新程序

* Guru Meditation Error: Core  1 panic'ed (StoreProhibited). Exception was unhandled.
  * 修改分配内存从1024变为2048

* ％d整型输出，％ld长整型输出，

  ％o以八进制数形式输出整数，

  ％x以十六进制数形式输出整数，或输出字符串的地址。

  ％u以十进制数输出unsigned型数据(无符号数)。注意：%d与%u有无符号的数值范围，也就是极限的值，不然数值打印出来会有误。

  ％c用来输出一个字符，

  ％s用来输出一个字符串，

  ％f用来输出实数，以小数形式输出，默认情况下保留小数点6位。

  %.100f用来输出实数，保留小数点100位。

  ％e以指数形式输出实数

* Handle输入函数要引用地址

* **刚开始Task1的优先级为1，Task2优先级为2，后来Task1的优先级被设置为3**
  **但是在输出的时候直接输出111-222-111-222（并不是UP那样222-111-111-222）,但是查看优先级确实是3**

* ninja: build stopped: subcommand failed.
  ninja failed with exit code 1
  * 代码有语法错误

* vscode无法联想函数
  * 添加一些相关头文件
  * 软链接 命令行输ln -s ~/esp/esp-idf
  * 添加文件夹至工作目录

* 创建动态字符串
  * 定义char指针
  * **动态分配内存malloc(size);**
  * 设置内容snprintf（char*, size, "xxxx%d!\n",i）;
  * 打印：printf(char*)
  * **释放内存：free(char*)**

* micro ros 分支要选foxy版本

* build pingpong和blink程序都出错
  * get-ros2
  * 删掉micro-ros组件-还是报错
  * 更新idf和工具-还是报错
  * blink被污染,重新复制个新的

* build pingpong报错显示某个lib 找不到

  * 把idf中的mros component剪切到工程/componets/中
  * 某个C静态库编译时会出现比较慢的情况

* pingpong必须要ip

* 报错工具链不匹配

  * 可能是CmakeList里project名与工作空间名对不上
  * 关掉vscode终端，用shell打开运行

* flash连接出错：用其他设备占USB0口，换一个USB1口

* 存在RCSOFTCHECK(rcl_publish(&publisher, &msg, NULL));报未知错误终止程序的情况
  * 待解决错误

* support init 错误问题
  * 猜测可能是microros agent 有问题 尝试重新构建
    * 构建agent需要在microros_ws中build
  * **ip变了，重设agent IP即可**。。。

由于内存受限，嵌入式编程采用面向过程的编程（函数），不用面向对象的编程（封装类）

* rcl_pulish/init报错
  * 多重启几次就行了
  * 

* support init error

  * 没连上microros_agent      -----subscription_init error

  * mros 连的wifi与agent主机不在一个局域网内

* micro_ros_agent 正确输出

![image-20220210113005230](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220210113005230.png)

* shell一般不区分单引号和双引号；C里字符串不能用单引号表示；python里字符转义涉及到单双引号区分
* 双引号用来放字符串，单引号用来放字符，一般用双引号多

如果出错，首先检查命令有没有输错

* 添加外部的lib:
  * 下载lib
  * 在cmakelist extra components中添加路径
    * 要确保路径是正确的（能找得到的）！
  * 在main中include 包

Guru Meditation Error: Core  0 panic'ed (LoadProhibited). Exception was unhandled.

* 猜测可能是GoLinear参数未转成指针
* 对的

RCCHECK(rmw_uros_discover_agent(rmw_options));

* 

ROS2包编译过了没找到

* 手动编译之后，需要手动source工作空间的install目录。

ros命令行 字符串里不能有特殊符号

CmakeLists报错：

Make Error at /opt/ros/noetic/share/catkin/cmake/catkin_install_python.cmake:16 (message):
  catkin_install_python() only supports the PROGRAMS signature (not
  'PROGRAM').
Call Stack (most recent call first):
  packagebeginner/CMakeLists.txt:16 (catkin_install_python)

把

* catkin_install_python(PROGRAMS

    scripts/talker2.py

    scripts/listener2.py

    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}

  )

移到最后add部分
