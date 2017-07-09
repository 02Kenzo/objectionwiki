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
With `objection` [installed](installation), a patched IPA installed to your iOS device and with the device connected to your computer via USB, the `objection` REPL may be started with the `objection explore` command:

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
sensepost’s iPad on (iPad: 10.2.1) [usb] #
``` 

When a patched iOS application is started for the first time it will be in a paused state until you start the `objection` REPL, which will resume execution of the target application. If you need to do early instrumentation, the `explore` subcommand has the `--startup-command` and `--startup-script` arguments to help with that. Refer to the [early instrumentation](early-instrumentation) article for more information.

At this stage, you may now enter commands into the REPL as needed. For example, issuing the `ls` command will display a directory listing of the current directory. By default, starting the `objection` REPL will start in your applications main bundle path:

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # ls
Read Access
No Write Access
Type                   Perms  Read    Write    Owner           Group             Size  Creation                   Name
-------------------  -------  ------  -------  --------------  --------------  ------  -------------------------  ------------------------
NSFileTypeRegular        420  True    False    _installd (33)  _installd (33)    8063  2017-07-09 07:58:32 +0000  AppIcon40x40@2x.png
NSFileTypeRegular        420  True    False    _installd (33)  _installd (33)  191955  2017-07-09 07:58:33 +0000  Assets.car
NSFileTypeDirectory      493  True    False    _installd (33)  _installd (33)     102  1970-01-01 00:00:00 +0000  Base.lproj
NSFileTypeDirectory      493  True    False    _installd (33)  _installd (33)     102  1970-01-01 00:00:00 +0000  Frameworks
NSFileTypeRegular        420  True    False    _installd (33)  _installd (33)    1386  2017-07-09 07:58:35 +0000  Info.plist
NSFileTypeDirectory      493  True    False    _installd (33)  _installd (33)      68  1970-01-01 00:00:00 +0000  META-INF
NSFileTypeRegular        493  True    False    _installd (33)  _installd (33)  936656  2017-07-09 07:58:35 +0000  PewPew
NSFileTypeRegular        420  True    False    _installd (33)  _installd (33)       8  2017-07-09 07:58:35 +0000  PkgInfo
NSFileTypeDirectory      493  True    False    _installd (33)  _installd (33)     102  1970-01-01 00:00:00 +0000  _CodeSignature
NSFileTypeRegular        420  True    False    _installd (33)  _installd (33)    9498  2017-07-09 07:58:35 +0000  embedded.mobileprovision
NSFileTypeRegular        420  True    False    _installd (33)  _installd (33)    1673  2017-07-09 07:58:31 +0000  swapi.co.der
```

The files in the main bundle are all available by just extracting the IPA you already have, so that in itself might not be as interesting. However, other interesting directories that relate to the application in question may be enumerated using the `env` command. This will print out the locations of the applications Library, Caches and Documents directories:

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # env
Name              System    Model    Version
----------------  --------  -------  ---------
sensepost’s iPad  iOS       iPad     10.2.1

Name                           Path
-----------------------------  ------------------------------------------------------------------------------------------------------------
DocumentDirectory              /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Documents
LibraryDirectory               /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Library
CachesDirectory                /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Library/Caches
BundlePath                     /var/containers/Bundle/Application/9FCE7485-EE1B-462E-8FC5-A49974D73F4F/PewPew.app
ApplicationDirectory           /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Applications
DemoApplicationDirectory       /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Applications/Demos
DeveloperApplicationDirectory  /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Developer/Applications
UserDirectory
CoreServiceDirectory
AutosavedInformationDirectory  /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Library/Autosave Information
DesktopDirectory               /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Desktop
ApplicationSupportDirectory    /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Library/Application Support
ReceiptPath                    /private/var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/StoreKit/sandboxReceipt
ResourcePath                   /var/containers/Bundle/Application/9FCE7485-EE1B-462E-8FC5-A49974D73F4F/PewPew.app
```

It is now possible to see that the _Documents_ directory lives at _/var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Documents_, so we `cd` to that and inspect its contents:

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # cd /var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Documents
/var/mobile/Containers/Data/Application/E2933EE7-4805-4411-A764-B1CDBFA5127C/Documents
sensepost’s iPad on (iPad: 10.2.1) [usb] # ls
Read Access
Write Access
Type                 Perms  Read    Write    Owner         Group           Size  Creation                   Name
-----------------  -------  ------  -------  ------------  ------------  ------  -------------------------  -----------------
NSFileTypeRegular      420  True    True     mobile (501)  mobile (501)     276  2017-07-09 08:01:08 +0000  credentials.plist
NSFileTypeRegular      420  True    True     mobile (501)  mobile (501)   12288  2017-07-05 19:48:01 +0000  pewpew.sqlite
```

Interesting, a plist file named 'credentials'. Let take a look at whats inside:

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # ios plist cat credentials.plist
{
    password = snek;
    username = help;
}
```

This was just a small example of what is possible with `objection`. Go try it out!

## getting started (android edition)
Coming soon™