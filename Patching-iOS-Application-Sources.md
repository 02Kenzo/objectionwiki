Sometimes, you are lucky enough to have the source code for the application you want to test. In these cases its possible to simply load the `FridaGadget` as part of the Xcode project, deploy it on an iOS device and connect `objection`.

Lets walk through a sample project to get this going. We are going to use a simple currency converter application that can be found here: [https://github.com/nicklockwood/Concurrency](https://github.com/nicklockwood/Concurrency). Clone the app somewhere on your disk and open it in Xcode.

![xcodeapp](https://i.imgur.com/IiFdivF.png)

Within Xcode, navigate to the projects root and fix the signing errors you may have by choosing a new, unique bundle identifier and selecting "Automatically manage signing".

## get the gadget
With the source code cloned, lets create a directory that will hold our Frida gadget. If you cloned the sample application mentioned above, `cd` to the `Concurrency` directory. Then:

* `mkdir Frameworks` and
* `cd Frameworks`

Next, download the latest Frida gadget with:

```
curl -O https://build.frida.re/frida/ios/lib/FridaGadget.dylib
```

## code sign the gadget
We need to code sign the Frida gadget we just downloaded. If you don't have a code signing certificate yet, check out the [Patching-iOS-Applications -> preparing Xcode](Patching-iOS-Applications#preparations---xcode) article for a quick how to on getting one (don't worry, its free!). You can list your code signing certificates with:

```
security find-identity -p codesigning -v
```

Pick the appropriate certificate and run `codesign` on the gadget you just downloaded by replacing `<Identity>` with the hash of your certificate:

```
codesign -f -s <Identity> FridaGadget.dylib
```

## add the frameworks directory
Next, within Xcode we need to locate the projects `AppDelegate.m` file. We are going to be adding the newly created Frameworks directory right next to this file. Once located, drag the `Frameworks` directory you created using finder into Xcode, dropping it next to the `AppDelegate.m` file.

![appdelegate](https://i.imgur.com/uJYJ9aF.png)

A new prompt will show with some options. Make sure you select _Copy items of needed_ and _Create folder references_. When done, you should see a new `Frameworks` folder next to the `AppDelegate.m` file.

![newframeworks](https://i.imgur.com/kBBp4wK.png)

## link library
With the Frameworks folder and therefore the Frida gadget added to the project, its time to tell Xcode to link the new dylib to the library. To do this, Navigate to the project's root on the left, and select the _Build Phases_ tab on the top right. Find the section title _Link Binary With Libraries_ and drag the dylib from the Frameworks folder on the left of Xcode to the existing list.

![linkdylib](https://i.imgur.com/AEpbH5o.png)

## build and resolve errors
At this stage, you can go ahead and try and build the project. Depending on the project itself, you may encounter various errors. 

If one of those errors relate to something like "FridaGadget.dylib does not contain bytecode", you can easily fix this by disabling it.

![bitcodeerror](https://i.imgur.com/kFO8FuG.png)

For this specific error, it can be resolved by navigating to the projects root again, selecting _Build Settings_ and flipping the _Enable Bitcode_ to _No_.

![fixedbitcode](https://i.imgur.com/01tM7wo.png)

## connect objection
Once the application has built successfully, you can deploy it on an iOS device (or the simulator), watching the console for when the Frida gadget as booted and is waiting for connections.

![fridalistening](https://i.imgur.com/kB0H3KL.png)

Once this has happened, `objection explore` away!