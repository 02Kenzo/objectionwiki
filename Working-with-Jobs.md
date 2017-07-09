There are two types of command categories within `objection`. Simply put, a command is either run as a one time command, or, as a long running 'job'.

A one time command gets loaded, executed and unloaded as soon as it is finished. This is often followed by some output presented in the terminal. Executing the one time command again will result in this process repeating. Examples of one time commands include `ls`, `env` and the file `upload` and `download` commands

A 'job' however gets loaded, executed and is then left running in the background. If the job has output that needs to be displayed, it will be shown in the REPL prefixed with a short job-id and name such as `[2dff56c320d5] [jailbreak-simulate]` followed by the message itself. You can continue using the REPL as these jobs are running in the background. Multiple jobs could be running at once too. Example commands that are run as jobs include `ios jailbreak simulate`, `ios pasteboard monitor` and `ios hooking dump method_args`.

## starting jobs
Nothing special is needed to start a new job. Commands that are supposed to run as jobs will automatically start as needed.

A special note should be made for the `import` command. Frida scripts loaded with the `import` command are also run as jobs automatically:

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # import test.js
Job: 55321569-cd6b-4eb6-82a6-ef3cbc0cce88 - Starting
Job: 55321569-cd6b-4eb6-82a6-ef3cbc0cce88 - Started

sensepost’s iPad on (iPad: 10.2.1) [usb] # jobs list
UUID                                  Name         Started
------------------------------------  -----------  -------------------
55321569-cd6b-4eb6-82a6-ef3cbc0cce88  user-script  2017-07-09 12:05:32
``` 

A generic name of `user-script` is used for jobs started with the `import` command, unless a name is specified:

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # import test.js "my test command"
Job: 0286fc37-66f1-4b05-882a-9125d96097be - Starting
Job: 0286fc37-66f1-4b05-882a-9125d96097be - Started

sensepost’s iPad on (iPad: 10.2.1) [usb] # jobs list
UUID                                  Name             Started
------------------------------------  ---------------  -------------------
55321569-cd6b-4eb6-82a6-ef3cbc0cce88  user-script      2017-07-09 12:05:32
0286fc37-66f1-4b05-882a-9125d96097be  my test command  2017-07-09 12:05:45
```

## job management
With the ability to have multiple jobs running at once, `objection` provides a `jobs` command that lets you list and kill jobs as needed.

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # jobs list
UUID                                  Name                             Started
------------------------------------  -------------------------------  -------------------
cf1c8381-5c55-4658-b9fd-2dff56c320d5  simulate-jailbroken-environment  2017-07-09 11:38:51
27c38e53-4c3e-468b-8948-5c5a31dbc7d2  pasteboard-monitor               2017-07-09 11:58:06
```

In the example above, we have two jobs running. If we wanted to stop the _pasteboard-monitor_ job, we can simply run `jobs kill 27c38e53-4c3e-468b-8948-5c5a31dbc7d2` (job ids are tab completable):

```
sensepost’s iPad on (iPad: 10.2.1) [usb] # jobs kill 27c38e53-4c3e-468b-8948-5c5a31dbc7d2
Job: 27c38e53-4c3e-468b-8948-5c5a31dbc7d2 - Stopping
Job: 27c38e53-4c3e-468b-8948-5c5a31dbc7d2 - Stopped
```

Killing a job results in it finally getting unloaded, just like the last step in a one time command. When you `exit` the REPL, as part of the exit routines, all of the remaining jobs that are still running will also be unloaded.