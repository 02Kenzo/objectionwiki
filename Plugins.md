It is possible to extend the core features of objection using plugins. In short, plugins allow one to use a single, self-contained python file (which may or may not include the necessary Frida scripts), and load it as part of the default objection REPL. This means, depending on what your plugin does, a tab completable command will be added to the objection REPL to execute whatever your plugin is built to do. Frida scripts outside of the default objection agent may be run using a plugin, meaning that one may not necessarily need to edit the agent to get new features locally, quickly.

# toc

- [plugin structure](#plugin-structure)
- [loading plugins](#loading-plugins)
- [plugin class](#plugin-class)
  - [plugin frida script](#plugin-frida-script)
  - [plugin script session](#plugin-script-session)
- [full example plugin](#full-example-plugin)

## plugin structure

Plugins only have a few required pieces of information for objection to be able to load it. At a high level, an `__init__.py` file should exist in a folder, with two variables specified. Those variables are `namespace` and `plugin`.

The `namespace` variable declares the namespace this plugin should be in. This string value is used internally to reference the plugin as well as to determine where the commands defined in an `implementation` should go.
The `plugin` variable should be a Class instance that inherits `from objection.utils.plugin.Plugin`.

An example plugin may be seen in the objection test suite [here](https://github.com/sensepost/objection/blob/master/tests/data/plugin/__init__.py).

## loading plugins

Two options exist to load plugins in objection. The first is by specifying a folder to scan for plugin repositories with the `-P/--plugin-folder` flag added to the `explore` CLI command. The second is by running the `plugin load` command, specifying the path to a repository inside of the objection REPL.

When specifying the `-P/--plugin-folder` flag, the target directory should contain subdirectories, each being a valid objection plugin.

## plugin class

### plugin frida script

While it's not a requirement to use a Frida script as part of your plugin, some hints are necessary to give your extended Plugin class an idea on where to look for the Frida script source code. There are a few options available:

- Specifying the `self.script_src` attribute with the Frida script source.
- Specifying the `self.script_path` attribute with an absolute path to a file containing the Frida script source.
- Not specifying any of the attributes such as `script_src` or `script_path`, but instead simply placing your Frida script sources in a file called `index.js` next to the plugin's `__init__.py` file. In this case, objection will automatically read this file.

### plugin script session

Scripts loaded in a plugin may be injected using the base `Plugin` class' `inject()` method. This is a requirement before you will be able to interact with your Frida scripts exposed `rpc.exports`. This method will most commonly be called in your extended `Plugin` class' `__init__` method. Once called, the extended `Plugin` class will have an `api` attribute, which are the snake case versions of the methods exposed using your scripts `rpc.exports`.

## full example plugin

The following plugin, when loaded, adds the `plugin frida info` command to the objection REPL.

```python
__description__ = "An example plugin, also used in a UnitTest"

from objection.utils.plugin import Plugin

s = """
rpc.exports = {
    getInformation: function() {
        console.log('hello from Frida');    // direct output
        send('Incoming message');           // output via send for 'message' signal
        return Frida.version;               // return type
    }
}
"""


class VersionInfo(Plugin):
    """ VersionInfo is a sample plugin to get Frida version information """

    def __init__(self, ns):
        """
            Creates a new instance of the plugin

            :param ns:
        """

        self.script_src = s
        # self.script_path = os.path.join(os.path.dirname(__file__), "script.js")

        implementation = {
            'meta': 'Work with Frida version information',
            'commands': {
                'info': {
                    'meta': 'Get the current Frida version',
                    'exec': self.version
                }
            }
        }

        super().__init__(__file__, ns, implementation)

        self.inject()

    def version(self, args: list):
        """
            Tests a plugin by calling an RPC export method
            called getInformation, and printing the result.

            :param args:
            :return:
        """

        v = self.api.get_information()
        print('Frida version: {0}'.format(v))


namespace = 'version'
plugin = VersionInfo
```