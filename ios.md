# REQUIREMENTS
Create a HelloWorld app, embed Frida in it, and verify that you can attach to it
Use the embedded.mobileprovision file from that project when you re-sign the target app
You must use a dev certificate (not a deployment cert) to sign with or Frida will not launch, even if it successfully deploys

# Commands
## Install patched app
ios-deploy --bundle /Payload/$APPNAME.app -W -d -v

## Now attach from another terminal
frida-trace -U -f re.frida.Gadget -i "open*"

## List Singing Certs
security find-identity -p codesigning -v

## Unzip an IPA
unzip -q "$IPA"

## Zip an IPA
zip -qr $IPA Payload

## Sign an App
codesign -f -s "$CERT_ID" Payload/*.app

## List Linking
otool -l Payload/$APPNAME.app/$APPNAME | vim -R -

Search for LC_LOAD_DYLIB

## Print mobileprovision
security cms -D -i *.mobileprovision

# Resources
* https://github.com/ios-control/ios-deploy/issues/193
* https://support.apple.com/en-us/HT202778
* https://github.com/ios-control/ios-deploy
* https://github.com/sensepost/objection/wiki/Running-Patched-iOS-Applications
* https://blog.securityinnovation.com/frida
* https://github.com/sensepost/objection/wiki/Patching-iOS-Applications - Meh, requires node
* https://stackoverflow.com/questions/6896029/re-sign-ipa-iphone - Under solution
* Getting started with Apple Certificates https://appaloosa-store.readme.io/docs/getting-started-with-apple-certificates
 *https://www.frida.re/docs/ios/#without-jailbreak
* How to re-sign the ipa file? https://stackoverflow.com/questions/5160863/how-to-re-sign-the-ipa-file
* insert_dylib https://github.com/Tyilo/insert_dylib
* Create an Xcode project (Swift) https://help.apple.com/xcode/mac/current/#/dev07db0e578
* ota_tools https://github.com/RichardBronosky/ota-tools
* https://hyperpad.zendesk.com/hc/en-us/articles/204378199-Create-a-Distribution-Provisioning-Profile
