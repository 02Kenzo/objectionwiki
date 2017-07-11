One of features `objection` has is the ability to 'bypass' TouchID. Not all TouchID implementations will be by-passable, and this article aims to explain why.

## some background
Apple introduced the ability to perform authentication via TouchID, and explains how this works in code in the [following article](https://developer.apple.com/documentation/localauthentication?language=objc). Whats important to understand here is that this is basically a way for an iOS application to ask iOS: "Check if a valid fingerprint / passphrase is entered please?".

## LAContext flow
As you now know, the `LAContext` class specifically does _local_ authentication. It does not verify anything externally or anything like that. It simply relies on iOS to present the relevant dialogs and confirm authentication.

Methods with which you confirm local authentication include the devices passcode, passphrase or via TouchID. An iOS application can not confirm the devices passphrase / TouchID directly via code, but Apple made it possible via the `LAContext` helper class. This means that an iOS application can not control the authentication dialogs themselves, but can make them pop up.

From a code perspective, once you have set up a new `LAContext` instance, the `evaluatePolicy` method is called, giving iOS a chance to present the relevant dialogs and perform the authentication. Depending on the success or failure of the authentication itself, a `reply` block is invoked that includes a boolean indicating if it was successful or not.

The code block itself that gets executed is again developer controlled, and usually, based on the value of the `success` boolean, the application would continue accordingly or present an authentication failed error.

## the objection bypass
Within `objection`, when you run the `ios ui touchid_bypass` command, a hook is executed that listens for invocations of the `-[LAContext evaluatePolicy:localizedReason:reply:]` selector. If the `evaluatePolicy` method is called, the hook will replace the `success` boolean to a `True` in the code block that is executed when a `reply` is received.

## what it does not and can not do
Once the `reply` has had its success boolean set to `true`, and original, intended code block is executed, just like it would have under normal conditions. If this code block contains further validations, then those need to be inspected separately and hooked/manipulated appropriately.

What should be clear now is that this 'bypass' is purely a local bypass for when a target application calls `evaluatePolicy` and a failed response is received. This 'bypass' will not work in cases where keychain items are protected with access control flags such as `kSecAccessControlTouchIDAny` or `kSecAccessControlTouchIDCurrentSet`. 