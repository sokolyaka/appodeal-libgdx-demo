# Appodeal libGDX Demo

libGDX plugin for cross-platform Appodeal SDK support

## How to Update Plugin Manually

First of all clone this repository.

### Android Part:

+ Download new Appodeal Android SDK from [Appodeal Docs](https://www.appodeal.com/sdk/choose_framework) -> Android. Unzip it somewhere.
+ Copy new AndroidManifest.xml tags from [Appodeal Docs](https://www.appodeal.com/sdk/choose_framework) -> Android. And replace content between `<activity	... <category android:name="android.intent.category.LAUNCHER" /></intent-filter></activity>` and closing `</application>` tag with copied tags.
+ Replace old `jars` with new inside [android/libs](/android/libs/).
+ Replace old `aars` with new inside [android/libs/aar_repository](/android/libs/aar_repository/).
+ Check for any native SDK API changes in [AndroidGdxAppodeal.java](/android/src/com/appodeal/gdx/android/AndroidGdxAppodeal.java).
+ Android ready for build.

### iOS Part:

+ Download new Appodeal iOS SDK from [Appodeal Docs](https://www.appodeal.com/sdk/choose_framework) -> iOS -> Objective-C. Unzip it somewhere.
+ Place new `Appodeal.framework` under `ios-moe/xcode` folder.
+ Place contents of `Resources` folder under `ios-moe/xcode/ios-moe` folder. If `Resources` does not exists inside new SDK archive, then open `ios-moe.xcodeproj` navigate into `ios-moe` and remove highlighted with red color resouces.
+ Check for any changes in `Build Phases` -> `Link Binary With Libraries` in corresponding to changes in 3 step of 5.3 Manual integration in [Appodeal Docs](https://www.appodeal.com/sdk/choose_framework) -> iOS -> Objective-C.
+ Remove old bindings, to do that navigate into [org](/ios-moe/src/) folder and completely remove it.
+ Open [AppodealMOE.nbc](/ios-moe/AppodealMOE.nbc) and press `Generate Bindings`. It will generate new binding for the new Appodeal iOS SDK.
+ Check for any native SDK API changes in [iOSGdxAppodeal.java](/ios-moe/src/com/appodeal/gdx/ios/iOSGdxAppodeal.java).
+ iOS ready for build.

## Plugin files integration

Download Plugin here and unzip it somewhere.

### Core Module

Copy com folder from core/src folder to your source folder inside core module. Usually it is named src or java/main.

### iOS Module

+ Download Appodeal iOS SDK [here](http://bit.ly/appodeal-ios-sdk-2-1-10-release) -> iOS -> Objective-C, unzip and place `Appodeal.framework` under `ios-moe/xcode` folder.
+ Copy contents of src folder from `core/src` folder to your source folder inside `ios-moe` module. Usually it is named `src` or `java/main`.
+ Open `ios-moe.xcodeproj` inside `ios-moe/xcode` folder and drag and drop `Appodeal.framework` and `Resorces` folder from finder to xcode project hierarchy under project group, click on `Copy if needed` checkmark and then click OK.
+ Move to `Build Settings` of your xcode project, find there `Other Linker Flags`, double click it and add there `-ObjC` flag.
+ Move to `Build Phases` of your xcode project, expand `Link Binary With Libraries` and add there following libraries and frameworks:

```plist
AdSupport
AudioToolbox
AVFoundation
CFNetwork
CoreGraphics
CoreImage
CoreLocation
CoreMedia
CoreMotion
CoreTelephony
EventKitUI
GLKit
ImageIO
libc++
libsqlite3.dylib
libxml2.2.dylib
libz.dylib
MediaPlayer
MessageUI
MobileCoreServices
QuartzCore
Security
StoreKit
SystemConfiguration
Twitter
UIKit
WebKit
```

+ Open your Info.plist inside ios-moe/xcode/ios-moe folder and add there following keys:

```plist
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
  <true/>
</dict>
```

### Android Module

+ Copy contents of libs folder from android/libs folder to your android/libs folder.
+ Copy com folder from android/src folder to your source folder inside core module. Usually it is named src or java/main.
+ Open libGDX project main build.gradle, if you haven't used any third-party libraries in your android module add following dependency to your project(":android") object and add code below to dependencies, you can skip this step if it is already added.

```gradle
compile fileTree(dir: 'libs', include: '*.jar')
```

+ move to allprojects object and add code below to repositories object:

```gradle
flatDir {
    dirs 'libs/aar_repository'
}
```

+ move to project(":android") object and add code below to dependencies object:

```gradle
compile(name:'adcolony-sdk-3.1.2', ext:'aar')
compile(name:'mmedia-6.4.0', ext:'aar')
```

+ Appodeal requires Google Play Services to work properly. Add play services dependency to project(":android") object, you can use this code if you don't have it added yet (you can use versions from 8 and above):

```gradle
compile 'com.google.android.gms:play-services-ads:10.2.0'
compile 'com.google.android.gms:play-services-location:10.2.0'
```

+ Open your AndroidManifest.xml inside android module and add following permissions inside manifest tag of your AndroidManifest.xml (optional ones can be removed if you don't want to use it):

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" /> <!--optional -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /> <!--optional -->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" /> <!--optional-->
```

+ Open your AndroidManifest.xml inside android module and add following activities, services and receivers inside application tag of your AndroidManifest.xml (Note that you should remove unused ones when you are disabling (removing) some of networks):

```xml
<activity android:name="com.appodeal.ads.InterstitialActivity"
    android:configChanges="orientation|screenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<activity android:name="com.appodeal.ads.VideoActivity"
    android:configChanges="orientation|screenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<activity android:name="com.appodeal.ads.LoaderActivity"
    android:configChanges="orientation|screenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<activity android:name="com.appodeal.ads.VideoPlayerActivity"
    android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen"/>
<receiver android:name="com.appodeal.ads.AppodealPackageAddedReceiver" android:exported="true" android:enabled="true">
    <intent-filter>
        <action android:name="android.intent.action.PACKAGE_ADDED" />
        <data android:scheme="package" />
    </intent-filter>
</receiver>
<activity android:name="com.appodeal.ads.TestActivity"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />

<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
<activity android:name="com.google.android.gms.ads.AdActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
    android:theme="@android:style/Theme.Translucent" />

<activity android:name="com.chartboost.sdk.CBImpressionActivity" android:excludeFromRecents="true"
    android:hardwareAccelerated="true" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen"
    android:configChanges="keyboardHidden|orientation|screenSize" />

<activity android:name="com.applovin.adview.AppLovinInterstitialActivity" android:configChanges="orientation|screenSize"/>
<activity android:name="com.applovin.adview.AppLovinConfirmationActivity" android:configChanges="orientation|screenSize"/>

<activity android:name="com.mopub.mobileads.MoPubActivity"
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:theme="@android:style/Theme.Translucent" />
<activity android:name="com.mopub.common.MoPubBrowser"
    android:configChanges="keyboardHidden|orientation|screenSize" />
<activity android:name="com.mopub.mobileads.MraidActivity"
    android:configChanges="keyboardHidden|orientation|screenSize" />
<activity android:name="com.mopub.mobileads.MraidVideoPlayerActivity"
    android:configChanges="keyboardHidden|orientation|screenSize" />
<activity android:name="com.mopub.mobileads.RewardedMraidActivity"
    android:configChanges="keyboardHidden|orientation|screenSize"/>

<activity android:name="org.nexage.sourcekit.vast.activity.VASTActivity"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" />

<activity android:name="org.nexage.sourcekit.vast.activity.VPAIDActivity"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" />

<activity android:name="com.appodeal.ads.networks.vpaid.VPAIDActivity"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" />

<activity android:name="com.amazon.device.ads.AdActivity" android:configChanges="keyboardHidden|orientation|screenSize"/>

<activity android:name="com.my.target.ads.MyTargetActivity" android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
    android:hardwareAccelerated="true"/>

<!--suppress AndroidDomInspection -->
<activity android:name="com.facebook.ads.AudienceNetworkActivity" android:configChanges="keyboardHidden|orientation|screenSize" />

<!--suppress AndroidDomInspection -->
<activity android:name="com.startapp.android.publish.list3d.List3DActivity"
    android:theme="@android:style/Theme" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.startapp.android.publish.adsCommon.activities.OverlayActivity"
    android:theme="@android:style/Theme.Translucent"
    android:configChanges="orientation|keyboardHidden|screenSize" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.startapp.android.publish.adsCommon.activities.FullScreenActivity"
    android:theme="@android:style/Theme"
    android:configChanges="orientation|keyboardHidden|screenSize" />
<!--suppress AndroidDomInspection -->
<service android:name="com.startapp.android.publish.common.metaData.PeriodicMetaDataService" />
<!--suppress AndroidDomInspection -->
<service android:name="com.startapp.android.publish.common.metaData.InfoEventService" />


<service android:name="com.yandex.metrica.MetricaService" android:enabled="true"
    android:exported="true" android:process=":Metrica">
    <intent-filter>
        <category android:name="android.intent.category.DEFAULT" />
        <action android:name="com.yandex.metrica.IMetricaService" />
        <data android:scheme="metrica" />
    </intent-filter>
    <meta-data android:name="metrica:api:level" android:value="58" />
</service>
<receiver android:name="com.yandex.metrica.MetricaEventHandler"
    android:enabled="true" android:exported="true">
    <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
</receiver>

<!--suppress AndroidDomInspection -->
<activity android:name="com.yandex.mobile.ads.AdActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize" />

<activity android:name="com.unity3d.ads.adunit.AdUnitActivity"
    android:configChanges="fontScale|keyboard|keyboardHidden|locale|mnc|mcc|navigation|orientation|screenLayout|screenSize|smallestScreenSize|uiMode|touchscreen"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" android:hardwareAccelerated="true" />
<activity android:name="com.unity3d.ads.adunit.AdUnitSoftwareActivity"
    android:configChanges="fontScale|keyboard|keyboardHidden|locale|mnc|mcc|navigation|orientation|screenLayout|screenSize|smallestScreenSize|uiMode|touchscreen"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" android:hardwareAccelerated="false" />
<activity android:name="com.unity3d.ads2.adunit.AdUnitActivity"
    android:configChanges="fontScale|keyboard|keyboardHidden|locale|mnc|mcc|navigation|orientation|screenLayout|screenSize|smallestScreenSize|uiMode|touchscreen"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" android:hardwareAccelerated="true" />
<activity android:name="com.unity3d.ads2.adunit.AdUnitSoftwareActivity"
    android:configChanges="fontScale|keyboard|keyboardHidden|locale|mnc|mcc|navigation|orientation|screenLayout|screenSize|smallestScreenSize|uiMode|touchscreen"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen" android:hardwareAccelerated="false" />

<!--suppress AndroidDomInspection -->
<activity android:name="com.jirbo.adcolony.AdColonyOverlay"
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.jirbo.adcolony.AdColonyFullscreen"
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.jirbo.adcolony.AdColonyBrowser"
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen" />

<!--suppress AndroidDomInspection -->
<activity android:name="com.vungle.publisher.VideoFullScreenAdActivity"
    android:configChanges="keyboardHidden|orientation|screenSize|screenLayout|smallestScreenSize"
    android:theme="@android:style/Theme.NoTitleBar.Fullscreen"/>
<!--suppress AndroidDomInspection -->
<activity android:name="com.vungle.publisher.MraidFullScreenAdActivity"
    android:configChanges="keyboardHidden|orientation|screenSize|screenLayout|smallestScreenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen"/>

<!--suppress AndroidDomInspection -->
<activity android:name="com.flurry.android.FlurryFullscreenTakeoverActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.flurry.android.FlurryShareActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.flurry.android.FlurryTileAdActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
    android:screenOrientation="portrait"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.flurry.android.FlurryBrowserActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />

<!--suppress AndroidDomInspection -->
<activity android:name="com.tapjoy.TJAdUnitActivity" android:configChanges="orientation|keyboardHidden|screenSize"
    android:hardwareAccelerated="true" android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.tapjoy.mraid.view.ActionHandler" android:configChanges="orientation|keyboardHidden|screenSize" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.tapjoy.mraid.view.Browser" android:configChanges="orientation|keyboardHidden|screenSize" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.tapjoy.TJContentActivity" android:configChanges="orientation|keyboardHidden|screenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" android:hardwareAccelerated="true" />

<!--suppress AndroidDomInspection -->
<activity android:name="com.ironsource.sdk.controller.ControllerActivity" android:configChanges="orientation|screenSize"
    android:hardwareAccelerated="true" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.ironsource.sdk.controller.InterstitialActivity" android:configChanges="orientation|screenSize"
    android:hardwareAccelerated="true" android:theme="@android:style/Theme.Translucent" />
<!--suppress AndroidDomInspection -->
<activity android:name="com.ironsource.sdk.controller.OpenUrlActivity" android:configChanges="orientation|screenSize"
    android:hardwareAccelerated="true" android:theme="@android:style/Theme.Translucent" />

<!--suppress AndroidDomInspection -->
<activity android:name="com.adcolony.sdk.AdColonyInterstitialActivity" android:configChanges="keyboardHidden|orientation|screenSize"
    android:hardwareAccelerated="true"/>
<!--suppress AndroidDomInspection -->
<activity android:name="com.adcolony.sdk.AdColonyAdViewActivity" android:configChanges="keyboardHidden|orientation|screenSize"
    android:hardwareAccelerated="true"/>

<activity android:name="com.inmobi.rendering.InMobiAdActivity" android:configChanges="keyboardHidden|orientation|keyboard|smallestScreenSize|screenSize|screenLayout"
    android:hardwareAccelerated="true" android:resizeableActivity="false" android:theme="@android:style/Theme.NoTitleBar" />
<receiver android:name="com.inmobi.commons.core.utilities.uid.ImIdShareBroadCastReceiver" android:enabled="true" android:exported="true">
    <intent-filter>
        <action android:name="com.inmobi.share.id"/>
    </intent-filter>
</receiver>

<meta-data android:name="presage_key" android:value="000000"/>
<service android:name="io.presage.services.PresageServiceImp"/>
<activity android:name="io.presage.activities.PresageActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenSize" android:hardwareAccelerated="true">
    <intent-filter>
        <action android:name="io.presage.intent.action.LAUNCH_WEBVIEW" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
<receiver android:name="io.presage.receivers.BootReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.DATE_CHANGED"/>
        <action android:name="io.presage.receivers.BootReceiver.RESTART_SERVICE"/>
    </intent-filter>
</receiver>

<provider
    android:name="com.millennialmedia.internal.utils.MediaContentProvider"
    android:authorities="${applicationId}.MediaContentProvider"
    android:grantUriPermissions="true"
    android:exported="false" />
```

### Additional Settings
Note that you should enable Multidex Support for using base Appodeal libGDX plugin. If you don't want to use multidex for some reasons contact Support on Appodeal website to get special build of plugin, or replace current jar's and aar's inside libs folder of android module using MaxDex build from [Docs page](https://www.appodeal.com/sdk/) -> Android. To add multidex support follow [this](https://developer.android.com/studio/build/multidex.html) guide.

## libGDX Integration

### Ad Types

+ GdxAppodeal.INTERSTITIAL
+ GdxAppodeal.BANNER
+ GdxAppodeal.REWARDED_VIDEO
+ GdxAppodeal.NON_SKIPPABLE_VIDEO

Ad types can be combined using "|" operator. For example GdxAppodeal.INTERSTITIAL | GdxAppodeal.SKIPPABLE_VIDEO

### SDK Initialization

To initialize SDK you need to add following code in create method of your main adapter:

```java
String appKey = "e7e04e54ae0a8d28cff2f7e7e7d094e78b2a09743be2cc4a";
GdxAppodeal.initialize(appKey, GdxAppodeal.BANNER | GdxAppodeal.INTERSTITIAL);
```
+ To initialize only interstitials use GdxAppodeal.initialize(appKey, GdxAppodeal.INTERSTITIAL)
+ To initialize only banners use GdxAppodeal.initialize(appKey, GdxAppodeal.BANNER)
+ To initialize only rewarded video use GdxAppodeal.initialize(appKey, GdxAppodeal.REWARDED_VIDEO)
+ To initialize only non-skippable video use GdxAppodeal.initialize(appKey, GdxAppodeal.NON_SKIPPABLE_VIDEO)

### Display Ad

```java
GdxAppodeal.show(AdTypes);
```

### Checking if ad is loaded

```java
GdxAppodeal.isLoaded(AdTypes);
```

### Hiding banner

```java
GdxAppodeal.hide(GDXAppodeal.BANNER);
```

### Setting Callbacks

#### Interstitial:

```java
GdxAppodeal.setInterstitialCallbacks(new InterstitialCallback() {
  @Override
  public void onInterstitialLoaded(boolean isPrecache) { }

  @Override
  public void onInterstitialFailedToLoad() { }

  @Override
  public void onInterstitialShown() { }

  @Override
  public void onInterstitialClicked() { }

  @Override
  public void onInterstitialClosed() { }
});
```

#### Banner:

```java
GdxAppodeal.setBannerCallbacks(new BannerCallback() {
  @Override
  public void onBannerLoaded() { }

  @Override
  public void onBannerFailedToLoad() { }

  @Override
  public void onBannerShown() { }

  @Override
  public void onBannerClicked() { }
});
```

#### Rewarded Video:

```java
GdxAppodeal.setRewardedVideoCallbacks(new RewardedVideoCallback() {
  @Override
  public void onRewardedVideoLoaded() { }

  @Override
  public void onRewardedVideoFailedToLoad() { }

  @Override
  public void onRewardedVideoShown() { }

  @Override
  public void onRewardedVideoFinished(int amount, String name) { }

  @Override
  public void onRewardedVideoClosed() { }
});
```

#### Non Skippable Video:

```java
GdxAppodeal.setNonSkippableVideoCallbacks(new NonSkippableVideoCallback() {
  @Override
  public void onNonSkippableVideoLoaded() { }

  @Override
  public void onNonSkippableVideoFailedToLoad() { }

  @Override
  public void onNonSkippableVideoShown() { }

  @Override
  public void onNonSkippableVideoFinished() { }

  @Override
  public void onNonSkippableVideoClosed() { }
});
```

### Advanced Features

#### Enabling test mode

```java
GdxAppodeal.setTesting(true);
```

#### Enabling logging

```java
GdxAppodeal.setLogLevel(level)
```

Available params: LogLevel.none, LogLevel.debug, LogLevel.verbose

#### Manual ad caching

```java
GdxAppodeal.cache(adTypes);
```

+ You should disable automatic caching before SDK initialization using setAutoCache(adTypes, false).
+ To cache interstitial use GdxAppodeal.cache(GdxAppodeal.INTERSTITIAL)
+ To cache rewarded video use GdxAppodeal.cache(GdxAppodeal.REWARDED_VIDEO)
+ To cache non-skippable video use GdxAppodeal.cache(GdxAppodeal.NON_SKIPPABLE_VIDEO)
+ To cache interstitial and non skippable video use GdxAppodeal.cache(GdxAppodeal.INTERSTITIAL | GdxAppodeal.NON_SKIPPABLE_VIDEO)
+ To cache banner use GdxAppodeal.cache(GdxAppodeal.BANNER)

#### Enabling or disabling automatic caching

```java
GdxAppodeal.setAutoCache(adTypes, false);
```

+ Should be used before SDK initialization
+ To disable automatic caching for interstitials use GdxAppodeal.setAutoCache(GdxAppodeal.INTERSTITIAL, false)
+ To disable automatic caching for rewarded videos use GdxAppodeal.setAutoCache(GdxAppodeal.REWARDED_VIDEO, false)
+ To disable automatic caching for non-skippable videos use GdxAppodeal.setAutoCache(GdxAppodeal.NON_SKIPPABLE_VIDEO, false)
+ To disable automatic caching for banners use GdxAppodeal.setAutoCache(GdxAppodeal.BANNER, false)

#### Disabling networks

```java
GdxAppodeal.disableNetwork((String)network);
```

Should be used before SDK initialization

Available parameters: "adcolony", "admob", "amazon_ads", "applovin", "appnext", "avocarrot", "chartboost", "facebook", "flurry", "inmobi", "inner-active", "ironsource", "mailru", "mmedia", "mopub", "mobvista", "ogury", "openx", "pubnative", "smaato", "startapp", "tapjoy", "unity_ads", "vungle", "yandex"


## Versions & Requirements

Current plugin version: 2.1.10

Appodeal SDK:
+ iOS 2.1.10
+ Android 2.1.9

Running OS Requirements:
+ iOS 8.1 and above
+ Android API 14 and above

iOS MOE requirements:
+ MOE 1.3.6 or above
+ libGDX 1.9.6 or above

Tested on:
+ libGDX 1.9.6
+ MOE 1.3.6
