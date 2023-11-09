# Prerequisites
- Create an Appple Development certificate
    - Create an Apple developer account
    - In XCode, go to Preferences -> Accounts, sign in with your Apple developer account
    - On Accounts screen, click `Manage Certificates...`
    - Add `Apple Development` certificate
- Create a HelloWorld app, embed Frida in it, and verify that you can attach to it
    - IMPORTANT: Copy the gadget into your project directory as `FridaGadget.dylib`
    - Make sure it is listed under Build Phases -> Link Binary With Libraries
    - Copy frida gadget into your cache directory: `cp frida-gadget-16.0.9-ios-universal.dylib ~/.cache/frida/gadget-ios.dylib`
- Use the embedded.mobileprovision file from that project when you re-sign the target app
- Build `insert_dylib` from https://github.com/Tyilo/insert_dylib with XCode
- Install frida-tools: `pip install frida-tools`
- Run `frida --version` to find your version. This example uses 16.0.9
- Download frida-gadget for the version of frida tools you installed from https://github.com/frida/frida/releases/tag/16.0.9
- Install `ios-deploy`: `brew install ios-deploy`
- Install `applesign`: `sudo npm install -g applesign`

# Embed Frida in .ipa file
## 1. List Singing Certs
```
security find-identity -p codesigning -v
```

If you have followed the pre-requisite steps above, you should see some certificates listed:
```
  1) A587E470A2917BF12CE300BB0260EED38C5335B2 "Apple Development: Bryan Geraghty (VU5M63LPY7)"
       1 valid identities found
```

## 2. Set environment variables
```
IPAFILE="Oelo 1.32.ipa";
APPNAME="Oelo Lighting";
CERTID="VU5M63LPY7";
```

## 3. Unzip .ipa file
```
unzip -q "$IPAFILE" -d "$IPAFILE-unzipped"
```

## 4. Embed Frida gadget
```
mkdir -p "$IPAFILE-unzipped/Payload/$APPNAME.app/Frameworks"
cp frida-gadget-16.0.9-ios-universal.dylib "$IPAFILE-unzipped/Payload/$APPNAME.app/Frameworks/FridaGadget.dylib"
insert_dylib --inplace --overwrite --strip-codesig '@executable_path/Frameworks/FridaGadget.dylib' "$IPAFILE-unzipped/Payload/$APPNAME.app/$APPNAME"
cp ~/Library/Developer/Xcode/DerivedData/iosTestApp-grekcgftglffohawuqwweikjohbu/Build/Products/Debug-iphoneos/iosTestApp.app/embedded.mobileprovision "$IPAFILE-unzipped/Payload/$APPNAME.app/"
```

## 5. Sign the app
```
codesign -f -s "$CERTID" "$IPAFILE-unzipped/Payload/$APPNAME.app"
```

## Install patched app
ios-deploy --bundle "$IPAFILE-unzipped/Payload/$APPNAME.app" -W -d -v

## Now attach from another terminal
frida-trace -U -f re.frida.Gadget -i "open*"

# Other useful commands
## List apps running on device
```
frida-ps -Ua
```

```
 PID  Name        Identifier
4  ----------  ---------------------
2401  Gadget      re.frida.Gadget
2293  Settings    com.apple.Preferences
2401  iosTestApp  atredis.iosTestApp
```

## Test Frida connection
```
frida -U -n iosTestApp
```
```
     ____
    / _  |   Frida 16.0.9 - A world-class dynamic instrumentation toolkit
   | (_| |
    > _  |   Commands:
   /_/ |_|       help      -> Displays the help system
   . . . .       object?   -> Display information about 'object'
   . . . .       exit/quit -> Exit
   . . . .
   . . . .   More info at https://frida.re/docs/home/
   . . . .
   . . . .   Connected to iPad (id=d6cd22d14c521f74cec2d22379323c18113335c8)

[iPad::iosTestApp ]->
```

## Create a .ipa file
zip -qr $IPAFILE "$IPAFILE-unzipped/Payload"

## List Linking
Search for LC_LOAD_DYLIB

```
otool -l "$IPAFILE-unzipped/Payload/$APPNAME.app" | vim -R -
```

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
