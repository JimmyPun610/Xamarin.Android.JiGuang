# Xamarin.Android.JiGuang
This is JiGuang Push notification(極光推送) SDK in Xamarin Android

JiGuang Android SDK version v3.3.6

Product website : https://www.jiguang.cn/push

# Installataion 
```
Install-Package Xamarin.Android.JiGuang -Version 3.3.6
```

# Usage
1. Create Custom JCommonService class, setup the service name, replace {ProjectNamespace} to your project namespace like "XF.JiGuangPush.Sample.Droid.Notificaiton"
```c#
[Service(Name = "{ProjectNamespace}.MyJCommonService")]
public class MyJCommonService : JCommonService
{
}
```

2. Create Custom JPushMessageReceiver class, replace {ProjectNamespace} to your project namespace like "XF.JiGuangPush.Sample.Droid.Notificaiton". You can also override the methods for your own actions.
```C#
[BroadcastReceiver(Name = "{ProjectNamespace}.MyJPushMessageReceiver")]
public class MyJPushMessageReceiver : XFJiGuangPushMessageReceiver
{
     public override void OnRegister(Context p0, string p1)
        {
            base.OnRegister(p0, p1);
        }

        public override void OnConnected(Context p0, bool p1)
        {
            base.OnConnected(p0, p1);
         
        }

        public override void OnTagOperatorResult(Context p0, JPushMessage p1)
        {
            base.OnTagOperatorResult(p0, p1);
        }

        public override void OnAliasOperatorResult(Context p0, JPushMessage p1)
        {
            base.OnAliasOperatorResult(p0, p1);
        }

        public override void OnNotifyMessageArrived(Context p0, NotificationMessage p1)
        {
            base.OnNotifyMessageArrived(p0, p1);
        }

        public override void OnNotifyMessageOpened(Context p0, NotificationMessage p1)
        {
            base.OnNotifyMessageOpened(p0, p1);
        }
}
```

3. Manifest sample
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="{your_application_id}" android:installLocation="auto">
	<uses-sdk android:minSdkVersion="21" android:targetSdkVersion="28" />
	<!-- Required -->
	<permission android:name="{your_application_id}.permission.JPUSH_MESSAGE" android:protectionLevel="signature" />
	<!-- Required  一些系统要求的权限，如访问网络等-->
	<uses-permission android:name="{your_application_id}.permission.JPUSH_MESSAGE" />
	<uses-permission android:name="android.permission.RECEIVE_USER_PRESENT" />
	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.READ_PHONE_STATE" />
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
	<uses-permission android:name="android.permission.WRITE_SETTINGS" />
	<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	<!-- Optional. Required for location feature -->
	<uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />
	<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
	<!-- 用于开启 debug 版本的应用在 6.0 系统上的层叠窗口权限 -->
	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
	<uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" />
	<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
	<uses-permission android:name="android.permission.GET_TASKS" />
	<uses-permission android:name="android.permission.VIBRATE" />
	<application android:label="{your_application_id}.Android" android:icon="@mipmap/icon">
		<!-- Required SDK 核心功能-->
		<!-- 可配置android:process参数将PushService放在其他进程中 -->
		<service android:name="cn.jpush.android.service.PushService" android:enabled="true" android:exported="false">
			<intent-filter>
				<action android:name="cn.jpush.android.intent.REGISTER" />
				<action android:name="cn.jpush.android.intent.REPORT" />
				<action android:name="cn.jpush.android.intent.PushService" />
				<action android:name="cn.jpush.android.intent.PUSH_TIME" />
			</intent-filter>
		</service>
		<!-- since 3.0.9 Required SDK 核心功能-->
		<provider android:authorities="{your_application_id}.DataProvider" android:name="cn.jpush.android.service.DataProvider" android:exported="true" />
		<!-- since 1.8.0 option 可选项。用于同一设备中不同应用的JPush服务相互拉起的功能。 -->
		<!-- 若不启用该功能可删除该组件，将不拉起其他应用也不能被其他应用拉起 -->
		<service android:name="cn.jpush.android.service.DaemonService" android:enabled="true" android:exported="true">
			<intent-filter>
				<action android:name="cn.jpush.android.intent.DaemonService" />
				<category android:name="{your_application_id}" />
			</intent-filter>
		</service>
		<!-- since 3.1.0 Required SDK 核心功能-->
		<provider android:authorities="{your_application_id}.DownloadProvider" android:name="cn.jpush.android.service.DownloadProvider" android:exported="true" />
		<!-- Required SDK核心功能-->
		<receiver android:name="cn.jpush.android.service.PushReceiver" android:enabled="true">
			<intent-filter android:priority="1000">
				<action android:name="cn.jpush.android.intent.NOTIFICATION_RECEIVED_PROXY" />
				<!--Required  显示通知栏 -->
				<category android:name="{your_application_id}" />
			</intent-filter>
			<intent-filter>
				<action android:name="android.intent.action.USER_PRESENT" />
				<action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
			</intent-filter>
			<!-- Optional -->
			<intent-filter>
				<action android:name="android.intent.action.PACKAGE_ADDED" />
				<action android:name="android.intent.action.PACKAGE_REMOVED" />
				<data android:scheme="package" />
			</intent-filter>
		</receiver>
		<!-- Required SDK核心功能-->
		<activity android:name="cn.jpush.android.ui.PushActivity" android:configChanges="orientation|keyboardHidden" android:theme="@android:style/Theme.NoTitleBar" android:exported="false">
			<intent-filter>
				<action android:name="cn.jpush.android.ui.PushActivity" />
				<category android:name="android.intent.category.DEFAULT" />
				<category android:name="{your_application_id}" />
			</intent-filter>
		</activity>
		<!-- SDK 核心功能-->
		<activity android:name="cn.jpush.android.ui.PopWinActivity" android:configChanges="orientation|keyboardHidden" android:exported="false" android:theme="@style/MyDialogStyle">
			<intent-filter>
				<category android:name="android.intent.category.DEFAULT" />
				<category android:name="{your_application_id}" />
			</intent-filter>
		</activity>
		<!-- 注意此配置在 JPush 3.2.0 及以前版本是必须配置，3.2.0 以后版本已废弃此配置-->
		<service android:name="cn.jpush.android.service.DownloadService" android:enabled="true" android:exported="false"></service>
		<!-- Since JCore2.0.0 Required SDK核心功能-->
		<!-- 可配置android:process参数将Service放在其他进程中；android:enabled属性不能是false -->
		<!-- 这个是自定义Service，要继承极光JCommonService，可以在更多手机平台上使得推送通道保持的更稳定 -->
		<service android:name="{ProjectNamespace}.MyJCommonService" android:enabled="true" android:exported="false" android:process=":pushcore">
			<intent-filter>
				<action android:name="cn.jiguang.user.service.action" />
			</intent-filter>
		</service>
		<!-- Required SDK核心功能-->
		<receiver android:name="cn.jpush.android.service.AlarmReceiver" />
		<!-- User defined.  For test only  用户自定义接收消息器,3.0.7开始支持,目前新tag/alias接口设置结果会在该广播接收器对应的方法中回调-->
		<receiver android:name="{ProjectNamespace}.MyJPushMessageReceiver" android:enabled="true" android:exported="false">
			<intent-filter>
				<action android:name="cn.jpush.android.intent.RECEIVE_MESSAGE" />
				<category android:name="{your_application_id}"></category>
			</intent-filter>
		</receiver>
		<!--since 3.3.0 Required SDK核心功能-->
		<activity android:name="cn.jpush.android.service.JNotifyActivity" android:exported="true" android:taskAffinity="jpush.custom" android:theme="@android:style/Theme.Translucent.NoTitleBar">
			<intent-filter>
				<action android:name="cn.jpush.android.intent.JNotifyActivity" />
				<category android:name="{your_application_id}" />
			</intent-filter>
		</activity>
		<!-- Required  . Enable it you can get statistics data with channel -->
		<meta-data android:name="JPUSH_CHANNEL" android:value="developer-default" />
		<meta-data android:name="JPUSH_APPKEY" android:value="{your_jpush_appkey}" />
	</application>
</manifest>
```

4. Config manifest.xml for your own project
```
- {your_jpush_appkey}
- {your_application_id}
- {ProjectNamespace}.MyJPushMessageReceiver
- {ProjectNamespace}.MyJCommonService
```
