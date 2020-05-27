Frida supports Gadget configurations and is documented [here](https://frida.re/docs/gadget/). By extension, `objection`'s patcher have support to add these configuration files when you patch applications.

The main configurations you may be interested in would typically be:

- Overriding any default gadget behaviour such as pausing application execution.
- Modifying the host & port combination that the Gadget will listen on.
- Permanently applying instrumentation logic (via an embedded script) to a patched application.

# toc.

- [gadget recap](#quick-gadget-recap)
- [gadget configuration](#gadget-configuration)
- [objection implementation](#objection-implementation)
  - [android](#android)
  - [ios](#ios)
- [embedding instrumentation](#embedding-instrumentation)
- [full android script embedding example](#full-android-script-embedding-example)

## quick gadget recap

Remember that when you patch an application on both iOS and Android, the platform and architecture specific shared library is loaded as soon as possible, opening the socket that allows for the Frida client and `objection` to connect and do its thing. The final patched application will have the shared library embedded so that it could be loaded, and some binary artefact (or smali) would have been modified to load it.

## gadget configuration

On either platform, when the gadget initialises it checks for a configuration file right next to the shared library with the same name as the shared library and the `.config` suffix. The configuration file comes in the form of a small JSON structure that looks as follows (and is also the defaults for the Gadget):

```json
{
  "interaction": {
    "type": "listen",
    "address": "127.0.0.1",
    "port": 27042,
    "on_load": "wait"
  }
}
```

For details about each of the keys, refer to the Frida Gadget documentation [here](https://frida.re/docs/gadget/)

## objection implementation

Depending on the platform you are patching, objection lets you specify a path to an optional configuration file you want to have embedded with the gadget. Typically this means you will have a file called something like `config.json` with your desired configuration. Next, you will tell the appropriate patcher that this is the configuration file to include with the `--gadget-config` flag. For example: 

```text
objection patchipa -s app.ipa --gadget-config config.json
```

### android
For Android and the `patchapk` sub command, the flags are detailed from the `--help` menu as:

```text
$ objection patchapk --help
Usage: objection patchapk [OPTIONS]

  Patch an APK with the frida-gadget.so.

Options:
  [ ... ]

  -c, --gadget-config TEXT       The gadget configuration file to use. Refer
                                 to https://frida.re/docs/gadget/ for more
                                 information.
  -l, --script-source TEXT       A script file to use with the the "path"
                                 config type. Specify "libfrida-
                                 gadget.script.so" as the "path" in your
                                 config.
  [ ... ]
```

*Important:* Take note of the path name hint for the `--script-source` flag for Android. This is the path you should use in your configuration file.

### ios
For iOS and the `patchipa` sub command, the flags are detailed from the `--help` menu as:

```text
$ objection patchipa --help

  [ ... ]

  -C, --gadget-config TEXT       The gadget configuration file to use. Refer
                                 to https://frida.re/docs/gadget/ for more
                                 information.

  -l, --script-source TEXT       A script file to use with the the "path"
                                 config type. Remember that use the name of
                                 this file in your "path". It will be next to
                                 the config.
  [ ... ]
```

## embedding instrumentation

Running instrumentation in an autonomous manner can be done by setting the `type` as `script` and specifying the appropriate `path` value in the Gadget configuration file. For example, for Android:

```json
{
  "interaction": {
    "type": "script",
    "path": "libfrida-gadget.script.so"
  }
}
```

Your instrumentation source needs to be specified with the `--script-source` flag, telling objection which file has the source for it. Next, to embed your instrumentation, run the patchapk command as follows (assuming your instrumentation lives in `index.js`:

```text
objection patchapk -s app.apk -a armeabi-v7a -c config.json -l index.js
```

Keep in mind that in this mode you will not be able to connect other Frida clients as no socket is opened for it!

### script file name notes
For Android, you have to use the path as `libfrida-gadget.script.so` (note the .so) at the end. For iOS, this can be anything, just make sure you update the `path` value in your configuration file to patch whatever you called it on disk.

## full android script embedding example

A full example for Android would be using the following configration file and instrumentation file to print the current timestamp every second so that you can view them in `adb logcat`.

The `config.json` file:

```json
{
  "interaction": {
    "type": "script",
    "path": "libfrida-gadget.script.so"
  }
}
```

The `index.js` file:

```javascript
var android_log_write = new NativeFunction(
    Module.getExportByName(null, '__android_log_write'),
    'int',
    ['int', 'pointer', 'pointer']
);

var tag = Memory.allocUtf8String("[frida-sript]");

var work = function() {
    setTimeout(function() {
        android_log_write(3, tag, Memory.allocUtf8String("ping @ " + Date.now()));
        work();
    }, 1000);
}

work();
```

To patch those into a target application with `objection`:

```
objection patchapk -s app.apk -c config.json -l index.js
```

Next, launching the application and inspecting `adb logcat` (maybe using something like [pidcat](https://github.com/JakeWharton/pidcat/)) you should see some output and your application automatically resuming:

```text
          [frida-sript]  D  ping @ 1590572522509
                         D  ping @ 1590572523510
                         D  ping @ 1590572524512
                         D  ping @ 1590572525514
                         D  ping @ 1590572526515
```