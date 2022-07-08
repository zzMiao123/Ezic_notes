# 2.15工作日报

| 内容             | 时间                |
| ---------------- | ------------------- |
| 调试电机驱动     | 14:00-16:00         |
| 了解rivz使用     | 16:00-17:00         |
| 更换驱动板调试   | 17:00-20:00         |
| 调试四轮麦轮移动 | 20:00-23:00         |
|                  | 10:35上班/23:10下班 |



步进电机需要缓慢从0加速

尝试改成s3程序

* 反复重启：
* ESP-ROM:esp32s3-20210327
  Build:Mar 27 2021
  rst:0x8 (TG1WDT_SYS_RST),boot:0x8 (SPI_FAST_FLASH_BOOT)
  Saved PC:0x403786d0
  0x403786d0: _xt_panic at /home/maoyuxuan/esp/esp-idf/components/esp_system/port/arch/xtensa/panic_handler_asm.S:28

换新驱动板：DRV8825

https://www.pololu.com/product/2133

![img](https://a.pololu-files.com/picture/0J4232.600.png?f2f6269e0a80c41f0a5147915106aa55)

| MODE0 | MODE1 | MODE2 | Microstep Resolution |
| :---- | :---- | :---- | :------------------- |
| Low   | Low   | Low   | Full step            |
| High  | Low   | Low   | Half step            |
| Low   | High  | Low   | 1/4 step             |
| High  | High  | Low   | 1/8 step             |
| Low   | Low   | High  | 1/16 step            |
| High  | Low   | High  | 1/32 step            |
| Low   | High  | High  | 1/32 step            |
| High  | High  | High  | 1/32 step            |

注意：第四个轮子驱动板高-高-低对应1/16，其余三块都是低-低-高

接线有误