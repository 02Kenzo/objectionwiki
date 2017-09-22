If you have a jailbroken device, you can start `frida-server` after [installing it](https://www.frida.re/docs/ios/#with-jailbreak) on your device as well as the [client tools](https://www.frida.re/docs/installation/) on your laptop.

With everything installed, run `frida-ps -U` to list all of the processes on the device.

```txt
~ » frida-ps -U
 PID  Name
----  ----------------
3202  App Store
3004  Cydia
3165  IPA Installer
 371  Mail
2844  Settings
3167  AGXCompilerServi
```

Great. That is all the information you need. Each of those "name"'s are what we refer to as Gadgets in objection. So, to "connect" to one of those apps, specify the name with the `--gadget` flag.

```txt
~ » objection --gadget "App Store" explore

     _     _         _   _
 ___| |_  |_|___ ___| |_|_|___ ___
| . | . | | | -_|  _|  _| | . |   |
|___|___|_| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion) v1.1.6

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
com.apple.AppStore on (iPad: 8.1) [usb] #
```