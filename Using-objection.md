This article will walk through some sample usage of `objection`. A few assumptions are made:

* You have successfully followed the [installation](installation) guide and have the `objection` command available in your `PATH`. 
* You have successfully patched your application (ie: [an IPA](Patching-iOS-Applications) or [an APK](Patching-Android-Applications)), or added the FridaGadget to your project and deployed it to a device.

## toc

* [command format](#command-format)
* [getting started (ios edition)](#getting-started-ios-edition)
* [getting started (android edition)](#getting-started-android-edition)

## command format
The `objection` command contains a number of subcommands. Certain subcommands may contain flags to set various configurations and options. To get a list of available options, run any `objection` command with the `--help` flag. For example:

```txt
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
  patchapk     Patch an APK with the frida-gadget.so.
  patchipa     Patch an IPA with the FridaGadget dylib.
  version      Prints the current version and exists.
```

The most commonly used subcommand is `explore`, which will start the `objection` exploration REPL. Considering the options in the above `--help` output, to connect to a Frida gadget hosted on the network, one would execute `objection -N explore`.

The exploration REPL will autocomplete commands when the `[TAB]` key is pressed whenever possible. If you need some help, prefixing your full command with the `help` keyword will print documentation and usage examples for the command in question.

## getting started (ios edition)
With `objection` [installed](installation), a [patched IPA](Patching-iOS-Applications) installed to your iOS device and with the device connected authorized to your computer via USB, the `objection` REPL may be started with the `objection explore` command:

```txt
$ objection explore

     _     _         _   _
 ___| |_  |_|___ ___| |_|_|___ ___
| . | . | | | -_|  _|  _| | . |   |
|___|___|_| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion)

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] #
``` 

When a patched iOS application is started for the first time it will be in a paused state until you start the `objection` REPL, which will resume execution of the target application. If you need to do early instrumentation, the `explore` subcommand has the `--startup-command` and `--startup-script` arguments to help with that. Refer to the [early instrumentation](early-instrumentation) article for more information.

At this stage, you may now enter commands into the REPL as needed. For example, issuing the `ls` command will display a directory listing of the current directory. By default, starting the `objection` REPL will start in your applications main bundle path:

```
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # ls
NSFileType      Perms  NSFileProtection    Read    Write    Owner           Group           Size       Creation                   Name
------------  -------  ------------------  ------  -------  --------------  --------------  ---------  -------------------------  ------------------------
Regular           420  None                True    False    _installd (33)  _installd (33)  7.9 KiB    2017-08-25 05:33:48 +0000  AppIcon40x40@2x.png
Regular           420  None                True    False    _installd (33)  _installd (33)  187.5 KiB  2017-08-25 05:33:49 +0000  Assets.car
Directory         493  None                True    False    _installd (33)  _installd (33)  102.0 B    1970-01-01 00:00:00 +0000  Base.lproj
Directory         493  None                True    False    _installd (33)  _installd (33)  102.0 B    1970-01-01 00:00:00 +0000  Frameworks
Regular           420  None                True    False    _installd (33)  _installd (33)  1.3 KiB    2017-08-25 05:33:49 +0000  Info.plist
Directory         493  None                True    False    _installd (33)  _installd (33)  68.0 B     1970-01-01 00:00:00 +0000  META-INF
Regular           493  None                True    False    _installd (33)  _installd (33)  915.0 KiB  2017-09-14 06:18:04 +0000  PewPew
Regular           420  None                True    False    _installd (33)  _installd (33)  8.0 B      2017-08-25 05:33:49 +0000  PkgInfo
Directory         493  None                True    False    _installd (33)  _installd (33)  102.0 B    1970-01-01 00:00:00 +0000  _CodeSignature
Regular           420  None                True    False    _installd (33)  _installd (33)  9.3 KiB    2017-09-14 06:18:03 +0000  embedded.mobileprovision
Regular           420  None                True    False    _installd (33)  _installd (33)  1.6 KiB    2017-08-16 17:05:01 +0000  swapi.co.der

Readable: Yes  Writable: Yes
```

The files in the main bundle are all available by just extracting the IPA you already have, so that in itself might not be as interesting. However, other interesting directories that relate to the application in question may be enumerated using the `env` command. This will print out the locations of the applications Library, Caches and Documents directories:

```
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # env

Name                           Path
-----------------------------  ------------------------------------------------------------------------------------------------------------
DocumentDirectory              /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Documents
LibraryDirectory               /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Library
CachesDirectory                /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Library/Caches
BundlePath                     /var/containers/Bundle/Application/4CF3BFEE-5964-4A01-B698-18AC2500EEA1/PewPew.app
ApplicationDirectory           /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Applications
DemoApplicationDirectory       /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Applications/Demos
DeveloperApplicationDirectory  /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Developer/Applications
UserDirectory
CoreServiceDirectory
AutosavedInformationDirectory  /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Library/Autosave Information
DesktopDirectory               /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Desktop
ApplicationSupportDirectory    /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Library/Application Support
ReceiptPath                    /private/var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/StoreKit/sandboxReceipt
ResourcePath                   /var/containers/Bundle/Application/4CF3BFEE-5964-4A01-B698-18AC2500EEA1/PewPew.app
```

It is now possible to see that the _Documents_ directory lives at _/var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Documents_, so we `cd` to that and inspect its contents:

```
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # cd /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Documents
/var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Documents

com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # ls
NSFileType      Perms  NSFileProtection                      Read    Write    Owner         Group         Size      Creation                   Name
------------  -------  ------------------------------------  ------  -------  ------------  ------------  --------  -------------------------  -----------------
Regular           420  CompleteUntilFirstUserAuthentication  True    True     mobile (501)  mobile (501)  4.0 B     2017-09-04 13:47:08 +0000  api.db
Regular           420  None                                  True    True     mobile (501)  mobile (501)  4.0 B     2017-09-04 13:48:49 +0000  bar.png
Regular           420  CompleteUnlessOpen                    True    True     mobile (501)  mobile (501)  4.0 B     2017-09-04 13:44:27 +0000  blueprint.txt
Regular           420  CompleteUntilFirstUserAuthentication  True    True     mobile (501)  mobile (501)  275.0 B   2017-09-15 09:43:25 +0000  credentials.plist
Regular           420  CompleteUntilFirstUserAuthentication  True    True     mobile (501)  mobile (501)  12.0 KiB  2017-08-10 09:50:08 +0000  pewpew.sqlite
Regular           420  Complete                              True    True     mobile (501)  mobile (501)  4.0 B     2017-09-04 13:42:57 +0000  secret.txt

Readable: Yes  Writable: Yes
```

Interesting, a plist file named 'credentials' and a sqlite database. Let take a look at whats inside the plist. It is possible to download the raw `.plist` file from the mobile devices filesystem to your local filesystem and inspect it that way (In the example below, commands prefixed with a `!` are run as OS commands):

```
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # file download credentials.plist creds.plist
Downloading /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Documents/credentials.plist to creds.plist

com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # !cat creds.plist
Running OS command: cat creds.plist

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>password</key>
	<string>snek</string>
	<key>username</key>
	<string>bob</string>
</dict>
</plist>
```

Or, you can use the inline helper tool that will parse the plist and dump its contents. This is useful as it is not always possible to easily read the raw file:

```
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # ios plist cat credentials.plist
{
    password = snek;
    username = bob;
}
```

We can query the sqlite database as well to get an idea of its table structure and contents. Lets connect and have a look at the table structure first:

```
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # sqlite connect pewpew.sqlite
Caching local copy of database file...
Downloading /var/mobile/Containers/Data/Application/6CDB1F03-8747-42DE-8376-C4008A2E0731/Documents/pewpew.sqlite to /var/folders/nn/7rzmzs_920n8qvff8n9nf1rm0000gn/T/tmpxv1u2gt5objection.sqlite
Validating SQLite database format
Connected to SQLite database at: pewpew.sqlite

com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # sqlite execute schema
------------------------------------------------------------------
CREATE TABLE data ( key text NOT NULL UNIQUE, value text NOT NULL)
------------------------------------------------------------------
```

As you can see, the database contains a single table called _data_, with two columns. Lets execute a query to have a look at the data itself:

```
com.sensepost.ipewpew on (iPad: 10.2.1) [usb] # sqlite execute query select * from data;
-----------  -------------------------------------
credentials  {"username":"help","password":"snek"}
-----------  -------------------------------------
```

## getting started (android edition)
With `objection` [installed](installation), a [patched APK](Patching-Android-Applications) installed to your Android device and with the device connected and authorized to your computer via USB, the `objection` REPL may be started with the `objection explore` command:

```txt
$ objection explore

     _     _         _   _
 ___| |_  |_|___ ___| |_|_|___ ___
| . | . | | | -_|  _|  _| | . |   |
|___|___|_| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion)

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
com.opera.mini.native on (samsung: 6.0.1) [usb] #
``` 

When a patched Android application is started for the first time it will be in a paused state until you start the `objection` REPL, which will resume execution of the target application. If you need to do early instrumentation, the `explore` subcommand has the `--startup-command` and `--startup-script` arguments to help with that. Refer to the [early instrumentation](early-instrumentation) article for more information.

At this stage, you may now enter commands into the REPL as needed. For example, issuing the `ls` command will display a directory listing of the current directory. By default, starting the `objection` REPL will start in your applications main bundle path:

```
com.opera.mini.native on (samsung: 6.0.1) [usb] # ls
Type       Last Modified            Read    Write    Hidden    Size     Name
---------  -----------------------  ------  -------  --------  -------  ------------
Directory  2017-09-05 14:03:20 GMT  True    False    False     4.0 KiB  lib
Directory  2017-09-15 09:48:59 GMT  True    True     False     4.0 KiB  cache
Directory  2017-09-11 12:12:08 GMT  True    True     False     4.0 KiB  code_cache
Directory  2017-09-05 14:03:41 GMT  True    True     False     4.0 KiB  no_backup
Directory  2017-09-05 14:03:45 GMT  True    True     False     4.0 KiB  app_libs
Directory  2017-09-15 09:49:22 GMT  True    True     False     4.0 KiB  files
Directory  2017-09-15 09:49:22 GMT  True    True     False     4.0 KiB  shared_prefs
Directory  2017-09-05 14:04:07 GMT  True    True     False     4.0 KiB  app_webview
Directory  2017-09-05 14:03:41 GMT  True    True     False     4.0 KiB  app_textures
Directory  2017-09-15 09:49:14 GMT  True    True     False     4.0 KiB  app_opera
Directory  2017-09-05 14:09:04 GMT  True    True     False     4.0 KiB  databases

Readable: Yes  Writable: Yes
```

Other interesting directories that relate to the application in question may be enumerated using the `env` command. This will print out the locations of the applications Files, Caches and other directories:

```
com.opera.mini.native on (samsung: 6.0.1) [usb] # env

Name                    Path
----------------------  ------------------------------------------------------------
filesDirectory          /data/user/0/com.opera.mini.native/files
cacheDirectory          /data/user/0/com.opera.mini.native/cache
externalCacheDirectory  /storage/emulated/0/Android/data/com.opera.mini.native/cache
codeCacheDirectory      /data/user/0/com.opera.mini.native/code_cache
obbDir                  /storage/emulated/0/Android/obb/com.opera.mini.native
packageCodePath         /data/app/com.opera.mini.native-1/base.apk
```

It is now possible to see that the _cacheDirectory_ directory lives at _/data/user/0/com.opera.mini.native/caches_, so we `cd` to that and inspect its contents:

```
com.opera.mini.native on (samsung: 6.0.1) [usb] # cd /data/user/0/com.opera.mini.native/cache
/data/user/0/com.opera.mini.native/cache

com.opera.mini.native on (samsung: 6.0.1) [usb] # ls
Type       Last Modified            Read    Write    Hidden    Size      Name
---------  -----------------------  ------  -------  --------  --------  ----------------------------
Directory  2017-09-05 14:03:41 GMT  True    True     False     4.0 KiB   crash_dumps
Directory  2017-09-05 14:03:42 GMT  True    True     False     4.0 KiB   webviewAppCache
Directory  2017-09-05 14:03:42 GMT  True    True     False     4.0 KiB   webviewDatabases
Directory  2017-09-15 09:48:55 GMT  True    True     False     4.0 KiB   turboproxy
File       2017-09-05 14:03:51 GMT  True    True     False     64.0 B    fhash.dat
Directory  2017-09-05 14:03:53 GMT  True    True     False     4.0 KiB   uil-images
Directory  2017-09-11 08:18:07 GMT  True    True     False     4.0 KiB   obml
Directory  2017-09-05 14:03:59 GMT  True    True     False     4.0 KiB   mini_images
File       2017-09-05 14:04:05 GMT  True    True     False     59.3 KiB  1493867303508.tmp
Directory  2017-09-11 12:12:08 GMT  True    True     False     4.0 KiB   volley
Directory  2017-09-15 09:49:04 GMT  True    True     False     4.0 KiB   org.chromium.android_webview
File       2017-09-05 14:04:18 GMT  True    True     False     521.0 B   -721711023.png
File       2017-09-08 09:06:10 GMT  True    True     False     952.0 B   raw1084510070dex
File       2017-09-11 10:32:30 GMT  True    True     False     952.0 B   raw-632611483dex
File       2017-09-08 09:06:10 GMT  True    True     False     12.4 KiB  raw1084510070dex.dex
File       2017-09-15 09:48:59 GMT  True    True     False     59.3 KiB  1496809943795.tmp
File       2017-09-11 10:32:31 GMT  True    True     False     12.4 KiB  raw-632611483dex.dex

Readable: Yes  Writable: Yes
```

We can aso download files off of the remote filesystem using the `file download` command:

```
com.opera.mini.native on (samsung: 6.0.1) [usb] # file download fhash.dat fhash.dat
Downloading /data/user/0/com.opera.mini.native/cache/fhash.dat to fhash.dat
```