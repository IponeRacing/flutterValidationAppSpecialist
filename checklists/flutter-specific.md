# Flutter-Specific Compliance Checklist

Cross-platform checks specific to Flutter apps targeting both stores.
Last updated: April 2026.

---

## Project Configuration

### pubspec.yaml
- [ ] Version format: `X.Y.Z+buildNumber` (e.g., `1.0.5+34`)
- [ ] Build number strictly incremented since last submission to EACH store
- [ ] iOS and Android may have different build numbers — verify both
- [ ] All dependencies up to date (especially `flutter`, `dart`)
- [ ] No `dev_dependencies` leaking into production builds
- [ ] Assets properly declared under `flutter.assets`
- [ ] All declared asset paths actually exist

### Flutter SDK
- [ ] Using stable channel
- [ ] SDK version compatible with iOS 26 SDK requirements
- [ ] `flutter doctor` reports no critical issues
- [ ] `dart analyze` reports no errors (warnings acceptable)

## Privacy Manifest (iOS)

### PrivacyInfo.xcprivacy
- [ ] File exists at `ios/Runner/PrivacyInfo.xcprivacy`
- [ ] NOT auto-generated placeholder — must be customized for your app
- [ ] Includes declarations for Flutter engine's own API usage:
  - `NSPrivacyAccessedAPICategoryUserDefaults` (Flutter uses UserDefaults)
- [ ] Includes declarations for ALL pod dependencies
- [ ] Run `pod install` and check that pods with privacy manifests are correctly bundled

### Plugin Privacy Audit
- [ ] For each plugin in `pubspec.yaml`, check:
  - Does the plugin's pod have its own `PrivacyInfo.xcprivacy`?
  - What APIs does it use? (check plugin source or documentation)
  - Are those APIs declared in your app's privacy manifest?
- [ ] Common plugins that need attention:
  | Plugin | Privacy APIs Used |
  |--------|------------------|
  | `shared_preferences` | UserDefaults |
  | `path_provider` | File timestamp, disk space |
  | `firebase_core` | UserDefaults, disk space |
  | `google_mobile_ads` | UserDefaults, tracking domains |
  | `flutter_local_notifications` | UserDefaults |
  | `in_app_purchase` | UserDefaults |

## Permission Injection (Android)

### Manifest Merging Audit
- [ ] Run `flutter build apk --debug` then check merged manifest:
  ```
  android/app/build/intermediates/merged_manifests/debug/AndroidManifest.xml
  ```
- [ ] Compare merged permissions vs your declared permissions
- [ ] Remove unwanted permissions injected by plugins:
  ```xml
  <manifest xmlns:tools="http://schemas.android.com/tools">
    <uses-permission android:name="android.permission.READ_PHONE_STATE" tools:node="remove"/>
  </manifest>
  ```
- [ ] Common plugins that inject unexpected permissions:
  | Plugin | May Inject |
  |--------|-----------|
  | `firebase_messaging` | `WAKE_LOCK`, `RECEIVE_BOOT_COMPLETED` |
  | `camera` | `CAMERA`, `RECORD_AUDIO`, `WRITE_EXTERNAL_STORAGE` |
  | `geolocator` | `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` |
  | `image_picker` | `READ_MEDIA_IMAGES`, `READ_MEDIA_VIDEO`, `CAMERA` |
  | `flutter_local_notifications` | `VIBRATE`, `RECEIVE_BOOT_COMPLETED` |
  | `url_launcher` | `QUERY_ALL_PACKAGES` |

## Localization

### ARB Files
- [ ] `lib/l10n/` contains `.arb` files for all target languages
- [ ] `app_en.arb` (or primary language) is the source of truth
- [ ] All keys in primary `.arb` exist in ALL other `.arb` files
- [ ] No orphaned keys in secondary files that don't exist in primary
- [ ] `@@locale` key present in each `.arb` file
- [ ] `l10n.yaml` configuration file exists and is correct
- [ ] `flutter gen-l10n` runs without errors
- [ ] Generated files in `lib/l10n/app_localizations*.dart` are up to date

### Store Metadata Localization
- [ ] App name localized for all target markets
- [ ] Description localized for all target markets
- [ ] Keywords localized for all target markets (iOS)
- [ ] Release notes localized for all target markets
- [ ] Screenshots localized (or use universal screenshots)
- [ ] Consistency between in-app language and store listing language

## Build Verification

### iOS Release Build
- [ ] `flutter build ios --release --no-codesign` succeeds (CI/verification only — does NOT produce IPA)
- [ ] `flutter build ios --release` succeeds (with signing — also does NOT produce IPA)
- [ ] **For store/Fastlane upload**: use `flutter build ipa --release` which produces a signed IPA at `build/ios/ipa/`
- [ ] Copy IPA to Fastlane expected path (e.g., `ios/build/ipa/`) if Fastlane lane references a specific path
- [ ] Archive created: `xcodebuild -workspace ... -scheme Runner -archivePath ... archive`
- [ ] IPA exported: `xcodebuild -exportArchive -archivePath ... -exportOptionsPlist ... -exportPath ...`
- [ ] Archive visible in Xcode Organizer (`~/Library/Developer/Xcode/Archives/`)
- [ ] No build warnings that indicate potential runtime issues

### Android Release Build
- [ ] `flutter build appbundle --release` succeeds
- [ ] AAB file generated at `build/app/outputs/bundle/release/app-release.aab`
- [ ] No Proguard/R8 errors
- [ ] Release APK also testable: `flutter build apk --release`

### Common Build Issues
- [ ] No `dart:mirrors` usage (not supported in AOT/release)
- [ ] No `dart:developer` imports in production code paths
- [ ] All native dependencies compile for both platforms
- [ ] CocoaPods up to date: `cd ios && pod install --repo-update`
- [ ] Gradle wrapper up to date

## Asset Verification

### App Icons
- [ ] iOS icon: `ios/Runner/Assets.xcassets/AppIcon.appiconset/`
  - 1024x1024 PNG, no alpha channel, no transparency, no rounded corners
  - All required sizes generated
- [ ] Android icon: `android/app/src/main/res/mipmap-*/`
  - Adaptive icon with foreground and background layers
  - 512x512 for Play Store listing
- [ ] Icons match across platforms (consistent branding)

### Launch Screen / Splash
- [ ] iOS: `ios/Runner/Assets.xcassets/LaunchImage.imageset/` configured
- [ ] Android: `android/app/src/main/res/drawable/launch_background.xml` configured
- [ ] Splash screen matches app theme
- [ ] No jarring transition from splash to first screen

## Debug Artifacts

### Cleanup
- [ ] No `print()` or `debugPrint()` exposing sensitive data
- [ ] No debug API endpoints in production configuration
- [ ] No test accounts or credentials in source code
- [ ] `kDebugMode` / `kReleaseMode` checks where appropriate
- [ ] Firebase Crashlytics or equivalent configured for release crash reporting
- [ ] Analytics properly initialized (not double-counting, not in debug mode)

## Fastlane Cross-Platform

### Shared Configuration
- [ ] If using shared Fastlane config (e.g., `~/.fastlane/SharedFastfile`):
  - [ ] API keys configured for both Apple and Google
  - [ ] `import` statement at top of project Fastfile
  - [ ] Apple: `app_store_connect_api_key()` helper available
  - [ ] Google: service account JSON key path available
  - [ ] Key files NOT committed to any git repo

### iOS Fastlane Lanes
- [ ] `generate_metadata` — generates metadata from CSV/source
- [ ] `upload_metadata` — pushes metadata to App Store Connect via `deliver`
- [ ] `upload_app` — uploads IPA to TestFlight via `pilot`
- [ ] `deploy` — chains metadata + app upload

### Android Fastlane Lanes
- [ ] `upload_app` — uploads AAB to Google Play via `supply`
- [ ] Correct `package_name` configured
- [ ] Correct track (`internal`, `alpha`, `beta`, `production`)
- [ ] Service account has necessary Play Console permissions

### Pre-Upload Verification
- [ ] Version number incremented in `pubspec.yaml`
- [ ] Build number higher than last uploaded build (both stores)
- [ ] Release notes updated for all localized languages
- [ ] Both builds succeed before starting uploads
- [ ] `bundle exec fastlane [lane]` runs correctly (using bundled Fastlane)

## Common Cross-Platform Gotchas

1. **Build number mismatch**: iOS and Android build numbers may need to be different if one store is ahead
2. **Permission asymmetry**: iOS requires `Info.plist` descriptions; Android requires runtime requests
3. **Plugin version drift**: Same plugin version may behave differently on iOS vs Android
4. **Localization gaps**: Missing translations crash on some locales, fallback on others
5. **Asset naming**: iOS is case-insensitive, Android is case-sensitive for file names
6. **Deep links**: Universal Links (iOS) and App Links (Android) have different verification
7. **Background execution**: Different rules for background tasks on each platform
8. **Notification channels**: Android requires notification channels (API 26+), iOS doesn't
