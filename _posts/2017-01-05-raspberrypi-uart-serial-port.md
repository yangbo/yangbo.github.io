---
layout: post
title:  "树莓派(RPi)访问串口使用方法"
date:   2017-01-05 23:03:00
categories: RaspberryPi UART serail-port
---

# UART 简介

UART 即 Universal Asynchronous Receiver Transmitter，是一种不用时钟同步进行串行通讯的协议。两个设备之间不需要同步时钟，而是事先约定一个时钟频率并通过"start-bits"来标识一个"Word"的开始。

树莓派的 UART 端口使用的是v3.3的TTL电平，注意不是 RS232的 +/-12V，如果直接将 Pi 的 UART 管脚接到 PC 的RS232串口或者 Arduino 的 5v 串口上，会导致芯片冒烟（听说但没见过）。

连线方法是将 RPi 的 Rx 接到另外一个设备的 Tx 管脚，将 Pi 的 Tx 接到另一个设备的 Rx 管脚，就是

    Rx -> Tx，Tx -> Rx

# 树莓派(RPi)的 UART 设备文件

树莓派上的 UART 设备文件有两种，一个是硬件串口(/dev/ttyAMA0)一个是软件串口(/dev/ttyS0)。软件串口设备的时钟使用的是CPU时钟，CPU忙时会影响时钟的稳定性。

# 串口别名

    /dev/seiral0 -> /dev/ttyS0
    /dev/seiral1 -> /dev/ttyAMA0

# Pi 用 serial0 作为登陆终端

可以配置 pi 不使用串口输出终端消息，如下：

    sudo raspi-config
    然后选择 'Advanced Options' > 'Disable Shell and Kernel messages on seiral connection'

这样就可以独占 serial0 串口了。

# 用 python 访问树莓派的 UART 端口

## 用 WiringPi 访问串口

## 用 PySerial 访问串口

安装：

    pip install wiringpi2

使用：

    import wiringpi2 as wiringpi
    wiringpi.wiringPiSetup()
    serial = wiringpi.serialOpen('/dev/ttyAMA0',9600)
    wiringpi.serialPuts(serial,'hello world!')
    
# 参考

* [Configuring The GPIO Serial Port On Raspbian Jessie Including Pi 3](http://spellfoundry.com/2016/05/29/configuring-gpio-serial-port-raspbian-jessie-including-pi-3/)
* [Pi Pin Graphics](http://pinout.xyz/pinout/uart)
* [RPi Serial Connection](http://elinux.org/RPi_Serial_Connection)