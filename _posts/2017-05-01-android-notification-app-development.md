---
layout: post
title:  "Android消息推送系统开发"
date:   2017-05-01 10:36:00
categories: android push-message
---

# 背景

开发的一个应用，需要有消息通知功能，即当某事件发生时能及时通知到用户，这个系统因为是
个人使用，所以无法使用繁琐和昂贵的消息服务，例如短信、微信通知，所以选择使用Android
的消息推送方法来实现，但是免费的Android消息推送服务有繁琐的注册流程还有一些使用上的限制，
而我并不需要很复杂的消息推送功能，并且需要能快速实现，因此尝试自己搭建一个 MQTT 服务，
并自己开发一个Android客户端程序来接收消息通知，提醒用户进行相关操作。

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

- java client on [github](https://github.com/fusesource/mqtt-client)

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
* [Android推送实现](http://blog.csdn.net/xyz_lmn/article/details/7528671)
* [Android推送指南](http://blog.csdn.net/joshua_yu/article/details/6563587)

# android kvm linux installation

[speeding-up-the-android-emulator-on-intel](https://software.intel.com/en-us/android/articles/speeding-up-the-android-emulator-on-intel-architecture#_Toc358213272)

# Android Service Development

https://developer.android.com/guide/components/services.html
https://developer.android.com/reference/android/app/Service.html

注意：服务在其托管进程的主线程中运行，它既不创建自己的线程，也不在单独的进程中运行（除非另行指定）。 这意味着，如果服务将执行任何 CPU 密集型工作或阻止性操作（例如 MP3 播放或联网），则应在服务内创建新线程来完成这项工作。

## IntentService

每次 activity startService 时会创建一个新的 IntentService 对象，虽然服务只有一个，但是
服务对象却有多个，所以不同对象间的成员变量是不同的。

```
IntentService 每次 startService 会创建一个线程来处理，如果之前的 Intent 还没有处理完成，则
会将本次的Intent 放入一个消息队列，等待上一个 Intent 处理完毕后才继续执行新的Intent。
```
IntentService 可以用 setIntentRedelivery(boolean enabled)控制 onStartCommand() 的返回值。
如果 mRedelivery = false (默认) 那么 onStartCommand() 就返回 START_NOT_STICKY，即不会重新
投递 Intent。

如果 IntentService 很快执行结束，那么从手机上是看不到“服务”的，只会看到一个后台进程。

IntentService is a base class for Services that handle asynchronous requests (expressed as Intents) on demand. Clients send requests through startService(Intent) calls; the service is started as needed, handles each Intent in turn using a worker thread, and stops itself when it runs out of work.
This "work queue processor" pattern is commonly used to offload tasks from an application's main thread. The IntentService class exists to simplify this pattern and take care of the mechanics. To use it, extend IntentService and implement onHandleIntent(Intent). IntentService will receive the Intents, launch a worker thread, and stop the service as appropriate.

All requests are handled on a single worker thread -- they may take as long as necessary (and will not block the application's main loop), but only one request will be processed at a time.

综上所述，IntentService 只适合执行很快的后台任务，这些任务将会尽量在一个线程中执行，
任务执行结束后服务会自动退出。

Service 的 START_NOT_STICKY 选项表示：如果服务运行中途进程被kill，那么该服务不需要重新启动，
也就是说这个服务会自己以后重新执行，不需要 Service 来重新启动。

## 您应使用服务还是线程？
简单地说，服务是一种即使用户未与应用交互也可在后台运行的组件。 因此，您应仅在必要时才创建服务。

如需在主线程外部执行工作，不过只是在用户正在与应用交互时才有此需要，则应创建新线程而非服务。 例如，如果您只是想在 Activity 运行的同时播放一些音乐，则可在 onCreate() 中创建线程，在 onStart() 中启动线程，然后在 onStop() 中停止线程。您还可以考虑使用 AsyncTask 或 HandlerThread，而非传统的 Thread 类。如需了解有关线程的详细信息，请参阅进程和线程文档。

请记住，如果您确实要使用服务，则默认情况下，它仍会在应用的主线程中运行，因此，如果服务执行的是密集型或阻止性操作，则您仍应在服务内创建新线程。

## Service, IntentService, Thread 的区别和联系
* 服务可以接收 Intent 消息，而线程不能。
* 服务运行在 main thread 中，而线程则运行在独立的thread中。

## Android Constraints Layout
* [Android ConstraintLayout详解](http://www.jianshu.com/p/a8b49ff64cd3)
* [constraint layout](https://developer.android.com/training/constraint-layout/index.html)
* [constraint layout reference](https://developer.android.com/reference/android/support/constraint/ConstraintLayout.html)

[android-studio]: https://developer.android.com/studio/install.html
[connect network]: https://developer.android.com/training/basics/network-ops/connecting.html
[MQTT]: http://baike.baidu.com/link?url=Q60ei2-mhqhLqNixFrTfbo7mTaGtLYddPJp6rbefA3GGBYJLpdWH4NUodpnC4_-mWSz0FS5fZ2A7PI7FUN0qBq
[MQTT-Homepage]: http://mqtt.org
[wmqtt]: http://www-01.ibm.com/support/docview.wss?rs=171&uid=swg24006006
[MQTT Broker]: http://www.alphaworks.ibm.com/tech/rsmb
[MQTT API]: http://www-01.ibm.com/support/docview.wss?rs=171&uid=swg24006006
