Before you can use any of the `objection` commands on an iOS application, the application's IPA itself needs to be patched and code signed to load the `FridaGadget.dylib` on start. To patch an IPA though, a few things need to be done in preparation, such as getting an `embedded.mobileprovision` file, as well as a code signing certificate from Apple. Once you have these, `objection` has a `patchipa` subcommand that will help you take care of the rest.

**Note:** Unfortunately, the only way to get an iOS IPA patched and signed at the moment is by using a computer running macOS (maybe a hackintosh works?). If someone knows of another way, I'm all ears! :) **Only** the signing process needs macOS. Installing and running the patched IPA can be done using any other OS.

## toc
* [preparations - xcode](#preparations---xcode)
* [preparations - mobileprovision](#preparations---mobileprovision)
* [patching - dependencies](#patching---dependencies)
* [patching - patching-an-ipa](#patching---patching-an-ipa)
* [next steps](#next-steps)

## preparations - xcode
First and foremost, you need to register for an Apple Developer account. A free one works fine, but you need to keep in mind that you will only be allowed to run patched applications for 7 days, before repeating the patching/signing/installation process again. 

To register:

1. Visit https://developer.apple.com/register/ and agree to the license agreement.
2. Sign in with your AppleID in XCode by browsing to "XCode" -> "Preferences" -> "Accounts" and clicking on the "+" at the bottom left.
![appleid](https://i.imgur.com/hKLnf9p.png)
3. Click on "Manage Certificates" and click on the "+" drop down, selecting "iOS Development". XCode will generate a new code signing certificate for you to use.
![codesign](https://i.imgur.com/MMQpyjl.png)
4. Click "Done".

Once you have a developer account signed in to in XCode, you will now be able to sideload applications and run them on iOS devices. You can also check for code signing certificates that are now available in a terminal:

```
$ security find-identity -p codesigning -v
  1) 0C2E8200Dxxxx "iPhone Developer: xxxxx@gmail.com (XZ9U7UBAEL)"
     1 valid identities found
```

## preparations - mobileprovision
With XCode ready we can now generate the `.mobileprovison` file we need. This file contains some certificate information as well as the entitlements groups (see the keychain article for more about this) for the application. To generate the `.mobileprovision` file, all we need to do is build and deploy a blank iOS application to an iOS device. The build process will leave an `embedded.mobileprovision` file in `~/Library/Developer/Xcode/DerivedData/` for us to pick up and re-use. When you run the `objection patchipa` command, it will automatically search for a valid `embedded.mobileprovision` file in `~/Library/Developer/Xcode/DerivedData/` to use.

So, to build and deploy a blank iOS application using XCode to get the updated `embedded.mobileprovision` file you need:

1. Start XCode and select "Create new XCode Project"
![new](https://i.imgur.com/pgI9GjJ.png)
2. Select "iOS" -> "Single View Application" and hit next.
3. Fill in a Product name, choose a unique organization identifier and hit next.
![product](https://i.imgur.com/T2takof.png)
4. Choose where to save the project and finally click on "Create".
5. In the projects root, under the "General" tab, ensure that there are no errors with the signing certificate. Most of the time, XCode is pretty verbose with what needs to be done to fix any errors you may encounter, so just take a moment to read.
![settings](https://i.imgur.com/bIF6LaS.png)
6. When done, ensure your iOS device is plugged in. Select it as the target device in the top left corner, and hit the big play button. This process will compile the blank app for the target device, and leave that `embedded.mobileprovision` file on disk for us to pick up later.
7. Trust your iTunes account to run code on your iOS device.

Running your blank application for the first time on an iOS device will most probably result in a error that reads something like:
```
Verify the Developer App certificate for your account is trusted on your device. Open Settings on your device and navigate to General -> Device Management, then select your Developer App certificate to trust it.
```
What this means is that you need to also trust the iTunes account you used to run code on the iOS device you have.  This is pretty easy. Simply navigate to your iOS devices Settings App -> General -> Profiles & Device Management -> Select your iTunes account from the "Developer App" section -> Select "Trust".

## patching - dependencies
Phew, thats quite a bit of setup work needed. Luckily, the only part you would need to redo often is to run the blank sample app on your iOS device to a generate new, valid `embedded.mobileprovision` file.

The next part is to prepare the commands needed for the IPA patching process. `objection patchipa` is a command that basically wraps around several other system commands, automating the patching process as far as possible. Naturally, those commands need to be installed and available first. They are:

* `applesign` - from: https://github.com/nowsecure/node-applesign
* `insert_dylib` - from: https://github.com/Tyilo/insert_dylib
* `security`, `codesign`, xcodebuild` - macOS/XCode commands
* `zip` & `unzip` - builtin, or just installed using `homebrew`

Most of these dependencies are really easy to solve as they are either already part of macOS, or can be installed using `homebrew`. As for `applesign` and `insert_dylib` though, those can be quickly installed as follows:

#### applesign
Install with: `npm install -g applesign`. If you dont have `npm`, then `brew install npm` can sort you out quickly.

#### insert_dylib
This utility will be compiled from source and installed. To do that:

```bash
git clone https://github.com/Tyilo/insert_dylib
cd insert_dylib
xcodebuild
cp build/Release/insert_dylib /usr/local/bin/insert_dylib
```

## patching - patching an IPA
With **all** of the above dependencies solved, we can finally patch an actual IPA. The patching process itself is as simple as:

```
objection patchipa --source my-app.ipa --codesign-signature 0C2E8200Dxxxx
```

This command will extract the IPA, locate the app binary, patch it to load the `FridaGadget.dylib`, codesign the dylib and applications binary and repackage it for you.

```
$ objection patchipa --source "DVIA-nowatch.ipa" --codesign-signature 0C2E8200D4XXXX
Using Gadget version: 10.5.15
No provision file specified, searching for one...
Found provision /Users/leonjza/Library/Developer/Xcode/DerivedData/PewPew-ctjryaatvxygoabrwrllkmwxlvgn/Build/Products/Debug-iphoneos/PewPew.app/embedded.mobileprovision expiring in 4 days, 19:10:47.596338
Found a valid provisioning profile
Working with app: DamnVulnerableIOSApp.app
Bundle identifier is: com.highaltitudehacks.dvia
Creating Frameworks directory for FridaGadget...
Codesigning 1 .dylib's with signature 0C2E8200D4XXXX
Code signing: FridaGadget.dylib
Creating new archive with patched contents...
Codesigning patched IPA...
Cannot find entitlements in binary. Using defaults

Copying final ipa from /var/folders/nn/7rzmzs_920n8qvff8n9nf1rm0000gn/T/DVIA-nowatch-frida-codesigned.ipa to current directory...
Cleaning up temp files...
```

## next steps
Once you have a patched IPA ready, it's time to move on to side loading and running your application. See the [Running Patched iOS Applications](Running-Patched-iOS-Applications) article for more information.