Patching Android applications with a `frida-gadget.so` is a surprisingly complex task, more so given the amount of moving parts to pull it off! In many cases though objections' Android APK patcher should just work™. However, if it didn't this page should help you understand what you can do about it.

In almost all of the scenarios you may run into, you are going to have to perform some of the steps objection automates manually to debug what is going on with your target application.

# toc

- [high level patching overview](#high-level-patching-overview)
- [debugging failed patches](#debugging-failed-patches)
  - [APK packing / unpacking](#apk-packing--unpacking)
  - [custom target classes](#custom-target-classes)

## high level patching overview

The patching process works as follows:

- unpack the target APK using `apktool`
  - inject the `INTERNET` permission if it is not already used by the app
- find a launchable activity using `aapt`
- patch in a static constructor that loads the platform specific Frida gadget into the class discovered in the previous step
- copy over the frida-gadget shared library to the unpacked path
- repack a new, modified APK using `apktool`
- zipalign & sign the new apk

If you are interested in the details, the patching flow is wrapped up into one function that calls the relevant method on the [AndroidPatcher](https://github.com/sensepost/objection/blob/1.9.5/objection/utils/patchers/android.py#L180) class, [here](https://github.com/sensepost/objection/blob/1.9.5/objection/commands/mobile_packages.py#L97-L227).

## debugging failed patches

So the patcher failed, all is not lost yet! There are many reasons why this could have happened, but the following flow should help you get going again.

### understand the error

Read the error information, in **detail**. More often than not, the problem is clearly stated and you can fix it yourself.

### check old issue reports

Sometimes, your question has already been answered. This is a good search filter for issues that may relate to patching problems: https://github.com/sensepost/objection/issues?q=is%3Aissue+label%3Aapps+

### APK packing / unpacking

Many errors could be caused by `apktool` not being able to unpack and repack the APK. Copy the target APK somewhere safe and run the `apktool d your.apk` and then `apktool b yourapk_path/` commands to see if that works. If it does, the next thing to try would be the objection command to [unpack](https://github.com/sensepost/objection/blob/1.9.5/objection/utils/patchers/android.py#L397-L405) and [repack](https://github.com/sensepost/objection/blob/1.9.5/objection/utils/patchers/android.py#L842-L849) APK's.

- To unpack: `apktool decode -f -r -o temp/ your.apk` (toggle `-r` on and off to skip resource decoding)
- To build: `apktool build temp/ -o new.apk`

#### the pause flag

If you are able to unpack, but not repack the APK, this may be a perfect opportunity to discover why that is the case. Sometimes all you need to do is fiddle with a resource value, delete an unused file or similar. When manually decoding and rebuilding an APK you have ample time to make these changes to get the rebuild going again, but not really when using objection. For this reason the Android patcher command in objection has the `--pause` flag. When using this flag, the patching process will print the current working directory with all of the changes made and pause, allowing you to make any last minute changes that may be necessary for the rebuild to complete successfully.

### custom target classes

By default, objection will try and find a launchable activity as the class to target for the `loadLibrary` [patch](https://github.com/sensepost/objection/blob/1.9.5/objection/utils/patchers/android.py#L646-L700). Sometimes, the patching process can fail either because it was not able to identify a launchable activity, or because objection could not find the smali location for the class. To combat this, the `patchapk` command has a `--target-class` [flag](https://github.com/sensepost/objection/blob/1.9.5/objection/console/cli.py#L338) where you could specify the fully qualified target class to use instead.

A good candidate would be to use the class specified in the `android:name` tag in the Android Manifest. ref: https://developer.android.com/reference/android/app/Application

