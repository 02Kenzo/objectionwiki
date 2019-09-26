Supporting both iOS and Android and having new features and improvements added regularly as the tool is used in real world scenarios, the following is a short list of only a few key features:

For all supported platforms, `objection` allows you to:

- Patch iOS and Android applications, embedding a Frida gadget that can be used with `objection` or just Frida itself.
- Interact with the filesystem, listing entries as well as upload & download files where permitted.
- Perform various memory related tasks, such as listing loaded modules and their respective exports.
- Attempt to bypass and simulate jailbroken or rooted environments.
- Discover loaded classes and list their respective methods.
- Perform common SSL pinning bypasses.
- Dynamically dump arguments from methods called as you use the target application.
- Interact with SQLite databases inline without the need to download the targeted database and use an external tool.
- Execute custom Frida scripts.

iOS specific features in `objection` include the ability to:

- Dump the iOS keychain, and export it to a file.
- Dump data from common storage such as NSUserDefaults and the shared NSHTTPCookieStorage.
- Dump various formats of information in human readable forms.
- Bypass certain forms of TouchID restrictions.
- Watch for method executions by targeting all methods in a class, or just a single method.
- Monitor the iOS pasteboard.
- Dump encoded `.plist` files in a human readable format without relying on external parsers.

Android specific features in `objection` include the ability to:

- List the applications Activities, Services and Broadcast receivers.
- Start arbitrary Activities available in the target application.
- Watch a class method, reporting execution as it happens.