---
layout: post
title:  "开发Android接收远程推送消息的应用"
date:   2017-05-01 10:36:00
categories: android push-message
---

# 安装 Android Studio

首先需要科学上网，然后从这里下载 [Android Studio][android-studio] 并按照安装文档进行安装。

看见安装过程中提示说可以通过 KVM 加速 Android 模拟器的运行速度，于是搜索一下安装方法，
参考文档见下方。

# 使用手机进行测试

我的手机是一部小米5S plus，要用来测试首先需要打开“开发者选项”中的“USB调试”，
之后就可以连接到电脑运行开发中的程序了。

## Android Studio 外观配置

在 Linux Ubuntu 下，选择 Android Studio 的 Look and Feel 为 "Gtk+"，字体设置为 20，
Color Schema 设置为 "Default"，key map 设置为 Eclipse 使用起来就比较顺手了。当然这是
因为我日常使用 Eclipse 开发的原因。

# Push 服务

Android 官方文档中没有关于 Push 服务的相关说明，只有一个[联网][connect network]的文档。

互联网上的 Push 服务提供商有：
* 百度云推送
* 极光推送
* 云巴 yunba.io
* [网易云信](http://netease.im)

## OpenSource 项目

### MQTT - MQ Telemetry transport
如果自己实现，可以搜索“如何实现 android 推送功能”。有人提到了 [MQTT 协议][MQTT] / [MQTT homepage][MQTT-Homepage]。

RabbitMQ 是支持 MQTT v3 的，参考[这里](https://www.rabbitmq.com/mqtt.html)。

### Openfire

- Openfire

	http://www.igniterealtime.org/projects/openfire/
	Openfire is a real time collaboration (RTC) server

- Android Push Notification

	https://sourceforge.net/projects/androidpn/

# 复现 demo 项目

参考 [blog](http://blog.csdn.net/xyz_lmn/article/details/7528671) 的“采用MQTT协议实现Android推送”实现一个 demo 推送android客户端。

先 clone git 项目: 

- android 客户端
  https://github.com/tokudu/AndroidPushNotificationsDemo，项目有点旧（7年前的东西）。
  wmqtt.jar 是IBM提供的MQTT协议的实现。你可以从如下[站点][wmqtt]下载它。

- Really Small Message Broker (RSMB) ，他是一个简单的[MQTT代理][MQTT Broker]

- php 消息发送程序
  https://github.com/tokudu/PhpMQTTClient
  
- java 的发送[客户端][MQTT API]
  
因为我使用 Android Studio 所以需要从 Eclipse 项目迁移过来，参考下面的文章迁移。
https://developer.android.com/studio/intro/migrate.html#eclipse

在迁移 Android 代码时，因为代码比较旧，所以需要改一下，主要是修改创建 Notification 的
方法.

## Android Notification 开发

得参考 [Android Notification 文档](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).

创建 Notification 的方法需要用 Builder :
{% highlight java linenos=table %}
private void showNotificationNew(String text) {
	NotificationCompat.Builder mBuilder =
			new NotificationCompat.Builder(this)
					.setSmallIcon(com.tokudu.demo.R.drawable.icon)
					.setContentTitle(NOTIF_TITLE)
					.setContentText(text);
    // Simply open the parent activity
    PendingIntent pi = PendingIntent.getActivity(this, 0,
            new Intent(this, PushActivity.class), 0);

    Notification notification = mBuilder.build();
    mNotifMan.notify(NOTIF_CONNECTED, notification);
}
{% endhighlight %}

## 安装 APK 失败的问题
提示卸载失败：

	$ adb shell pm uninstall com.ipandabot.investmessage
	DELETE_FAILED_INTERNAL_ERROR
	Error while Installing APKs

搜索blog发现需要“禁用小米优化”开关，问题的确可以解决。

## Android MQTT client 不能连接到 MQTT broker 的问题

连接时报告异常：android.os.NetworkOnMainThreadException

解决方案参考[文档](http://stackoverflow.com/questions/6343166/how-to-fix-android-os-networkonmainthreadexception)

采用比较粗暴的方法解决，即将创建连接的工作放到一个线程中去，如下：
{% highlight java linenos=table %}
	private synchronized void connect() {		
		log("Connecting...");
		// fetch the device ID from the preferences.
		final String deviceID = mPrefs.getString(PREF_DEVICE_ID, null);
		// Create a new connection only if the device id is not NULL
		if (deviceID == null) {
			log("Device ID not found.");
		} else {
            if (this.conThread == null) {
                log("创建新连接线程...");
                this.conThread = new Thread(new Runnable() {
                    @Override
                    public void run() {
                        try {
                            mConnection = new MQTTConnection(MQTT_HOST, deviceID);
                        } catch (MqttException e) {
                            log("Can not connect MQTT broker. MqttException: " + (e.getMessage() != null ? e.getMessage() : e.toString()));
                            if (isNetworkAvailable()) {
                                scheduleReconnect(mStartTime);
                            }
                        }
                    }
                });
                conThread.start();
                setStarted(true);
            } else {
                log("连接线程已经存在...");
            }
		}
	}
{% endhighlight %}

# 参考 Blog：
* [Android推送实现]:(http://blog.csdn.net/xyz_lmn/article/details/7528671)
* [Android推送指南]:(http://blog.csdn.net/joshua_yu/article/details/6563587)

# android kvm linux installation

[speeding-up-the-android-emulator-on-intel](https://software.intel.com/en-us/android/articles/speeding-up-the-android-emulator-on-intel-architecture#_Toc358213272)


[android-studio]: https://developer.android.com/studio/install.html
[connect network]: https://developer.android.com/training/basics/network-ops/connecting.html
[MQTT]: http://baike.baidu.com/link?url=Q60ei2-mhqhLqNixFrTfbo7mTaGtLYddPJp6rbefA3GGBYJLpdWH4NUodpnC4_-mWSz0FS5fZ2A7PI7FUN0qBq
[MQTT-Homepage]: http://mqtt.org
[wmqtt]: http://www-01.ibm.com/support/docview.wss?rs=171&uid=swg24006006
[MQTT Broker]: http://www.alphaworks.ibm.com/tech/rsmb
[MQTT API]: http://www-01.ibm.com/support/docview.wss?rs=171&uid=swg24006006
