---
layout: post
title:  "树莓派(RPi)访问串口的方法"
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

## 串口别名

    /dev/seiral0 -> /dev/ttyS0
    /dev/seiral1 -> /dev/ttyAMA0

## Pi 用 serial0 作为登陆终端

可以配置 pi 不使用串口输出终端消息，如下：

    sudo raspi-config
    然后选择 'Advanced Options' > 'Disable Shell and Kernel messages on seiral connection'

这样就可以独占 serial0 串口了。

## 串口不稳定的问题

当使用 RPi3 的 /dev/serial0 串口设备时，发现读取数据不稳定，现象是只有少数数据读取正确，其他的字节会出现读到错误数值的问题。
这是为什么呢？原来是因为 RPi3 的 serial0 指向的是 ttyS0 是一个 GPIO 的串口而不是硬件串口，需要CPU频率稳定，如果对 RPi3 
进行了超频设置，那么串口通信的频率将会不稳定，导致传输出错。

解决办法是设置 core_freq 为一个确定的值，并且超频到1.2GHz。方法是编辑 /boot/config.txt，添加下面的内容

    force_turbo=1
    arm_freq=1200
    core_freq=250

参考的是这篇[帖子][pi3 core UART]。更进一步，如果你想提高 core_freq 那么你还得改 UART divider，这需要改
boot loader 的参数。

# 用 python 访问树莓派的 UART 端口

## 用 WiringPi 访问串口

WiringPi-Python 访问串口的方法
    
    import wiringpi
    
    wiringpi.wiringPiSetup() # For sequential pin numbering, one of these MUST be called before using IO functions
    # OR
    wiringpi.wiringPiSetupSys() # For /sys/class/gpio with GPIO pin numbering
    # OR
    wiringpi.wiringPiSetupGpio() # For GPIO pin numbering
    
    serial = wiringpi.serialOpen('/dev/ttyAMA0',9600) # Requires device/baud and returns an ID
    wiringpi.serialPuts(serial,"hello")
    data = wiringpi.serialGetchar(serial)   # if no data, block for 10 seconds then return -1
    wiringpi.serialClose(serial) # Pass in ID


[WiringPi-Python](https://github.com/WiringPi/WiringPi-Python)

[WiringPi homepage](http://wiringpi.com/)

[WiringPi 串口文档](http://wiringpi.com/reference/serial-library/)

## 用 PySerial 访问串口

安装：

    pip install wiringpi2

使用：

    import wiringpi2 as wiringpi
    wiringpi.wiringPiSetup()
    serial = wiringpi.serialOpen('/dev/ttyAMA0',9600)
    wiringpi.serialPuts(serial,'hello world!')
    
# GPIOZero 没有串口访问的相关方法

# RPIO

RPIO 居然也没有串口的访问支持。

[RPIO document](https://pythonhosted.org/RPIO/index.html)

# 参考

* [Configuring The GPIO Serial Port On Raspbian Jessie Including Pi 3](http://spellfoundry.com/2016/05/29/configuring-gpio-serial-port-raspbian-jessie-including-pi-3/)
* [Pi Pin Graphics](http://pinout.xyz/pinout/uart)
* [RPi Serial Connection](http://elinux.org/RPi_Serial_Connection)

[pi3 core UART]: https://www.raspberrypi.org/forums/viewtopic.php?f=72&t=138563&p=919875&hilit=pi+3+core+uart