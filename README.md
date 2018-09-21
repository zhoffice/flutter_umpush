# flutter_umpush

友盟推送、友盟统计的 flutter 插件，使用公司帐号反复测试都是可用的。

这个插件是从我们公司的 cordova 插件分离出来的，集成到 flutter 之后，使用公司的帐号测试可使用，安卓和 ios 都没问题。
后来，担心泄漏公司帐号，我又重新建了一个项目。目前 flutter clean && flutter build apk && flutter build ios 都没问题。只是没有使用真实帐号测试过。

# 包括的功能

1. 友盟统计：已经集成进来了，但是各种友盟的 api 没封装，我们公司之前是 cordova 的项目，只要求统计到设备信息就可以了，没有“埋点的需求”；

2. 友盟推送：支持小米、华为、魅族的离线唤醒功能，亲测可用。从友盟后台推送的时候，需要勾选“MIUI、EMUI、Flyme 系统设备离线转为系统下发”，填写打开指定页面是：com.github.flutterumpush.UmengOtherPushActivity，100 个放心，本人反复测试过，没问题的。

3. 由于我们之前是基于 cordova 的项目，不需要 tags，我们只需要根据推送的 url 跳转到指定的界面而已，所有很多功能我没封装。

# 安卓平台需要注意一下

1. so 文件只能使用 armeabi-v7a
   ndk {
   moduleName "app"
   abiFilters 'armeabi-v7a'//,'armeabi','arm64-v8a'
   }
2. 仿照 example/android/app/AndroidManifest.xml，把友盟帐号填写上去；

# ios 平台

1. 工程需要勾中 Remote notification 和 Push Notifications，这样才能支持；
2. 友盟推送需要在 adhoc 或 Distribution 环境下才可以收到推送；

# 安卓离线唤醒的原理（其他中间商的估计也要这么）

1. 把信息发给友盟后台，后台再转发给厂商的后台，厂商再把信息推送给手机，由于是厂商的通道，所以绝对能通知到；
2. 用户点击消息后，由于指定唤醒的界面是 com.github.flutterumpush.UmengOtherPushActivity.java，而不是 MainActivity.java，所以 flutter 和 dart 都没初始化，但是 UmengApplication.java 就初始化了，这是友盟相关 Token 已经生成，然而，由于 flutter 没有初始化，导致无法执行 onToken、onMessage 函数，所以我把 token、message 都缓存到 SharedPreferences。然后根据 URL_SCHEME 唤醒自己；
3. 这时候才真正实例化 MainActivity.java，也就是初始化 flutter 和 dart，等初始化完毕，在 configure 函数把 SharedPreferences 的东西取出来，执行 onToken、onMessage。

## Getting Started

For help getting started with Flutter, view our online
[documentation](https://flutter.io/).

For help on editing plugin code, view the [documentation](https://flutter.io/developing-packages/#edit-plugin-package).
