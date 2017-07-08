This article will walk through some sample usage of `objection`. A few assumptions are made:

* It is assumed that you have successfully followed the [installation](installation) guide and have the `objection` command available in your `PATH`. 
* It is also assumed that you have a successfully patched an unencrypted IPA, or added the FridaGadget to your project and deployed it to a device.

## toc

* [command format](#command-format)
* [getting started (ios edition)](#getting-started-ios-edition)

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

The most commonly used subcommand is `explore`, which will start the `objection` exploration REPL. Considering the options in the above `--help` output, to connect to a Frida gadget hosted on the network, one would execute `objection -N explore`:

```bash
$ objection -N explore

     _     _         _   _
 ___| |_  |_|___ ___| |_|_|___ ___
| . | . | | | -_|  _|  _| | . |   |
|___|___|_| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion) v0.1

     Runtime Mobile Exploration
        by: @leonjza from @sensepost

[tab] for command suggestions
skdw on (iPhone: 10.3.1) [net] #
``` 

## getting started (ios edition)
With a patched IPA installed to your iOS device, the `objection` repl may be started with the `objection explore` command.