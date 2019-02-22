Since `objection` version 1.5.0, an optional API has been made available. The API operates in two main modes, whereby the first is to invoke methods directly available in the [Frida agent](https://github.com/sensepost/objection/tree/master/agent), and the second mode allows for the execution of arbitrary Frida scripts.

To skip the details, jump down to the summary [here](#summary)

# toc
- [starting](#starting)
- [rpc methods](#rpc-methods-endpoint)
  - [rpc android example](#rpc---android-example)
  - [rpc ios example](#rpc---ios-example)
- [raw scripts endpoint](#raw-scripts-endpoint)
- [summary](#summary)

## starting
The `objection` API interface may be started in two ways, of which both ways are done via the command line interface. In both cases, it is possible to specify the host and port of the API with the `--api-host` and `--api-port` flags right after the `objection` command.

i. *Headless Mode:* The first mode is a 'headless mode', meaning that the objection REPL will not be available. To start the API, simply run the `objection api`. If you need to specify a port for the API to run on, do this with `objection --api-port 8000 api` (as an example).

ii. *REPL and API:* The second mode is by using the normal `objection explore` command, but this time, adding the `--enable-api` flag to the `explore` subcommand. For example: `objection explore --enable-api`. If you need to specify a port for the API to listen on, an example invocation may be: `objection --api-port 8000 explore --enable-api`.

## rpc methods endpoint
The `objection` API exposes and endpoint at `/rpc/invoke/<method name>` where `<method name>` is the name of the RPC method from the specification file. All of the RPC methods exposed by the Frida agent are callable by the `objection` API. RPC endpoints may be sourced by looking at the appropriate agent [RPC specification files](https://github.com/sensepost/objection/tree/master/agent/src/rpc).

The RPC invoke endpoint accepts both `GET` and `POST` HTTP verbs. Use the `GET` verb if the endpoint does not require any arguments and use the `POST` verb if the endpoint requires arguments. `POST` requests need to have the `application/json` content-type header, and submit arguments as a json payload.

For example:

- `iosCookiesGet: ()` does not require any arguments, therefore we can call it with: `curl -s http://127.0.0.1:8888/rpc/invoke/iosCookiesGet`
- `iosHookingSearchMethods: (partial: string)` requires the `partial` argument, and therefore we call it with: `curl -s -X POST "http://127.0.0.1:8888/rpc/invoke/androidHookingGetClassMethods" -H "Content-Type: application/json" -d '{"className": "com.twitter.sdk.android.tweetui.GalleryActivity"}'`

### rpc - android example
Android RPC endpoints are specified [here](https://github.com/sensepost/objection/blob/master/agent/src/rpc/android.ts). Taking the `androidHookingListActivities ` example, we can list activities in the current Android application. After starting the `objection` API, we can invoke the list with:

```bash
$ curl -s "http://127.0.0.1:8888/rpc/invoke/androidHookingListActivities"
["com.reddit.frontpage.StartActivity","com.reddit.frontpage.IntroductionActivity", ... snip ...]
```

We can then get the methods in one of these activity classes with:

```bash
$ curl -s -X POST "http://127.0.0.1:8888/rpc/invoke/androidHookingGetClassMethods" -H "Content-Type: application/json" -d '{"className": "com.reddit.frontpage.StartActivity"}'
["static android.os.Handler com.reddit.frontpage.StartActivity.a(com.reddit.frontpage.StartActivity)","private void com.reddit.frontpage.StartActivity.a()", ... snip ...]
```

### rpc - ios example
iOS RPC endpoints are specified [here](https://github.com/sensepost/objection/blob/master/agent/src/rpc/ios.ts). Taking the `iosHookingSearchMethods ` example, we can list search for methods in the current iOS application. After starting the `objection` API, we can invoke the search with:

```bash
$ curl -s -X POST "http://127.0.0.1:8888/rpc/invoke/iosHookingSearchMethods" -H "Content-Type: application/json" -d '{"partial": "jail"}'
["[JailbreakDetection + isJailbroken]","[UIScreen + _shouldDisableJail]", ... snip ...]
```

## raw scripts endpoint
It is possible to invoke raw scripts via the API. An endpoint at `/script/runonce` exists. This endpoint only accepts the `POST` HTTP verb. The content-type header for this endpoint should be `text/javascript`, and the POST body of your request should contain the complete Frida script to run.

For example, to run a simple Frida script that reports the version number, we send `send(Frida.version);`:

```bash
$ curl -X POST -H "Content-Type: text/javascript" http://127.0.0.1:8888/script/runonce -d "send(Frida.version);"
[{"payload":"12.2.5","type":"send"}]
```

Longer scripts may be uploaded using the `@` directory for the `--data/-d` flag. For example:

```bash
$ cat script.js
if (ObjC.available) {

    var foo = ObjC.classes.NSString;
    var s = foo.stringWithString_("testing Frida script, via objection API");

    send(s.toString());
}

$ curl -X POST -H "Content-Type: text/javascript" http://127.0.0.1:8888/script/runonce -d "@script.js"
[{"payload":"testing Frida script, via objection API","type":"send"}]
```

## summary

Endpoints:
- `POST` to `/script/runonce`
- `GET` & `POST` to `/rpc/invoke/<method name>`

Content-Type header:
- `POST` to `/rpc/invoke/<method name>` = `application/json`
- `POST` to `/script/runonce` = `text/javascript`

Endpoints:
- iOS [here](https://github.com/sensepost/objection/blob/master/agent/src/rpc/ios.ts)
- Android [here](https://github.com/sensepost/objection/blob/master/agent/src/rpc/android.ts)
- All [here](https://github.com/sensepost/objection/blob/master/agent/src/rpc/)