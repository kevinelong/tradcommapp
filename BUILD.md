# Build provenance

Engineering record for the binaries published here. The user-facing description,
install steps and limitations are in [README.md](README.md); this file is the
audit trail: what was built, from which source, with which toolchain, and what
the signature does and does not prove.

## tradcomm-0.1.0.apk

| Field | Value |
|---|---|
| Package | `com.tradcomm.tradcomm_app` |
| `versionName` / `versionCode` | `0.1.0` / `1` |
| Built | 2026-10-19 |
| Size | 51,159,771 bytes (48.8 MB) |
| SHA-256 | `9245a88289bc14f761d0ccaf4a1cd4ce71d6217f698119dabde1c1a8c0f54dc1` |
| Source repository | private (`kevinelong/tradcomm`); distribution lives here |
| Source commit | `b9d6c612d9db60888f81eaaca408f96b95979893` |
| Build mode | `flutter build apk --release` (universal APK, R8 enabled, icon tree-shaking on) |
| minSdk / targetSdk | 24 / 36 |

Toolchain:

| Component | Version |
|---|---|
| Flutter | 3.47.7 (stable) |
| Dart | 3.13.5 |
| JDK | Temurin 17.0.20.1 |
| Android Gradle Plugin | 9.1.0 |
| Gradle | 9.3.1 |
| Kotlin | 2.4.0 |
| Android SDK platform | 36 |

## Signing: read this before distributing

The APK is signed with the **Android debug keystore**. `packages/app`'s
`build.gradle.kts` sets `signingConfig = signingConfigs.getByName("debug")` for
the release build because no production keystore exists yet.

Consequences, all of them real:

1. It is not acceptable for Google Play. Play requires a production key.
2. **A future properly signed build cannot be installed over this one.** Android
   refuses an update whose signing certificate differs; users will have to
   uninstall first, which loses local study history and any licence state.
3. Anyone can produce an APK with the same certificate, so the signature is not
   evidence of provenance. The SHA-256 published here is the only integrity
   check: compare it after download.

Untested for release: the app was built and installed on an Android 14 (API 34)
emulator; it has not been exercised on a physical device, and R8 shrinking was
verified only by launching the app and reaching onboarding.

## Runtime configuration shipped in this build

- **API base URL:** `http://10.0.2.2:8080` - the Android emulator's alias for the
  host machine. On a real phone that address is unreachable, so in this build
  content sync and licence redemption will fail and the app runs on the bundled
  bank with the 40-question free tier. A build pointed at a hosted API will be
  published with a real base URL.
- **Bundled content:** the 300-question bank is compiled into the APK
  (`assets/questions.json`) and copied into the local database on first launch.
- **No telemetry endpoint is called.** The app has no analytics SDK.

## Reproducing the build

From the private source repository, at the commit above:

```sh
cd packages/app
flutter pub get
flutter build apk --release
# build/app/outputs/flutter-apk/app-release.apk
```

Byte-identical output is not guaranteed across machines (Gradle embeds
timestamps); the meaningful checks are the package id, the version, and that the
source commit matches.

## Verification results at build time

- `flutter analyze` clean; `flutter test` 47 tests passing.
- Release APK installed on API 34 and launched to the onboarding screen; version
  read back from the device as `versionName=0.1.0`, `versionCode=1`.
- Content bank validates against the schema in the source repository
  (`dart run bin/validate.dart` -> 0 issues).

## Publishing checklist for the next build

1. Build with a production keystore, or repeat the debug-key caveat above.
2. Point `--dart-define=TRADCOMM_API_BASE=https://...` at a reachable API before
   shipping a build that is expected to sync or redeem.
3. Record size, SHA-256, source commit and toolchain here.
4. Attach the APK and its `.sha256` to a GitHub release, never to the git tree.
