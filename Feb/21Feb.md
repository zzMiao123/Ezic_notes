# 2.21工作日报

| 内容               | 时间                    |
| ------------------ | ----------------------- |
| 调试超声传感器驱动 | 14:00-15:00;15:30-16:30 |
| 了解hexa           | 15:00-15:30             |
| 找步进电机库       | 16:30-18:00;19:00-21:00 |
| 调试集成程序       | 21:00-22:30             |
| 应用舵机           | 22:30-23:00             |
|                    | 13:55上班/22:55下班     |



控制台一直报错：设备未响应（PING_TIMEOUT）

* 检查接线

* 检查程序用法

  * 看一下函数头
  * 看一下结构体对不对
    * 

* 看别人怎么用的

  * http://m.elecfans.com/article/1084205.html
    * 这个库不一样
  * https://blog.csdn.net/u011594289/article/details/121724665
    * 将代码在程序中实现看看

* 发现是init函数有问题，在程序中实现init功能，就可以正常运行了

新代码电机抖动

* 对比看看改动在哪
  * init放在外面
  * 加了timer
  * **因为多了printf**



用步进电机库

* esp-iot-solution(v1.1)/motor/stepper
  * idf v 4.0.1
    * git fetch
    * git checkout v4.0.3
    * get-idf
    * install.sh
    * get-idf
    * idf.py build 
      * '/home/maoyuxuan/esp/esp-idf/components/coap/libcoap/include/coap2' is not
          a directory.
* idf 自带a4988库
  * 放进工程里一直报错
    * 只能编译支持的芯片
    * 要把相应的组件放到components里
  * 编译示例工程也出错
    * 只能编译支持的芯片



改成支持s3的

* 验证了支持