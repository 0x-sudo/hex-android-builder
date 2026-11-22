# Hex Android builder

Rolling APK build of [anomalyco/hex](https://github.com/anomalyco/hex) `dev`, from the `android/` folder.

Download:

https://github.com/0x-sudo/hex-android-builder/releases/download/android-latest/hex-android.apk

The APK is arm64 only and needs Android 12+. It is signed with a stable local key, so new builds install over old ones.

Builds run every 6 hours, and only when the upstream `dev` branch changed. You can start one by hand from the Actions tab.

Secrets:

- `HEX_KEYSTORE_B64` — base64 of `upload.jks`
- `HEX_KEYSTORE_PASSWORD` — its password

Keep a backup of the keystore and password. Lose them and every update needs an uninstall first.
