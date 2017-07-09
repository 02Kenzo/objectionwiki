Running patched applications on iOS devices require us to side load and run them using external tools. For macOS users, `ios-deploy` is perfect for this. For Linux users, the `libimobiledevice` project provides commands to perform the same tasks.

## installing and running on macOS
For macOS users, `ios-deploy` tool (https://github.com/phonegap/ios-deploy) can handle the installation _and_ running of patched applications for you.

Installing `ios-deploy` is as simple as `npm install -g ios-deploy`. Once installed, you should have the `ios-deploy` command available in your `PATH`.