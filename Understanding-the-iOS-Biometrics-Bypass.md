One of features `objection` has is the ability to 'bypass' iOS biometrics. This includes both the older TouchID as well as newer FaceID features, collectively referred to as biometrics. Not all biometrics implementations will be by-passable, and this article aims to explain why.

## some background

Apple devices have the ability to perform authentication via biometrics, and explains how this works in code in the [following article](https://developer.apple.com/documentation/localauthentication?language=objc). Whats important to understand here is that this is basically a way for an iOS application to ask iOS: "Check if a valid biometric / passphrase is entered please?".

## LAContext flow

As you now know, the `LAContext` class specifically does _local_ authentication. It does not verify anything externally or anything like that. It simply relies on iOS to present the relevant dialog and confirm authentication.

Methods with which you confirm local authentication include the devices passcode, passphrase or via biometrics. An iOS application can not confirm the devices passphrase / biometrics directly via code, but Apple made it possible via the `LAContext` helper class. This means that an iOS application can not control the authentication dialog itself, but can make it pop up.

From a code perspective, once you have set up a new `LAContext` instance, the `evaluatePolicy` method is called, giving iOS a chance to present the relevant dialog and perform the authentication. Depending on the success or failure of the authentication itself, a `reply` block is invoked that includes a boolean indicating if it was successful or not.

The code block itself that gets executed is again developer controlled, and usually, based on the value of the `success` boolean, the application would continue accordingly or present an authentication failed error.

## the objection bypass

Within `objection`, when you run the `ios ui biometrics_bypass` command, a hook is executed that listens for invocations of the `-[LAContext evaluatePolicy:localizedReason:reply:]` selector. If the `evaluatePolicy` method is called, the hook will replace the `success` boolean to a `True` in the code block that is executed when a `reply` is received.

## what it does not and can not do

Once the `reply` has had its success boolean set to `true`, and original, intended code block is executed, just like it would have under normal conditions. If this code block contains further validations, then those need to be inspected separately and hooked/manipulated appropriately.

What should be clear now is that this 'bypass' is purely a local bypass for when a target application calls `evaluatePolicy` and a failed response is received. This 'bypass' will not work in cases where keychain items are protected with access control flags such as `kSecAccessControlTouchIDAny` or `kSecAccessControlTouchIDCurrentSet`. 

## still confused?

Issue [#136](https://github.com/sensepost/objection/issues/136#issuecomment-419664574) contains a more verbose explanation which may help clear more things up. Additionally, [this](https://developer.apple.com/documentation/localauthentication/accessing_keychain_items_with_face_id_or_touch_id) Apple documentation link also describes the internals pretty well.