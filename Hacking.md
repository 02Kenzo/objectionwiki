So you want to dig a little deeper into the project and its source code. This article aims to give you a birds eye view on the project structure, a quick flow example to run a hook and other goodies.

## birds eye view
At its code, `objection` relies **heavily** on Frida to perform most of the magic. Frida, together with some purpose built hooks and the python REPL is what makes up `objection`.

## project structure
Lets take a quick look at the project structure.

Command line argument parsing is handled with [click](http://click.pocoo.org/5/), the REPL is handled by [python-prompt-toolkit](https://github.com/jonathanslenders/python-prompt-toolkit) and hook compilation (basically adding the global error handler per runtime) is handled with [jinja](http://jinja.pocoo.org/docs/2.9/).

## repl command flow
When a command is entered in the `objection explore` REPL, the [`run_command()`](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L131) method is run to process the string input received from `prompt_toolkit`. The `run_command()` method 'explodes' the received command, honoring quotes just like a shell would, and tried to find a python method to execute with [`_find_command_exec_method `](https://github.com/sensepost/objection/blob/master/objection/console/repl.py#L200).

Commands that can be run is defined in a repository located in the `COMMANDS` variable in [repository.py](https://github.com/sensepost/objection/blob/master/objection/console/repository.py#L28), specifying an `exec` key with a value being the python method to execute.