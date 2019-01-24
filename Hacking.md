So you want to dig a little deeper into the project and its source code. Maybe to test out some changes, or better yet, for a pull request! This article aims to give you an overview on the project structure.

## birds eye view
At its core, `objection` relies **heavily** on [Frida](https://www.frida.re/) to perform most of the magic. Frida, together with some purpose built hooks and the python REPL is what makes up `objection`.

A command is entered into the `objection` REPL, running a python method which may or may not expect arguments. Depending on the python method invoked, a call using the Frida [RPC](https://www.frida.re/docs/javascript-api/#rpc) to the injected [agent](https://github.com/sensepost/objection/tree/master/agent) will be made. 

## project structure
Let's take a quick look at the project structure.

### external libraries
Command line argument parsing is handled with [click](http://click.pocoo.org/5/), the REPL is handled by [python-prompt-toolkit](https://github.com/jonathanslenders/python-prompt-toolkit) and the agent is written in [TypeScript](https://www.typescriptlang.org/) and [compiled](https://github.com/sensepost/objection/blob/master/agent/package.json#L9) to ES5 compatible JavaScript. If you add the `-d` flag to the `explore` command, extra debugging information would be printed to the screen during normal operation.

### code locations
* Python methods to invoke when matched to a command lives in [objection/commands](https://github.com/sensepost/objection/tree/master/objection/commands).
* The Frida agent performing the instrumentation magic lives in [objection/agent](https://github.com/sensepost/objection/tree/master/agent).
* Classes and methods responsible for the command line interface, as well as the REPL live in [objection/console](https://github.com/sensepost/objection/tree/master/objection/console).

## REPL command flow
When a command is entered in the `objection explore` REPL, the [`run_command()`](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L131) method is run to process the string input received from `prompt_toolkit`. The `run_command()` method 'explodes' the received command, honouring quotes just like a shell would, and tries to find a python method to execute with [`_find_command_exec_method `](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L200).

Commands that can be run is defined in a repository located in the `COMMANDS` variable in [repository.py](https://github.com/sensepost/objection/blob/master/objection/console/repository.py#L28), specifying an `exec` key with a value being the python method to execute. Any remaining tokens will be passed on to the python method that will get called as [arguments](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L198).

Communications with a remote FridaGadget is handled in [utils/agent.py](https://github.com/sensepost/objection/blob/master/objection/utils/agent.py). The compiled agent is [injected](https://github.com/sensepost/objection/blob/master/objection/utils/agent.py#L152) as soon as the REPL is launched. Communications with Frida occurs via RPC calls, with entry points defined in the agents [RPC directory](https://github.com/sensepost/objection/tree/master/agent/src/rpc) and exposed via the agents main [`rpc.exports`](https://github.com/sensepost/objection/blob/master/agent/src/index.ts#L8) declaration.