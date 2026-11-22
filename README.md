# Hex for Android — unofficial build

[![build](https://github.com/0x-sudo/hex-android-builder/actions/workflows/hex-apk.yml/badge.svg)](https://github.com/0x-sudo/hex-android-builder/actions/workflows/hex-apk.yml)

> **This is not official.**
> I am not affiliated with Anomaly or the Hex team. I just saw Hex, thought it looked great, and got curious and excited about running it on Android. So I built a pipeline that compiles their `android/` folder for me. If something breaks, blame me, not them. Go star the real thing: [anomalyco/hex](https://github.com/anomalyco/hex).

## Download

https://github.com/0x-sudo/hex-android-builder/releases/latest/download/hex-android.apk

This URL always points at the newest build. Each build is its own release, and older ones are deleted.

## Obtainium

1. Install [Obtainium](https://github.com/ImranR98/Obtainium).
2. Add app → paste `https://github.com/0x-sudo/hex-android-builder`.
3. Pick `hex-android.apk` if it asks.
4. Updates arrive when the daily build publishes a new release.

The tag is `android-<versionCode>`, so every build looks like a new version to Obtainium. The signing key never changes, so updates install over the old app.

## Install

- Phone: ARM64, Android 12 or newer.
- Open the APK on the phone, or:

```sh
adb install -r hex-android.apk
```

Updates install over the previous build. Every build uses the same signing key, so you do not need to uninstall first.

## What starts a build

- **Daily** at 03:17 UTC.
- **Manually** from Actions → `hex-apk` → Run workflow. Tick `force` to rebuild even when nothing changed.
- **Only when `android/` changed.** A docs or iOS commit does not trigger a build.

The workflow remembers the last built commit in the release body, so it does not rebuild the same code twice.

## What the pipeline does

1. Reads the latest `dev` commit and the latest commit that touched `android/`.
2. Compares it with the last successful build.
3. Sets up Java 21 and the exact Android SDK, build tools, NDK, and CMake that upstream pins.
4. Clones `anomalyco/hex`, `dev` branch, shallow.
5. Stamps a unique version.
6. Restores the Gradle cache.
7. Runs upstream unit tests, then `assembleRelease` with R8.
8. Verifies the APK: package name, ABI, minSdk, zip alignment, signature.
9. Writes a SHA-256 checksum.
10. Attests build provenance with GitHub.
11. Uploads the APK and checksum as a release asset, and as a 14-day workflow artifact.
12. Writes release notes with the upstream commit, size, checksum, and recent changes.

## Versioning

- `versionCode` = `YYMMDD * 1000 + run number % 1000`
- `versionName` = upstream version + short commit, for example `0.2.2-c7e96f1`

So every build installs as an update, and the app info screen tells you which commit you run.

## Verify a download

```sh
sha256sum -c hex-android.apk.sha256
gh attestation verify hex-android.apk --repo 0x-sudo/hex-android-builder
```

`gh` must be 2.49 or newer. The attestation proves the APK came from this workflow and this repo.

## Secrets

The repo needs two Actions secrets:

| Secret | Value |
|---|---|
| `HEX_KEYSTORE_B64` | base64 of the signing keystore |
| `HEX_KEYSTORE_PASSWORD` | the keystore password |

Back up the keystore and password. If you lose them, every future update needs a manual uninstall.

## Limits

- **Unofficial.** Not reviewed by Anomaly, not on Play.
- **ARM64 only.** No 32-bit or x86 build.
- **Android 12+.** Upstream's `minSdk` is 31.
- **Daily at most.** No push trigger; GitHub cannot watch another repo.
- **Upstream can break.** `dev` moves fast. A red run means upstream is broken, not your phone.
- **The app downloads a 190 MB model** on first run. The bundled tiny model works offline immediately.
- **Release builds use R8.** If upstream adds code that R8 strips wrongly, the app can crash. That would need a patch or a debug build.

## Local build

```sh
export JAVA_HOME=/opt/homebrew/opt/openjdk@21/libexec/openjdk.jdk/Contents/Home
export ANDROID_HOME="$HOME/Library/Android/sdk"
cd hex/android
./gradlew assembleDebug
```

## Troubleshooting

| Problem | Fix |
|---|---|
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` | Old build used a different key. Uninstall once. |
| No workflow run appears | The schedule is daily; start one by hand. |
| Build fails at `assembleRelease` | Upstream `dev` is broken or R8 needs a rule. Check the Actions log. |
| Attestation verify fails | Update `gh`. The repo is public, so verification needs no login. |

## Credits

- [Hex](https://github.com/anomalyco/hex) by Anomaly, MIT licensed.
- Bundled models keep their own licenses: Whisper (MIT), Inter (SIL OFL 1.1), Google Books Ngram data (CC BY 3.0). The app ships their license texts under Settings → Acknowledgements.
- This repo only adds CI. It contains no Hex source code.
