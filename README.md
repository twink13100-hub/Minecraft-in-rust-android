# SteelMC Android ARM64 — port bring-up

This starter deliberately builds the **real upstream SteelMC Rust server** for the
Android `aarch64-linux-android` target. It does not replace SteelMC with a mock server
or pretend that a native executable is an APK.

## First milestone: prove the upstream source can cross-compile

1. Fork `https://github.com/Steel-Foundation/SteelMC` into your own GitHub account.
2. Add `.github/workflows/android-arm64.yml` from this package to the fork.
3. Open **Actions → SteelMC Android ARM64 port check → Run workflow**.
4. If the build succeeds, download the artifact named
   `steelmc-android-arm64-native-validation`.

The workflow uses the upstream repository's pinned `nightly-2026-10-14` Rust
toolchain and attempts a release build of the actual `steel` package for Android
API 26 / ARM64. It turns off the optional `mimalloc` feature for the first portability
check; it does not change the server's gameplay implementation.

## Why this is not yet an APK

The upstream `steel` package is a command-line server binary. An APK needs an Android
application shell and a supported way to run the server lifecycle inside Android.
Simply placing an ELF executable in an APK would not make it a working Android app.

The next engineering milestone, after this CI check passes, is to refactor the
server startup into a reusable library entry point and expose it through JNI, then
add an Android foreground service and a small activity for Start/Stop, logs, storage
paths, and server configuration. The APK workflow should only be added once that
real JNI entry point exists and can be tested; otherwise the APK would be a fake
wrapper around a server that cannot actually start.

## Likely first blockers

- Android-incompatible APIs or dependencies in the current upstream source.
- `crossterm` terminal UI and the existing shutdown/signal handling.
- Dependencies or build scripts that assume desktop Linux.
- Runtime storage paths and Android process lifecycle.
- Memory, CPU, and thermal limits on phones.

A successful cross-compile is only milestone 1. Runtime testing on an Android ARM64
device is required before calling the port usable.

## License

SteelMC is licensed under AGPL-3.0-or-later. Keep the upstream license and notices,
and publish the corresponding source for any distributed modified version as required
by the license.
