The installation of the `objection` [python component](components) should be relatively simple. In fact, a simple  `pip3 install objection` will suffice.

# toc
- [prerequisites](#prerequisites)
- ['native' installation](#native-installation)
- ['virtual' installation](#virtual-installation)
- [errors](#errors)

## prerequisites
Before continuing with the installation though, make sure you have the following prerequisites met:

- `python` version 3.4+. Check your version with: `python -V`, upgrade/install using your distributions package manager.
- `pip3` version 9.0+. Check your version with: `pip --version`, upgrade with: `pip install pip --upgrade`.
- (_optionally_) `virtualenv` version 15+. Check your version with: `virtualenv --version`, upgrade/install with: `pip install virtualenv --upgrade`.

## 'native' installation
By 'native', we are referring to a non-`virtualenv` installation, where all of the `objection` dependencies will be installed in your operating systems python module path. There is nothing wrong with this really, but some may prefer to have isolated environments for certain programs. If this is you, check out the next section.

1. Make sure your system meets the [prerequisites](#prerequisites) detailed in the previous section.

2. Next, start the installation using `pip3` with:
```
pip3 install -U objection
```

3. Once the dependencies are installed, the `objection` command should be available in your `PATH`. In some shells, it may be necessary to run `hash -r` before the command will be available.

## 'virtual' installation
By 'virtual', we are referring to the fact that `objection` will be installed in an isolated python virtual environment. For many, this is the preferred option as `objection` dependencies will have no effect on any locally installed dependencies within the operating systems module path.

1. Make sure your system meets the [prerequisites](#prerequisites) detailed in the previous section, including the `virtualenv` command.

2. Create a new virtual environment with:
```
virtualenv --python=python3 ~/virtual-python3
```

3. Activate your new python virtual environment with:
```
source ~/virtual-python3/bin/activate
```

4. Next, start the installation using `pip3` with:
```
pip3 install -U objection
```

5. Once the dependencies are installed, the `objection` command should be available in your `PATH`. In some shells, it may be necessary to run `hash -r` before the command will be available.

## errors
The most common error you are likely to face is one that might look as follows:

```
~ # pip3 install objection
Collecting objection
  Could not find a version that satisfies the requirement objection (from versions: )
No matching distribution found for objection
```

This means that you are trying to install `objection` using Python 2 and not Python 3. Either change your interpreter to Python 3, try and install using `pip3` and not `pip` or activate a new [virtual environment](#virtual-installation).