Before you can use any of the `objection` commands on an Android application, the application's APK itself needs to be patched and code signed to load the `frida-gadget.so` on start. To patch an APK though, it should be as simple as running the `objection patchapk` command.

## toc
* [patching - dependencies](#patching---dependencies)
* [(optional) - obtaining play store apks](#optional---obtaining-play-store-apks)
* [patching - patching-an-apk](#patching---patching-an-apk)
* [next steps](#next-steps)

## patching - dependencies
The `objection patchapk` is a command that basically wraps around several other system commands, automating the patching process as far as possible. Naturally, those commands need to be installed and available first. They are:

* `aapt` - from: http://elinux.org/Android_aapt
* `adb` - from: https://developer.android.com/studio/command-line/adb.html
* `jarsigner` - from: http://docs.oracle.com/javase/7/docs/technotes/tools/windows/jarsigner.html
* `apktool` - from: https://ibotpeaches.github.io/Apktool/

Most of these dependencies are really easy to solve and can be installed using `homebrew` on macOS, or `apt` in Kali Linux. If you choose to manually install the dependencies, ensure that they are available in your current `PATH`.

## (optional) - obtaining play store apks
It is possible to get the APK of an app you downloaded from the Google Play store without root on your Android device. To do this, first download the application to your phone and connect it to your computer. Make sure your computer sees the device using `adb devices`. Then:

- Locate the package name for the application you downloaded:

```txt
$ adb shell pm list packages | grep uber
package:com.ubercab
```

You may also run this command with the `-f` flag to get the path of the app too, allowing you to skip the next step:

```
$ adb shell pm list packages -f -3 | cut -d "=" -f1 | grep uber
```

- With the package name known, determine its installed path on the device:

```txt
$ adb shell pm path com.ubercab         
package:/data/app/com.ubercab-1/base.apk
```

- Finally, download the APK from the device:

```txt
$ adb pull /data/app/com.ubercab-1/base.apk com.ubercab.apk
[100%] /data/app/com.ubercab-1/base.apk


$ ls com.ubercab.apk 
com.ubercab.apk
```

This process should leave you with the downloaded APK on your local computer, ready to use in the patching process described in the next section.

## patching - patching an APK
With **all** of the above dependencies solved, we can finally patch an actual APK. The patching process itself is as simple as:

```
objection patchapk --source app-release.apk
```

This command will determine the target architecture of your device using `adb`, extract the source APK, insert the INTERNET permission if it does not already exist, patch and embed the `frida-gadget.so` and repackage and sign a new APK for you.

```
$ objection patchapk --source app-release.apk               
No architecture specified. Determining it using `adb`...
Detected the architecture as: armeabi-v7a
Using Gadget version: 10.3.14
Unpacking app-release.apk
App already has android.permission.INTERNET
Reading smali from: /tmp/tmpq9hpoh87.apktemp/smali/com/sensepost/apewpew/MainActivity.smali
Injecting loadLibrary call at line: 10
Writing patched smali back to: /tmp/tmpq9hpoh87.apktemp/smali/com/sensepost/apewpew/MainActivity.smali
Creating library path: /tmp/tmpq9hpoh87.apktemp/lib/armeabi-v7a
Copying Frida gadget to libs path...
Rebuilding the APK with the frida-gadget loaded...
Built new APK with injected loadLibrary and frida-gadget
Signing new APK.
Signed the new APK
Copying final apk from /tmp/tmpq9hpoh87.apktemp.objection.apk to current directory...
Cleaning up temp files...

```

**Note:** If you do not have an Android device connected then you will need to specify the target architecture using the `--architecture` flag.

## next steps
Once you have a patched APK ready, its time to install it. See the #TODO article for more information.