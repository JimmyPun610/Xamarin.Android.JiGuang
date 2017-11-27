# XamarinAndroidJiGuangPushNotification
This is JiGuang Push notification SDK in Xamarin Android

Compile file as
https://github.com/JimmyPun610/XamarinAndroidJiGuangPushNotification/raw/master/JPushAndroidLibrary.dll

Based on JiGuang SDK v3.1.0
https://www.jiguang.cn/push

Guide : 
1. Reference the dll to your Xamarin Android project

2. Create a class inherit PushRecevier
```C#
using Android.App;
using Android.Content;
using Android.OS;
using CN.Jpush.Android.Service;
using CN.Jpush.Android.Api;

namespace MobileApp.Droid.NotificationHandler
{
    [BroadcastReceiver]
    [IntentFilter(new string[] { "cn.jpush.android.intent.REGISTRATION" }, Categories = new string[] { "YOUR_APP_ID" })]
    [IntentFilter(new string[] { "cn.jpush.android.intent.MESSAGE_RECEIVED" }, Categories = new string[] { "YOUR_APP_ID" })]
    [IntentFilter(new string[] { "cn.jpush.android.intent.NOTIFICATION_RECEIVED" }, Categories = new string[] { "YOUR_APP_ID" })]
    [IntentFilter(new string[] { "cn.jpush.android.intent.NOTIFICATION_OPENED" }, Categories = new string[] { "YOUR_APP_ID" })]
    [IntentFilter(new string[] { "cn.jpush.android.intent.CONNECTION" }, Categories = new string[] { "YOUR_APP_ID" })]
    class NotificationReceiver : PushReceiver
    {
        public override void OnReceive(Context context, Intent intent)
        {
			      base.OnReceive(context, intent);
            if (intent.Action == JPushInterface.ActionNotificationOpened)
            {
                //When user tap the notification on notification center
                Bundle bundle = intent.Extras;
                string jsonData = bundle.GetString(JPushInterface.ExtraExtra);
            }
            if(intent.Action == JPushInterface.ActionRegistrationId)
            {
                //Only call when first launch, get the registrationID
                string regID = JPushInterface.GetRegistrationID(context);
            }
       
        }
    }
}
```
3. Push these things to your AndroidManifest.xml
```XML
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <!--JPush Required-->
  <permission
    android:name="YOUR_APP_ID.permission.JPUSH_MESSAGE"
    android:protectionLevel="signature" />
  <uses-permission android:name="YOUR_APP_ID.android.permission.JPUSH_MESSAGE" />
  <uses-permission android:name="android.permission.RECEIVE_USER_PRESENT" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.READ_PHONE_STATE" />
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
  <uses-permission android:name="android.permission.VIBRATE" />
  <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="android.permission.WRITE_SETTINGS" />
  <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <application android:label="MobileApp.Android" android:icon="@drawable/icon">
	
    <!-- since 3.0.9 Required SDK-->
    <provider
        android:authorities="YOUR_APP_ID.DataProvider"
        android:name="cn.jpush.android.service.DataProvider"
        android:exported="true"
        />
	
   <!-- Required SDK 核心功能-->
    <!-- 可配置android:process参数将PushService放在其他进程中 -->
    <service
        android:name="cn.jpush.android.service.PushService"
        android:enabled="true"
        android:exported="false" >
      <intent-filter>
        <action android:name="cn.jpush.android.intent.REGISTER" />
        <action android:name="cn.jpush.android.intent.REPORT" />
        <action android:name="cn.jpush.android.intent.PushService" />
        <action android:name="cn.jpush.android.intent.PUSH_TIME" />
      </intent-filter>
    </service>

    <!-- since 1.8.0 option 可选项。用于同一设备中不同应用的JPush服务相互拉起的功能。 -->
    <!-- 若不启用该功能可删除该组件，将不拉起其他应用也不能被其他应用拉起 -->
    <service
        android:name="cn.jpush.android.service.DaemonService"
        android:enabled="true"
        android:exported="true">
      <intent-filter >
        <action android:name="cn.jpush.android.intent.DaemonService" />
        <category android:name="YOUR_APP_ID.android"/>
      </intent-filter>
    </service>

    <!-- Required SDK核心功能-->
    <receiver
        android:name="cn.jpush.android.service.PushReceiver"
        android:enabled="true" >
      <intent-filter android:priority="1000">
        <action android:name="cn.jpush.android.intent.NOTIFICATION_RECEIVED_PROXY" />
        <category android:name="YOUR_APP_ID"/>
      </intent-filter>
      <intent-filter>
        <action android:name="android.intent.action.USER_PRESENT" />
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
      </intent-filter>
      <!-- Optional--> 
      <intent-filter>
        <action android:name="android.intent.action.PACKAGE_ADDED" />
        <action android:name="android.intent.action.PACKAGE_REMOVED" />
        <data android:scheme="package" />
      </intent-filter>
    </receiver>

    <!-- Required SDK核心功能-->
    <activity
        android:name="cn.jpush.android.ui.PushActivity"
        android:configChanges="orientation|keyboardHidden"
        android:theme="@android:style/Theme.NoTitleBar"
        android:exported="false" >
      <intent-filter>
        <action android:name="cn.jpush.android.ui.PushActivity" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>
    </activity>
    <!-- SDK核心功能-->
    <activity
        android:name="cn.jpush.android.ui.PopWinActivity"
        android:configChanges="orientation|keyboardHidden"
        android:exported="false"
        android:theme="@android:style/Theme.NoTitleBar">
      <intent-filter>
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>
    </activity>

    <!-- Required SDK核心功能-->
    <service
        android:name="cn.jpush.android.service.DownloadService"
        android:enabled="true"
        android:exported="false" >
    </service>

    <!-- Required SDK核心功能-->
    <receiver android:name="cn.jpush.android.service.AlarmReceiver" />

    <!-- Required since 3.0.7 -->
    <!-- 新的tag/alias接口结果返回需要开发者配置一个自定的广播 -->
    <!-- 该广播需要继承JPush提供的JPushMessageReceiver类, 并如下新增一个 Intent-Filter -->
    <!--<receiver
    android:name=".JPushReceiver"
    android:enabled="true" >
      <intent-filter>
        <action android:name="cn.jpush.android.intent.RECEIVE_MESSAGE" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>
    </receiver>-->

     <!--User defined. 用户自定义的广播接收器-->
    <!--<receiver
        android:name="PushReceiver"
        android:enabled="true">
      <intent-filter>
        --><!--Required 用户注册SDK的intent--><!--
        <action android:name="cn.jpush.android.intent.REGISTRATION" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>
      <intent-filter>
        --><!--Required 用户接收SDK消息的intent--><!--
        <action android:name="cn.jpush.android.intent.MESSAGE_RECEIVED" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>
      <intent-filter>
        --><!--Required 用户接收SDK通知栏信息的intent--><!--
        <action android:name="cn.jpush.android.intent.NOTIFICATION_RECEIVED" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>
      <intent-filter>
        --><!--Required 用户打开自定义通知栏的intent--><!--
        <action android:name="cn.jpush.android.intent.NOTIFICATION_OPENED" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>
      <intent-filter>
        --><!--接收网络变化 连接/断开 since 1.6.3--><!--
        <action android:name="cn.jpush.android.intent.CONNECTION" />
        <category android:name="YOUR_APP_ID" />
      </intent-filter>

    </receiver>-->

    <!-- Required. For publish channel feature -->
    <!-- JPUSH_CHANNEL 是为了方便开发者统计APK分发渠道。-->
    <!-- 例如: -->
    <!-- 发到 Google Play 的APK可以设置为 google-play; -->
    <!-- 发到其他市场的 APK 可以设置为 xxx-market。 -->
    <!-- 目前这个渠道统计功能的报表还未开放。-->
    <meta-data android:name="JPUSH_CHANNEL" android:value="developer-default"/>
    <!-- Required. AppKey copied from Portal -->
    <meta-data android:name="JPUSH_APPKEY" android:value="YOUR_JPUSH_APP_KEY"/>
  </application>
  ```
3. Add these code to MainActivity.cs
```C#
  protected override void OnCreate(Bundle bundle)
        {  
            base.OnCreate(bundle);
            initPushNotification();
            global::Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication(new App());
        }
    private void initPushNotification()
        {
            IntentFilter filter = new IntentFilter();
            filter.AddAction(JPushInterface.ActionNotificationOpened);
            filter.AddAction(JPushInterface.ActionNotificationReceived);
            filter.AddAction(JPushInterface.ActionMessageReceived);
            filter.AddAction(JPushInterface.ActionRegistrationId);
            filter.AddAction(JPushInterface.ActionConnectionChange);
            NotificationReceiver receiver = new NotificationReceiver();
            RegisterReceiver(receiver, filter);
            JPushInterface.SetDebugMode(true);
            JPushInterface.Init(this.ApplicationContext);
        }
```
