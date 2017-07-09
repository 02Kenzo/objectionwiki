Running patched applications on iOS devices require us to side load and run them using external tools. For macOS users, `ios-deploy` is perfect for this. For Linux users, the `libimobiledevice` project provides commands to perform the same tasks.

## installing and running on macOS
For macOS users, the [ios-deploy](https://github.com/phonegap/ios-deploy) utility can handle the installation _and_ running of patched applications for you.

#### ios-deploy installation
Installing `ios-deploy` is as simple as `npm install -g ios-deploy`. Once installed, you should have the `ios-deploy` command available in your `PATH`.

#### running your application
To install and run your application:

1. Extract the IPA package with `unzip my-app.ipa`. This should leave you with a `Payload/` directory.
2. Attach your iOS device to your computer via USB and ensure that it is unlocked.
3. Run `ios-deploy` with `ios-deploy --bundle Payload/my-app.app -W -d` where `my-app.app` is the folder where your application lives.

You should see the `lldb` debugger pop up and print out a 'success' message. The application you are running will appear to be in a 'frozen' state for a while. This is because the Frida gadget takes some time to finish loading. After some time, you should be able to run `objection explore` and land in a successfully connected prompt :)