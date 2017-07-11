Running patched applications on iOS devices require us to side load and run them using external tools. For macOS users, `ios-deploy` is perfect for this. For Linux users, the `libimobiledevice` project provides commands to perform similar tasks.

Unfortunately, you will need macOS to patch the IPA. However, once patched, the IPA can be installed and run using `idevicedebug` on Linux.

To run an IPA, we need to do it using tools that will start the target application with the [debugserver](http://iphonedevwiki.net/index.php/Debugserver) started.

## toc
* [installing and running on macos](#installing-and-running-on-macos)
    * [ios-deploy installation](#ios-deploy-installation)
    * [running your application](#running-your-application)
* [installing and running on linux](#installing-and-running-on-linux)
    * [installing libimobiledevice utilities](#installing-libimobiledevice-utilities)
    * [installing the application](#installing-the-application)
    * [running the application](#running-the-application)

## installing and running on macOS
For macOS users, the [ios-deploy](https://github.com/phonegap/ios-deploy) utility can handle the installation _and_ running of patched applications for you. `libimobiledevice` is available in Homebrew, so following the steps described for Linux users could be adapted for your MAC as well.

#### ios-deploy installation
Installing `ios-deploy` is as simple as:

```
npm install -g ios-deploy
```

Once installed, you should have the `ios-deploy` command available in your `PATH`.

#### running your application
To install and run your application:

1. Extract the IPA package with `unzip my-app.ipa`. This should leave you with a `Payload/` directory.
2. Attach your iOS device to your computer via USB and ensure that it is unlocked.
3. Run `ios-deploy` with `ios-deploy --bundle Payload/my-app.app -W -d` where `my-app.app` is the folder where your application lives.

You should see the `lldb` debugger pop up and print out a 'success' message. The application you are running will appear to be in a 'frozen' state for a while. This is because the Frida gadget takes some time to finish loading. After some time, you should be able to run `objection explore` and land in a successfully connected prompt :)

## installing and running on Linux
Using Linux, [libimobiledevice](http://www.libimobiledevice.org/) is a great set of tools to accomplish the IPA installation and running thereof.

**NOTE:** Unfortunately, I just cant get `ideviceinstaller` to work on Kali Linux. If anyone has some insight, or can get it to work, _please_ let me know!

#### installing libimobiledevice utilities
Depending on your distribution, `libimobiledevice` and `ideviceinstaller` may already be in its package repositories. Using an installation of Kali Linux, the required utilities provided by `libimobiledevice` can be installed with `apt install libimobiledevice-utils ideviceinstaller`.

Using Arch linux, `pacman` can be used to install `libimobiledevice` from 'Extras', and AUR used to install [ideviceinstaller](https://aur.archlinux.org/packages/ideviceinstaller-git/).

Of course, you can also compile the utilities from source, grabbing libimobiledevice from [here](https://github.com/libimobiledevice/libimobiledevice) and ideviceinstaller from [here](https://github.com/libimobiledevice/ideviceinstaller).
 
Once installed, various 'idevice*' commands should be available, such as `idevicestatus` and `idevicedebug`.

#### installing the application
Installing an IPA from a Linux based host can be done with `ideviceinstaller`, installed in the previous step. All you need is the unlocked, connected iOS device and the IPA to install. In our case, the IPA is the one we patched and code signed as described in the [Patching iOS Applications](Patching-iOS-Applications) article.

To install your patched IPA, simply run:
```
$ ideviceinstaller -i my-app-frida-codesigned.ipa
WARNING: could not locate iTunesMetadata.plist in archive!
Copying 'my-app-frida-codesigned.ipa' to device... DONE.
Installing 'com.sensepost.myapp'
Install: CreatingStagingDirectory (5%)
Install: ExtractingPackage (15%)
Install: InspectingPackage (20%)
Install: TakingInstallLock (20%)
Install: PreflightingApplication (30%)
Install: InstallingEmbeddedProfile (30%)
Install: VerifyingApplication (40%)
Install: CreatingContainer (50%)
Install: InstallingApplication (60%)
Install: PostflightingApplication (70%)
Install: SandboxingApplication (80%)
Install: GeneratingApplicationMap (90%)
Install: Complete
```

Once this process is complete, the application should be available on the iOS devices home screen. Special note should be taken on the line that reads `Installing 'com.sensepost.myapp'`. This is telling you what the "bundle identifier" is for the application you are installing. You will need this to run the application itself.

#### running the application
Ro tun the application, simply run the `idevicedebug` command, specifying the bundle identifier for the app as the last argument. So, if we were to run a SensePost application:

```
idevicedebug -d run com.sensepost.myapp
```

And thats it. You can now connect the `objection explore` REPL!