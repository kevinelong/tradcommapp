# Build provenance

Engineering record for the binaries published here. The user-facing description,
install steps and limitations are in [README.md](README.md); this file is the
audit trail: what was built, from which source, with which toolchain, and what
the signature does and does not prove.

## tradcomm-0.2.0.apk

| Field | Value |
|---|---|
| Package | `com.tradcomm.tradcomm_app` |
| `versionName` / `versionCode` | `0.2.0` / `2` |
| Built | 2026-10-19 |
| Size | 51,159,775 bytes (48.8 MB) |
| SHA-256 | `7d16e3d30435300a1e8b0659b87f57ff0301f14f6e46422e08ecab537826d3f3` |
| Source repository | private (`kevinelong/tradcomm`); distribution lives here |
| Source commit | `0171b95c989fdcea1a91bc6ac4fa4cbf4448a035` |
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

### Signing

Signed with a **production release key**, not the Android debug key. Certificate:

| Field | Value |
|---|---|
| Subject | `CN=TradComm, OU=Release, O=Kevin Ernest Long, C=US` |
| Key | RSA 4096, validity 10000 days, PKCS#12 keystore |
| Certificate SHA-256 | `285b76687c334a95b456356a8d8549923f30ad4c1af88828e7543d66ef4d507b` |
| Signature schemes | v2 and v3 (verified with `apksigner verify`) |

Verify it yourself:

```sh
apksigner verify --print-certs tradcomm-0.2.0.apk
```

The keystore and its password are not in this repository and not in the release
assets. **Every future update must be signed with the same key**: Android
refuses an update signed with a different certificate, so a lost key means a new
package id and a forced uninstall for every existing user.

### Superseded build

`v0.1.0` was signed with the debug key and is kept only for the record. Do not
install it: a build signed with the release key cannot replace it, so you would
have to uninstall (losing study history) to move to any later version.

## Runtime configuration shipped in this build

- **API base URL:** `http://10.0.2.2:8080` - the Android emulator's alias for the
  host machine. No API is deployed yet, so on a real phone content sync and
  licence redemption fail and the app runs on the bundled bank with the
  40-question free tier. The next build will be produced by
  `scripts/build-release-apk.sh https://<host>` in the source repository, which
  bakes a real base URL in and refuses to publish a debug-signed APK.
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
  read back from the device as `versionName=0.2.0`, `versionCode=2`.
- Signature verified as the release certificate above; installing this APK over
  the earlier debug-signed build is refused by Android with
  `INSTALL_FAILED_UPDATE_INCOMPATIBLE`, as expected for a key change.
- Content bank validates against the schema in the source repository
  (`dart run bin/validate.dart` -> 0 issues).

## Publishing checklist for the next build

1. Build with the same production keystore (see the key-loss warning above).
2. Point `--dart-define=TRADCOMM_API_BASE=https://...` at a reachable API before
   shipping a build that is expected to sync or redeem.
3. Record size, SHA-256, source commit and toolchain here.
4. Attach the APK and its `.sha256` to a GitHub release, never to the git tree.
