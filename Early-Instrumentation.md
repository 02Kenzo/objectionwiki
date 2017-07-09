Sometimes it is necessary to run commands and hook functions just before the execution of the application in question is resumed. Examples of this might be when testing jailbreak detections, or when an API call that has its SSL certificate pinned is made on startup.

## understanding early instrumentation
Under normal circumstances, when `objection` starts up, it will search for and connect to the FridaGadget that is available either via USB or the network (depending on the flags used to start up). Once connected, small hooks are run to determine the available runtimes and environment information. These values are used by features such as the file manager to know how to work with the remote filesystem and to populate information within the `objection` REPL's prompt.

When this startup process is followed, the application is also automatically resumed, but, with the added side effect that you may miss any opportunities to hook and work with code executed on startup.

## startup commands and scripts
To combat this, the `explore` subcommand (that starts the REPL) provides two optional flags that can be used to execute an `objection` command or your own Frida scripts before any of the environment related information gathering hooks are run. This makes it possible to hook methods such as those that check SSL pinning information or jailbreak information as the execution of the application in question is resumed.

**Note:** When running within the REPL, hooks that are run attempt to take care to catch any errors that have occurred and report them. When running your own Frida scripts, these protections are not automatically added. Take special care when developing hooks that you catch errors and report in them to make debugging easier!

## examples
As an example, lets look at a sample iOS application that performs jailbreak detection as soon as the application has started up. I want to test the behaviour of the application when it thinks it is running on a jailbroken device, so I need to run the `ios jailbreak simulate` command. As previously mentioned, normal startup of the `exploration` REPL does not give one enough time to apply the hooks for this, so we will use the early instrumentation example!

It will work as follows. I start the patched application with a debugger attached which will result in the execution flow pausing. Then we start the `exploration` REPL with a `--startup-command` :

```
$ objection explore --startup-command 'ios jailbreak simulate'
Running a startup command...
Job: cf1c8381-5c55-4658-b9fd-2dff56c320d5 - Starting
Job: cf1c8381-5c55-4658-b9fd-2dff56c320d5 - Started
[2dff56c320d5] [jailbreak-simulate] A failed lookup for /bin/sh occurred. Marking it as successful.

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

The applications jailbreak detection routine checked for the existence of `/bin/sh`, where the jailbreak simulation command replied positively resulting in the application thinking it is running on a jailbroken device. This all occurred before the REPL finished loading, resulting in a successful early instrumentation.