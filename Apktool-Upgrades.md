When [patching](Patching-Android-Applications) Android applications, objection uses [`apktool`](https://ibotpeaches.github.io/Apktool/) to unpack and pack applications. Using the latest `apktool` is always recommended for various reasons.

Unfortunately many distributions don't package the latest `apktool` as part of the Operating Systems package manger meaning you have to install it manually.

# which apktool to use

When patching an application using objection, objection knows where `apktool` is based on a `PATH` lookup. So, if you have `apktool` anywhere in your `PATH`, this is the one objection will use patch. If `apktool` is not in your `PATH`, objection will assume it is not correctly installed. To see which one exactly objection will use, you can run `which apktool`:

```bash
$ which apktool
/usr/local/bin/apktool
```

You can also see where else you may have `apktool` with `whereis apktool`:

```bash
$ whereis apktool
apktool: /usr/bin/apktool /usr/local/bin/apktool /usr/share/apktool /home/user/.local/bin/apktool /usr/share/man/man1/apktool.1.gz
```

# apktool version

At the time of writing this article, objection required `apktool` version 2.4.1. This could change as time goes on but it's best to always have the latest version anyways. To check which version you have (and which version objection will think you have):

```bash
$ apktool -version
2.4.1
```

# apktool update

The upgrade will depend heavily on your Operating System. You can try the package manager your OS uses to upgrade, and if it has the latest, hooray for you! If not, you can always download the latest version from the News section on the `apktool` website [here](https://ibotpeaches.github.io/Apktool/) and save the `.jar` file somewhere in your `PATH`. It is possible to download the `.jar` file directly into some place like `/usr/local/bin/apktool` as follows (as root):

```bash
$ curl -fsSL https://bitbucket.org/iBotPeaches/apktool/downloads/apktool_2.4.1.jar -o /usr/local/bin/apktool
```