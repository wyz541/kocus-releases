# Kocus releases

Public download mirror for Kocus release packages. The source repository is
private. This repository holds built installers only and carries no source code.

Kocus is a Kotlin Multiplatform translation app for Android, iOS, and desktop.
Every package below bundles the 461,860,800-byte Hy-MT2 model, so translation
works offline.

## Download

Open the [Releases](../../releases) page and pick the asset for your platform.
Anonymous download works. A GitHub account is not necessary.

| platform | asset | bytes | sha256 |
| --- | --- | --- | --- |
| macOS (Apple silicon) | `Kocus-1.0.0-macos-arm64.dmg` | 634757766 | `33feddd1543de5d5aebc49bad332905f55b359b3c4f9177774f5f46657b84f52` |
| Android (arm64-v8a) | `Kocus-1.0.0-android-arm64.apk` | 539920864 | `183f7f67cd8fb3b7c58766af02ee734e84cee783e919693f455e6246914f07d9` |
| iOS (arm64 device) | `Kocus-1.0.0-ios-arm64.ipa` | 511639201 | `711bb302c6317176a34e74d2914f82e9f8abaf11a877fe18b6ff7c8e5c9b5e7d` |

There is no Windows package yet. See [Windows](#windows) below.

To download and verify from a terminal:

```sh
curl -LO https://github.com/wyz541/kocus-releases/releases/download/v1.0.0/Kocus-1.0.0-macos-arm64.dmg
shasum -a 256 Kocus-1.0.0-macos-arm64.dmg
```

Compare the printed digest with the `sha256` column above. Use `sha256sum` on
Linux and Windows.

## Signing status

No package carries a distribution signature. Each platform needs a different
step before the app runs.

| platform | signature | required step |
| --- | --- | --- |
| macOS | ad-hoc (`codesign --sign -`), no Developer ID, not notarized | right-click Open, or clear the quarantine bit |
| Android | Android debug signature, regenerated on each build | uninstall the previous build first |
| iOS | none | re-sign with your own Apple ID first |

## macOS

Apple silicon (arm64) only, macOS 11 or newer. There is no Intel slice.

Gatekeeper blocks the first launch. Drag `Kocus.app` from the mounted image
onto the `Applications` link. Then right-click the installed app, choose Open,
and confirm in the dialog. If macOS still refuses, run
`xattr -dr com.apple.quarantine /Applications/Kocus.app` once.

The app bundles its own Java runtime. Import dictionaries (MDX/MDD) from the
app's dictionary manager. Uninstalling leaves data in `~/.kmp-translator`.

## Android

arm64-v8a only. Other ABIs ship no Hy-MT2 runtime and report `Unavailable` for
local translation.

The debug signature changes from one release to the next. Uninstall the
previous build before installing this one. A release-signed build will not
overwrite a debug-signed install either.

`versionName` / `versionCode` come from the build configuration (`1.0` / `1`).
They do not follow the release version.

## iOS

arm64 device build only. There is no simulator slice.

The build account holds a free Apple ID only. There is no distribution identity
to sign with, and a free provisioning profile expires after seven days. Re-sign
the IPA with your own Apple ID before installing, in Xcode or with a
sideloading tool. iOS refuses to install an app whose signature it cannot
verify.

Re-sign every Mach-O binary in the bundle, not just `Payload/Kocus.app/Kocus`.
This is a Debug build, so that file is a stub that loads
`Payload/Kocus.app/Kocus.debug.dylib`. The bundle also carries
`__preview.dylib` and `Frameworks/libtranslator-hy-mt2.dylib`.

Local (offline) translation can report `Unavailable` in a re-signed copy.
Before it loads `Payload/Kocus.app/Frameworks/libtranslator-hy-mt2.dylib`, the
app checks that file's size and SHA-256 against `runtime.libraryBytes` and
`runtime.librarySha256` in `Frameworks/runtime.properties`. A signing tool that
rewrites the dylib leaves both values stale, and the check then fails. Online
translation is not affected. To keep local translation, build from source with
your own identity.

## Windows

No Windows package is available yet. A `Kocus-<version>-windows-x64.msi` is
planned for a later release.
