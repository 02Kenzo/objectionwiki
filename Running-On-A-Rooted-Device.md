If you have a rooted Android device, you can start `frida-server` after [installing it](https://www.frida.re/docs/android/) on your device as well as the [client tools](https://www.frida.re/docs/installation/) on your laptop. There is no need to patch applications to embed the Frida gadget as Frida can simply inject into a target process.

With everything installed, run `frida-ps -U` to list all of the processes on the device.

```txt
~ » frida-ps -U
  PID  Name
-----  ---------------------
 3637  actlmand
 3641  adbd
 4750  android.process.acore
10850  android.process.media
 3667  androidshmservice
 3577  apaservice
64562  com.dropbox.android
 3632  at_distributor
```

Great. That is all the information you need. Each of those "name"'s are what we refer to as Gadgets in objection. So, to "connect" to one of those apps, specify the name with the `--gadget` flag.

```txt
~ » objection --gadget "com.dropbox.android" explore

     _     _         _   _
 ___| |_  |_|___ ___| |_|_|___ ___
| . | . | | | -_|  _|  _| | . |   |
|___|___|_| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion)

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
com.dropbox.android on (samsung: 4.4.2) [usb] #
```