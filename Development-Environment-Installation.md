A development installation of objection is slightly more involved when compared to the standard [distribution](Installation) installation.

At a glance, you would need to install python and some dependencies for the command line as well as a node environment and dependencies for the agent. Both should be relatively simple.

## environment
This section aims to describe both the python and node environment setups needed. A short version of the process is:

- Clone objection with `git clone https://github.com/sensepost/objection.git`.
- Install objection with `pip3 install --editable .` from the cloned directory.
- Install agent dependencies with `npm install` in the agent directory.
- Compile the agent with `npm run build`.

This should leave you with an `objection` command that can be used anywhere you have the same python environment active. Code changes made in the cloned directory should be active for that command.

### source code
To start, clone the objection repository. This can be done with:

```bash
git clone https://github.com/sensepost/objection.git
```

If you plan on contributing a change back to objection (yay), you would fork the repository and just clone your fork instead.

### python
For a development environment, it is recommended that you use a python virtual environment to avoid dependencies clobbering up your main python installation. To get a new python virtual environment running, make sure you have [virtualenv](https://virtualenv.pypa.io/en/latest/installation/) installed. Next, create a new environment and make it active with:

```bash
virtualenv -p python3 ~/py3-venv
source ~/py-venv/bin/activate
```

Once active, ensure that you are in the cloned `objection/` directory and install it with:

```bash
pip3 install --editable .
```

This should resolve all of the necessary dependencies for you and provide you with the `objection` command anywhere where you have the `~/py3-venv` active.

### agent
The agent specific development environment is setup is documented [here](Agent-Development-Environment).
