## Add security when building mobile apps

Security is a fundamental term to discuss, but in this little blog based on my experience, I hope to help you build more secure apps or add some security layers when making apps with the **Ionic Framework**, **React Native** or just **Native App**

Before we begin, you must back up your project in case you need to roll back any change you did it. Remember that some tips are just for a specific framework, and others can be shared.


## Content

- [Framework or Library](#Framework-or-Library)
    - [Ionic Obfuscation](#Ionic-Obfuscation)
    - [Ionic Obfuscation Rules](#Ionic-Obfuscation-Rules)
    - [Ionic Rooted Device Checking](#Ionic-Rooted-Device-Checking)
    - [Ionic Detect Jailbrek Phone](#Ionic-Detect-Jailbrek-Phone)
- [Android Settings](#Android-Settings)
    - [Prevent Screenshot | Screenrecord](#Prevent-Screenshot-|-Screenrecord)
    - [Inapropied Usege Of The Platform](#Inapropied-Usege-Of-The-Platform)
- [iOS Settings](#iOS-Settings)
    - [Prevent Insecure Conextions](#Prevent-Insecure-Conextions)
- [Reverse Engineer](#Reverse-Engineer)
    - [Reverse an Apk](#Reverse-an-Apk)


## Framework or Library

![Ionic Logo](https://upload.wikimedia.org/wikipedia/commons/d/d1/Ionic_Logo.svg)

For android **apk** or **abb** we use a file than you may now familiar the **build.gradle** and **proguard-rules.pro**


### Ionic Obfuscation

To obfuscate, you need to find the **build.gradle** file and enable the property **minifyEnabled** to true, like this:

```bash
release {
  minifyEnabled true
  ...
}
```


### Ionic Obfuscation Rules

After this, add the below lines in **proguard-rules.pro** file

```bash
# Ionic Config
-keep class org.apache.cordova.** { *; }
-keep class org.apache.cordova.camera.** { *; }
-keep class org.apache.cordova.** { *; }
-keep public class * extends org.apache.cordova.CordovaPlugin
-keep class com.ionic.keyboard.IonicKeyboard.** { *; }
# Ionic Config 


# Your App id
# !Remember to change the com.abc.xyz to your app id!
-keep class com.abc.xyz.BuildConfig { *; }
# Your App id


# AdmMob
-keep class * extends java.util.ListResourceBundle {
    protected Object[][] getContents();
}
-keep public class com.google.android.gms.common.internal.safeparcel.SafeParcelable {
    public static final *** NULL;
}
-keepnames @com.google.android.gms.common.annotation.KeepName class *
-keepclassmembernames class * {
    @com.google.android.gms.common.annotation.KeepName *;
}
-keepnames class * implements android.os.Parcelable {
    public static final ** CREATOR;
}
-keep public class com.google.cordova.admob.**
# AdmMob


# Not sure if needed, found it in several documentations
-keep class * extends java.util.ListResourceBundle {
    protected Object[][] getContents();
}
-keep public class com.google.android.gms.common.internal.safeparcel.SafeParcelable {
    public static final *** NULL;
}
-keepnames @com.google.android.gms.common.annotation.KeepName class *
-keepclassmembernames class * {
    @com.google.android.gms.common.annotation.KeepName *;
}
-keepnames class * implements android.os.Parcelable {
    public static final ** CREATOR;
}
# Not sure if needed, found it in several documentations


# Rules for Capacitor v3 plugins and annotations
 -keep @com.getcapacitor.annotation.CapacitorPlugin public class * {
     @com.getcapacitor.annotation.PermissionCallback <methods>;
     @com.getcapacitor.annotation.ActivityCallback <methods>;
     @com.getcapacitor.annotation.Permission <methods>;
     @com.getcapacitor.PluginMethod public <methods>;
 }
# Rules for Capacitor v3 plugins and annotations


# Rules for Capacitor v2 plugins and annotations
# These are deprecated but can still be used with Capacitor for now
-keep @com.getcapacitor.NativePlugin public class * {
  @com.getcapacitor.PluginMethod public <methods>;
}
# Rules for Capacitor v2 plugins and annotations


# Rules for Cordova plugins
-keep public class * extends org.apache.cordova.* {
  public <methods>;
  public <fields>;
}
# Rules for Cordova plugins


# Note! this rules add if you use Huawei Plugins
# HMS Settings
-ignorewarnings
-keepattributes *Annotation*
-keepattributes Exceptions
-keepattributes InnerClasses
-keepattributes Signature
-keep class com.huawei.hianalytics.**{*;}
-keep class com.huawei.updatesdk.**{*;}
-keep class com.huawei.hms.**{*;}
-repackageclasses
# HMS Settings
```

> **NOTE**
>
>Remember to check if any other package you use in your project has notes about another rule you must bed. Because use the **proguard-rules.pro** may break your app if you don't pay attention or omit those rules the author of the package gives you.


### Ionic Rooted Device Checking

You can achieve these by using the **Diagnostic Plugin** to check if the device is rooted. Keep in mind that have many other functions if you want to check it.

:package: [Cordova Diagnostic Plugin](https://github.com/dpa99c/cordova-diagnostic-plugin#isdevicerooted)


### Ionic Detect Jailbrek Phone

Another layer will be to use some library to check if your app is launched on an insecure OS like Jailbreak. I found this library to help prevent the **Jailbreak** and the documentation for setting it up.

:package: [Jailbreak/Root Detection Plugin for Apache Cordova](https://github.com/WuglyakBolgoink/cordova-plugin-iroot)

## Android Settings

### Prevent Screenshot | Screenrecord

For screenshots or screen record disable, you need to import the **WindowManager** and add this line to the **MainActivity** file
```bash
import android.view.WindowManager;
...

getWindow().setFlags(WindowManager.LayoutParams.FLAG_SECURE, WindowManager.LayoutParams.FLAG_SECURE);
```


### Inapropied Usege Of The Platform

If your app won't be doing more processes or need much RAM, you need to delete these properties from your **AndroidManifest.xml** you should use it only when you know exactly where all your memory is being allocated and why it must be retained.
```bash
<application
...
"android:largeHeap="true"
...
```
You can read more about this on the official [documentation](https://developer.android.com/topic/performance/memory)


## iOS Settings

On iOS, because it is a robust ecosystem, the same OS gives you some layer of security, but this doesn't mean is 100% secure.
For that reason, we add some settings that may help to secure your app.

go to the **Info.plist** and add these lines to

### Prevent Insecure Connections

```bash
<key>NSAppTransportSecurity</key>
<dict>
	<key>NSAllowsArbitraryLoads</key>
	<false/>
</dict>
```


## Reverse Engineer

In this case, the **Reverse Engineer** is used to verify if your code is optimized and compressed, but you can use it for whatever purpose you need, check malicious code, analyze apps or just for fun.


### Reverse an Apk

1. Rename your **.apk** file and add **.zip** at the end.
2. Extract the content. When you extract, you will have all the code, classes and many other things.
3. Download the tool **dex2jar** and place it in the same folder you extract the **apk** [link](https://github.com/DexPatcher/dex2jar/releases)
4. Run the following command on your terminal
    ```bash
    d2j-dex2jar.bat classes.dex
    ```
5. Download the tool **Java Decompiler** [link](https://java-decompiler.github.io/)
6. Last but not least, open the previous download program **Java Decompiler** and open the file located in the extracted **apk** folder **classes-dex2jar.jar**. If you see your code minified :party: you got it! The obfuscation process was successful.
[Example of obfuscation](https://www.preemptive.com/wp-content/uploads/2020/10/Rename-Obfuscation-Example.png)

> If you get some problems with these steps, you can check [this](https://medium.com/helpshift-engineering/reverse-engineer-your-favorite-android-app-863a797042a6) or any other **Reverse an Apk** tutorial.


I Hope this couples of tips help you with your goals, and I will continue adding more :partying_face: :memo: [source](https://github.com/ACRONIMAX/mobile-security)