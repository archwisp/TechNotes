# Run Writable System Emulator 
```
~/Library/Android/sdk/emulator/emulator -list-avds
Pixel_6_API_29

~/Library/Android/sdk/emulator/emulator -writable-system -avd Pixel_6_API_29 -netdelay none -netspeed full
```
# Flash device: https://flash.android.com

# Install Burp CA cert
- Format needs to be PEM+text 

```
adb root
adb shell avbctl disable-verification
adb shell disable-verity
adb reboot
adb root && adb remount
HASH=$(openssl x509 -inform DER -subject_hash_old -text -in ~/Downloads/cacert.der -out nul | head -n 1);
openssl x509 -inform DER -text -in ~/Downloads/cacert.der -out ~/Downloads/$HASH.0;
openssl x509 -inform DER -text -in ~/Downloads/cacert.der -out nul >> ~/Downloads/$HASH.0;
adb push ~/Downloads/$HASH.0 /system/etc/security/cacerts/;
adb remount
adb reboot
adb shell cat /system/etc/security/cacerts/9a5ba575.0
adb shell settings put global https_proxy 10.0.2.2:8080
adb shell settings put global https_proxy 192.168.0.109:8080
```

## References
### Remount
- https://stackoverflow.com/questions/55030788/adb-remount-fails-mount-system-not-in-proc-mounts
```
> adb root
> adb shell avbctl disable-verification
Successfully disabled verification. Reboot the device for changes to take effect.
> adb disable-verity
using overlayfs
Successfully disabled verity
Now reboot your device for settings to take effect
> adb reboot
> adb root && adb remount
remount succeeded
> adb push ScreenCap.apk /system/app/
ScreenCap.apk: 1 file pushed. 33.1 MB/s (1640812 bytes in 0.047s)
> adb reboot
```

### Install cert
- https://stackoverflow.com/questions/44942851/install-user-certificate-via-adb

```
PEM_FILE_NAME=logger-charles-cert.pem
hash=$(openssl x509 -inform PEM -subject_hash_old -in $PEM_FILE_NAME | head -1)
OUT_FILE_NAME="$hash.0"

cp $PEM_FILE_NAME $OUT_FILE_NAME
openssl x509 -inform PEM -text -in $PEM_FILE_NAME -out /dev/null >> $OUT_FILE_NAME

echo "Saved to $OUT_FILE_NAME"
adb shell mount -o rw,remount,rw /system
adb push $OUT_FILE_NAME /system/etc/security/cacerts/
adb shell mount -o ro,remount,ro /system
adb reboot
```

