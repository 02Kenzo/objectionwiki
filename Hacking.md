So you want to dig a little deeper into the project and its source code. This article aims to give you a birds eye view on the project structure, a quick flow example to run a hook and other goodies.

## birds eye view
At its core, `objection` relies **heavily** on Frida to perform most of the magic. Frida, together with some purpose built hooks and the python REPL is what makes up `objection`.

A command is entered into the `objection` REPL, dispatching a python method which may or may not expect arguments. Depending on the method, a runtime specific [hook](https://github.com/sensepost/objection/tree/master/objection/hooks) will get injected into the mobile devices process and executed. Feedback from the hook is sent back to the python environment using `send()` invocations and reported back to the screen. 

## project structure
Lets take a quick look at the project structure.

### external libraries
Command line argument parsing is handled with [click](http://click.pocoo.org/5/), the REPL is handled by [python-prompt-toolkit](https://github.com/jonathanslenders/python-prompt-toolkit) and hook compilation (basically adding the global error handler per runtime) is handled with [jinja](http://jinja.pocoo.org/docs/2.9/).

### code locations
* Python methods to invoke when matched to a command live in [objection/commands](https://github.com/sensepost/objection/tree/master/objection/commands).
* Frida hooks to load and execute live in their runtime specific directories in [objection/hooks](https://github.com/sensepost/objection/tree/master/objection/hooks).
* Classes and methods responsible for the command line interface, as well as the REPL live in [objection/console](https://github.com/sensepost/objection/tree/master/objection/console).

## REPL command flow
When a command is entered in the `objection explore` REPL, the [`run_command()`](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L131) method is run to process the string input received from `prompt_toolkit`. The `run_command()` method 'explodes' the received command, honoring quotes just like a shell would, and tries to find a python method to execute with [`_find_command_exec_method `](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L200).

Commands that can be run is defined in a repository located in the `COMMANDS` variable in [repository.py](https://github.com/sensepost/objection/blob/master/objection/console/repository.py#L28), specifying an `exec` key with a value being the python method to execute. Any remaining tokens will be passed on to the python method that will get called as [arguments](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L198).

Communications with a remote FridaGadget is handled in [utils/frida-transport.py](https://github.com/sensepost/objection/blob/master/objection/utils/frida_transport.py).

Depending on the type of Frida hook that will be run (based on an entered command), either a synchronous invocation will occur, which will [load](https://github.com/sensepost/objection/blob/master/objection/utils/frida_transport.py#L258), execute and [unload](https://github.com/sensepost/objection/blob/master/objection/utils/frida_transport.py#L259) the hook, or an asynchronous invocation will occur which [loads](https://github.com/sensepost/objection/blob/master/objection/utils/frida_transport.py#L288), executes and [backgrounds](https://github.com/sensepost/objection/blob/master/objection/utils/frida_transport.py#L291) the hook. Only when the `jobs kill <job_id>` command is run will an asynchronous invocation be [stopped](https://github.com/sensepost/objection/blob/master/objection/utils/frida_transport.py#L163).