Before you can use any of the `objection` commands on an iOS application, the application's IPA itself needs to be patched to load the `FridaGadget.dylib` on start. To patch an IPA though, a few things needs to be done in preparation, such as getting an `embedded.mobileprovision` file, as well as a code signing certificate from Apple. Once you have these, `objection` has a `pitch_ipa` subcommand that will help you take care of the rest.

**Note:** Unfortunately, the only way to get a an iOS IPA patched and signed at the moment is by using a computer running macOS (maybe a hackintosh works?). If someone knows of another way, I'm all ears! :)

## preparations - xcode
First and foremost, you need to register for an Apple Developer account. A free one works fine, but you need to keep in mind that you will only be allowed to run patched applications for 7 days, before repeating the patching/signing/installation process. 

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
  1) 0C2E8200D48DD49CA12CDBF4929B52F1A282D1DA "iPhone Developer: xxxxx@gmail.com (XZ9U7UBAEL)"
     1 valid identities found
```

## preparations - mobileprovision
With XCode ready we can now generate the `.mobileprovison` file we need. This file contains some certificate information as well as the entitlements groups (see the keychain article for more about this) for the application. To generate the `.mobileprovision` file, all we need to do is build and deploy a blank iOS application to an iOS device. The build process will leave a `embedded.mobileprovision` file in `~/Library/Developer/Xcode/DerivedData/` for us to pick up and re-use. When you run the `objection patch_ipa` command, it will automatically search for a valid `embedded.mobileprovision` file in `~/Library/Developer/Xcode/DerivedData/` to use.

So, to build and deploy a blank iOS application using XCode to get the `embedded.mobileprovision` file:

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
Verify the Developer App certificate for your account is trusted on your device. Open Settings on sensepost’s iPad and navigate to General -> Device Management, then select your Developer App certificate to trust it.
```
What this means is that you need to also trust the iTunes account you used to run code on the iOS device you have.  This is pretty easy. Simply navigate to your iOS devices Settings App -> General -> Profiles & Device Management -> Select your iTunes account from the "Developer App" section -> Select "Trust".