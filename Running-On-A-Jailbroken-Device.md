If you have a jailbroken device, you can start `frida-server` after [installing it](https://www.frida.re/docs/ios/#with-jailbreak) on your device as well as the [client tools](https://www.frida.re/docs/installation/) on your laptop. There is no need to patch applications to embed the Frida gadget as Frida can simply inject into a target process.

With everything installed, run `frida-ps -Uia` to list all of the processes on the device.

```txt
$ frida-ps -Uia
PID  Name              Identifier
---  ----------------  ---------------------------
  -  App Store         com.apple.AppStore
  -  Calendar          com.apple.mobilecal
  -  Camera            com.apple.camera
```

Great. That is all the information you need. Each of those "Identifier"'s are what we refer to as Gadgets in objection. So, to "connect" to one of those apps, specify the name with the `--gadget` flag.

```txt
~ » objection --gadget "com.apple.AppStore" explore

     _     _         _   _
 ___| |_  |_|___ ___| |_|_|___ ___
| . | . | | | -_|  _|  _| | . |   |
|___|___|_| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion)

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
com.apple.AppStore on (iPad: 8.1) [usb] #
```