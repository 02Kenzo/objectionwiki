The installation of `objection` itself should be relatively simple. In fact, a simple `git clone` and `pip install` will suffice. Installing `objection` using only `pip` will be possible soon™.

## prerequisites
Before continuing with the installation, make sure you have the following prerequisites met:

- `python` version 3.3+. Check your version with: `python -V`, upgrade/install using your distributions package manager.
- `pip` version 9.0. Check your version with: `pip --version`, upgrade with: `pip install pip --upgrade`.
- (_optionally_) `virtualenv` version 15+. Check your version with: `virtualenv --version`, upgrade/install with: `pip install virtualenv --upgrade`.

## native installation
By 'native', we are referring to a non-`virtualenv` installation, where all of the `objection` dependencies will be installed in your operating systems module path. There is nothing wrong with this really, but some may prefer to have isolated environments for certain programs. If this is you, check out the next section.

1. Make sure your system meets the [prerequisites](#prerequisites) detailed in the previous section.

2. Next, clone this repository:
```bash
git clone https://github.com/sensepost/objection.git
```

3. Once cloned, `cd` to the new `objection` directory, and install the dependencies using `pip`. We will also pass on the `--editable` flag (until `objection` is uploaded to pypi) so that future updates are as simple as as `git pull`:
```
cd objection/
```

```
pip install --editable .
```