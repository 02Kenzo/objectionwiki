Before you can use any of the `objection` commands on an iOS application, the application's IPA itself needs to be patched to load the `FridaGadget.dylib` on start. To patch an IPA though, a few things needs to be done in preparation, such as getting an `embedded.mobileprovision` file, as well as a code signing certificate from Apple. Once you have these, `objection` has a `pitch_ipa` subcommand that will help you take care of the rest.

**Note:** Unfortunately, the only way to get a an iOS IPA patched and signed at the moment is by using a computer running macOS (maybe a hackintosh works?). If someone knows of another way, I'm all ears! :)

## preparations
First and foremost, you need to register for an Apple Developer account. A free one works fine, but you need to keep in mind that you will only be allowed to run patched applications for 7 days, before repeating the patching/signing/installation process. 

To register:

1. Visit https://developer.apple.com/register/ and agree to the license agreement.
2. Sign in with your AppleID in XCode by browsing to "XCode" -> "Preferences" -> "Accounts" and clicking on the "+" at the bottom left.

![appleid](https://i.imgur.com/hKLnf9p.png)

2. Sign