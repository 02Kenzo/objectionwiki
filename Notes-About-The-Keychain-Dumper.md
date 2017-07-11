It is possible to dump the entries in the iOS keychain with `objection` and the `ios keychain dump` command. This is achieved by building a query dictionary just like you would in an iOS application, and querying for all of the available object class types such as `kSecClassKey`, `kSecClassIdentity` and `kSecClassInternetPassword`.

There are a few important things to keep in mind though:

* This command will only dump keychain entries for the current application. In fact, a more 'correct' way of saying this is that it will only dump entries for the current entitlement group.
* The entitlement group your application will use comes from the `embedded.mobileprovision` file used when patching and resigning the IPA.
* When patching multiple IPA's, it is common to re-use an `embedded.mobileprovision` file. This is fine, but it is important to remember that other applications keychain entries may exist when you dump the keychain.