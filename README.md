# Hex for Android (unofficial)

[![build](https://github.com/0x-sudo/hex-android-builder/actions/workflows/hex-apk.yml/badge.svg)](https://github.com/0x-sudo/hex-android-builder/actions/workflows/hex-apk.yml)

**Not official.** I am not affiliated with Anomaly. I just got curious and excited about Hex and wanted it on Android. If it breaks, blame me, not them. Star the real repo: [anomalyco/hex](https://github.com/anomalyco/hex).

## Download

https://github.com/0x-sudo/hex-android-builder/releases/latest/download/hex-android.apk

## Obtainium (recommended)

1. Install [Obtainium](https://github.com/ImranR98/Obtainium).
2. Add app → paste `https://github.com/0x-sudo/hex-android-builder`
3. Pick `hex-android.apk` if asked.

Each build is a new release, so updates arrive automatically.

## Install by hand

```sh
adb install -r hex-android.apk
```

- ARM64, Android 12+.
- Updates install over old builds. No uninstall needed.

## When it builds

- Daily at 03:17 UTC.
- Only when `android/` changed.
- Manual: Actions → `hex-apk` → Run workflow.

## Verify

```sh
sha256sum -c hex-android.apk.sha256
gh attestation verify hex-android.apk --repo 0x-sudo/hex-android-builder
```

## Limits

- Unofficial. Not on Play.
- Upstream `dev` can break the build.
- First app launch downloads a 190 MB model.
- Release builds use R8.

## Credits

[Hex](https://github.com/anomalyco/hex) by Anomaly, MIT licensed. Bundled models keep their own licenses.
