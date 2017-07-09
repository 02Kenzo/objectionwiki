This article will walk through some sample usage of `objection`. A few assumptions are made:

* It is assumed that you have successfully followed the [installation](installation) guide and have the `objection` command available in your `PATH`. 
* It is also assumed that you have a successfully patched an unencrypted IPA, or added the FridaGadget to your project and deployed it to a device.

## toc

* [command format](#command-format)
* [getting started (ios edition)](#getting-started-ios-edition)
* [getting started (android edition)](#getting-started-android-edition)

## command format
The `objection` command contains a number of subcommands. Certain subcommands may contain flags to set various configurations and options. To get a list of available options, run any `objection` command with the `--help` flag. For example:

```bash
$ objection --help
Usage: objection [OPTIONS] COMMAND [ARGS]...

       _     _         _   _
   ___| |_  |_|___ ___| |_|_|___ ___
  | . | . | | | -_|  _|  _| | . |   |
  |___|___|_| |___|___|_| |_|___|_|_|
          |___|(object)inject(ion)

       Runtime Mobile Exploration
          by: @leonjza from @sensepost

  By default, communications will happen over USB, unless the --network
  option is provided.

Options:
  -N, --network       Connect using a network connection instead of USB.
                      [default: False]
  -h, --host TEXT     [default: 127.0.0.1]
  -p, --port INTEGER  [default: 27042]
  -g, --gadget TEXT   Name of the Frida Gadget/Process to connect to.
                      [default: Gadget]
  --help              Show this message and exit.

Commands:
  device_type  Get information about an attached device.
  explore      Start the objection exploration REPL.
  patchipa     Patch an IPA with the FridaGadget dylib.
  version      Prints the current version and exists
```

The most commonly used subcommand is `explore`, which will start the `objection` exploration REPL. Considering the options in the above `--help` output, to connect to a Frida gadget hosted on the network, one would execute `objection -N explore`.

The exploration REPL will autocomplete commands when the `[TAB]` key is pressed whenever possible. If you need some help, prefixing your full command with the `help` keyword will print documentation and usage examples for the command in question.

## getting started (ios edition)
With a patched IPA installed to your iOS device and with the device connected to your computer via USB, the `objection` REPL may be started with the `objection explore` command:

```bash
$ objection explore

     _     _         _   _
 ___| |_  |_|___ ___| |_|_|___ ___
| . | . | | | -_|  _|  _| | . |   |
|___|___|_| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion)

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
skdw on (iPhone: 10.3.1) [net] #
``` 

At this stage, you may now enter commands into the REPL as needed. For example, issuing the `ls` command will display a directory listing of the current directory. By default, starting the `objection` REPL will start in your applications main bundle path too.

```
skdw on (iPhone: 10.3.1) [net] # ls
Read Access
Write Access
Type                   Perms  Read    Write    Owner      Group         Size  Creation                   Name
-------------------  -------  ------  -------  ---------  ----------  ------  -------------------------  -------------------
NSFileTypeDirectory      493  True    True     n/a (501)  staff (20)     102  2017-07-08 20:23:19 +0000  _CodeSignature
NSFileTypeRegular        420  True    True     n/a (501)  staff (20)    8146  2017-06-27 05:34:06 +0000  AppIcon40x40@2x.png
NSFileTypeRegular        420  True    True     n/a (501)  staff (20)  191955  2017-07-04 19:18:38 +0000  Assets.car
NSFileTypeDirectory      493  True    True     n/a (501)  staff (20)     102  2017-07-08 20:23:19 +0000  Base.lproj
NSFileTypeDirectory      493  True    True     n/a (501)  staff (20)     102  2017-07-08 20:23:19 +0000  Frameworks
NSFileTypeRegular        420  True    True     n/a (501)  staff (20)    1416  2017-07-05 14:25:24 +0000  Info.plist
NSFileTypeRegular        493  True    True     n/a (501)  staff (20)  915616  2017-07-08 20:23:19 +0000  PewPew
NSFileTypeRegular        420  True    True     n/a (501)  staff (20)       8  2017-07-05 14:25:24 +0000  PkgInfo
NSFileTypeRegular        420  True    True     n/a (501)  staff (20)    1673  2017-07-04 19:18:36 +0000  swapi.co.der
```

## getting started (android edition)
Coming soon™