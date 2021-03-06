# 2.14工作日报

| 内容                      | 时间                    |
| ------------------------- | ----------------------- |
| 了解turtlesim节点控制方法 | 14:00-14:30             |
| 驱动电机                  | 14:30-17:30             |
| 将电机联合到原程序        | 17:30-18:30;19:30-20:30 |
| 调试合程序                | 20:30-23:00             |
|                           | 14:10上班/23:10下班     |

## turtlesim

![image-20220214142208530](/home/maoyuxuan/.config/Typora/typora-user-images/image-20220214142208530.png)

* key_control可以把创建的publisher删了
* echo不到rotate_absolute两个话题
  * 可能是服务？或者其他通信方式
    * 补一下服务的知识
* 似乎没法显示micro-ros的节点?

小乌龟键盘控制节点，提供两种可选的控制方式。

- 方向键，通过话题(Topic)控制小乌龟的（直接发送移动话题）
- 绝对旋转，则是采用动作(Action）来控制的小乌龟



## 驱动电机

参考资料：http://www.taichi-maker.com/homepage/reference-index/motor-reference-index/arduino-a4988-nema-stepper-motor/

### 驱动芯片

![A4988电机驱动板引脚说明](http://www.taichi-maker.com/wp-content/uploads/2018/07/A4988-Pin-Layout.jpg)



**VMOT** – 电机电源正极（可用电源电压为8V ~ 35V）注：此引脚用于连接为电机供电的电源
**GND** – 电机电源接地
**2B, 2A** – 电机绕组2控制引脚  自尊自责自最早在
**1A, 1B** – 电机绕组1控制引脚
**VDD** – 逻辑电源正极（3 – 5.5伏）注：此引脚用于为A4988电机驱动板供电
**GND** – 逻辑电源接地

**ENABLE** – 使能引脚（**低电平有效**）
引脚说明：此当此引脚为低电平时，A4988才能进行电机驱动工作，当该引脚为高电平，A4988将不会进行电机驱动工作。如果该引脚悬空，则A4988默认为使能状态。即该引脚没有连接任何电平时，A4988可以正常工作。
**MS1, MS2, MS3** – 驱动模式引脚
引脚说明：这三个引脚控制A4988微步细分驱动模式。通过这三个引脚的逻辑电平，我们可以调整A4988驱动电机模式为全、半、1/4、1/8 及 1/16 步进模式。上图中右侧的表格里有具体如何调节这三个引脚电频以及A4988在不同的电平组合下的驱动模式。表格中“L”代表低电平，“H”代表高电平。“FULL”为全步进，“HALF”为半步进，“QUATER”为1/4步进, “EIGHTH”为1/8步进, “SIXTEENTH”为1/16步进。**当MS1, MS2, MS3这几个引脚悬空时，A4988默认为全步进电机驱动模式。**
**RESET** – 复位引脚
引脚说明：该引脚为低电平有效，即当该引脚为低电平时，A4988将复位。如果该引脚悬空，则A4988默认为高电平。即该引脚没有连接任何电平时，A4988可以正常工作。
**SLEEP** – 睡眠引脚
引脚说明：当该引脚连接电平为低电平时，A4988将进入低能耗睡眠状态，即消耗最小的电能。如果无需使用SLeep功能，则可以将SLEEP引脚与RESET引脚连接，则A4988将持续保持正常能耗状态而不会进入低能耗状态。
**STEP** – 步进引脚
引脚说明：此引脚用于通过Arduino等微控制器向A4988发送脉冲控制信号，A4988接收到此信号后，会根据 MS1, MS2 和 MS3引脚控制电机运转。
**DIR** – 方向引脚
引脚说明：通过此引脚可以调整A4988控制电机运行方向。当此引脚为低电平，A4988将控制电机顺时针旋转。高电平则逆时针旋转。

![Arduino控制Nema-17步进电机](http://www.taichi-maker.com/wp-content/uploads/2018/11/arduino-stepper-motor.png)

### Vref电压调节

所谓Vref电压调节，就是通过A4988上的电位器旋钮调节Vref参考电压。该电压将直接影响到步进电机在工作时流过线圈的电流强度。**请注意，这一步操作非常重要，如果忽略这一步有可能会产生电机损坏的后果。**

A4988 Vref参考电压计算公式：

**Vref = Imax X Rcs X 8 = 0.64** 

**Rcs**: 市面上A4988模块上的Rcs电阻值一般有3种类型，0.05 欧姆, 0.1 欧姆或0.2 欧姆。大部分A4988模块顺时针旋转电位器可调大Vref，逆时针旋转电位器调小Vref。

**Vref**: 电位器金属旋钮和GND之间的电压即为Vref。可使用小改锥旋转电位器旋钮从而改变Vref。（如下图所示）

**Imax**：步进电机工作时，线圈允许流过的最大电流

本电机额定电流/相：0.8A

本驱动板标号R100=0.1欧姆

测得0.61V，已调好

检查接线



### 控制代码

arduino示例：

> // 定义电机控制用常量
>
>  
>
> // A4988连接Arduino引脚号
>
> const int dirPin = 2; // 方向引脚
>
> const int stepPin = 3; // 步进引脚
>
>  
>
> // 电机每圈步数
>
> const int STEPS_PER_REV = 200;
>
>  
>
> void **setup**() {
>
>  
>
>  // Arduino控制A4988步进和方向的引脚为输出模式
>
>  **pinMode**(stepPin,OUTPUT); 
>
>  **pinMode**(dirPin,OUTPUT);
>
> }
>
> void **loop**() {
>
>  
>
>  // 设置电机顺时针旋转
>
>  **digitalWrite**(dirPin,LOW); 
>
>  
>
>  // 电机慢速旋转
>
>  **for**(int x = 0; x < STEPS_PER_REV; x++) {
>
>   **digitalWrite**(stepPin,HIGH); 
>
>   **delayMicroseconds**(2000); 
>
>   **digitalWrite**(stepPin,LOW); 
>
>   **delayMicroseconds**(2000); 
>
>  }
>
>  
>
>  // 等待一秒
>
>  **delay**(1000); 
>
>  
>
>  // 设置电机逆时针旋转
>
>  **digitalWrite**(dirPin,HIGH);
>
>  
>
>  // 电机快速旋转
>
>  **for**(int x = 0; x < (STEPS_PER_REV * 2); x++) {
>
>   **digitalWrite**(stepPin,HIGH);
>
>   **delayMicroseconds**(1000);
>
>   **digitalWrite**(stepPin,LOW);
>
>   **delayMicroseconds**(1000);
>
>  }
>
>  
>
>  // 等待一秒
>
>  **delay**(1000);
>
> }

复制一个blink工程

* 清除build (fullclean)
* 改名、改CmakeLIst
* 重新set-target build

由于只需要一个使能口和一个方向口，直接用blink测试

* 先接二极管，不亮
  * 检查下接口对不对
    * example设置灯为gpio控制
    * 设置接口
* 亮了
* 电机有微弱的运动
  * 可能是电池电压不够
  * 可能是接口电压不够
    * 万用表测一下
      * 驱动板电压4.88V
      * 电源电压11.56V左右
      * 步进信号3.3V-0跳动
  * 步进信号直接接高电平
    * 有点转
    * 时间太短了？
  * 可能是杜邦线接触不良

直接用高低电平测试

重新用新的杜邦线连接

有没有一种可能：步进电机驱动就是上一次电步进一格的

* 正解
* 提高频率看看
  * 解决

可以用这个驱动板控制吗？

* 不方便，驱动板有自己的芯片

## MicroROS 键盘控制电机小车

合并程序：key_control_motor

由设定速度计算脉冲频率

在for循环控制电平高低

问题：电机一卡一卡的，运动时间不足

* 可能速度太快了
  * 速度减慢到1/5
    * 没变化

* 加大细分
  * 不行

* 是轮子卡住了吗？
  * 拆掉轮子看看
    * 不是的
* 猜测可能是电池电量不足
  * 无法重现单电机的驱动
  * 需要购买锂电池、电流计、充电器
  * 可以找小飞机开发者要
