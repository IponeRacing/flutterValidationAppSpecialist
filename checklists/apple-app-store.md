# Apple App Store Compliance Checklist

Pre-submission checklist for Flutter apps targeting the Apple App Store.
Last updated: April 2026.

---

## Privacy & Data

### Privacy Manifest (`PrivacyInfo.xcprivacy`)
- [ ] File exists at `ios/Runner/PrivacyInfo.xcprivacy`
- [ ] `NSPrivacyAccessedAPITypes` declares all Required Reason APIs:
  - [ ] `NSPrivacyAccessedAPICategoryUserDefaults` (almost every Flutter app)
  - [ ] `NSPrivacyAccessedAPICategoryFileTimestamp` (if using file APIs)
  - [ ] `NSPrivacyAccessedAPICategoryDiskSpace` (if checking disk space)
  - [ ] `NSPrivacyAccessedAPICategorySystemBootTime` (if using boot time)
  - [ ] `NSPrivacyAccessedAPICategoryActiveKeyboards` (if detecting keyboards)
- [ ] Each API category has a valid `NSPrivacyAccessedAPITypeReasons` entry
- [ ] `NSPrivacyCollectedDataTypes` matches App Store Connect privacy labels
- [ ] `NSPrivacyTracking` set correctly (true/false)
- [ ] `NSPrivacyTrackingDomains` lists all tracking domains (if tracking=true)
- [ ] All third-party SDKs in `Podfile.lock` have their own privacy manifests
- [ ] All third-party SDKs are signed (xcframework signatures)

### App Tracking Transparency (ATT)
- [ ] `NSUserTrackingUsageDescription` in `Info.plist` with clear, non-technical text
- [ ] ATT request happens AFTER app UI is fully visible and rendered
- [ ] ATT request is NOT in `main()`, `initState()` of root widget, or `didFinishLaunching`
- [ ] Recommended: request in SplashPage/OnboardingPage after 1-2 second delay
- [ ] Check `trackingAuthorizationStatus` before requesting (don't re-ask if already decided)
- [ ] App works correctly whether user accepts or denies tracking
- [ ] `app_tracking_transparency` package in `pubspec.yaml`
- [ ] UMP (User Messaging Platform) consent flow for GDPR also implemented

### App Privacy Labels
- [ ] Data types match actual collection (analytics, advertising, crash logs, etc.)
- [ ] Linked vs unlinked data is correctly categorized
- [ ] Purpose for each data type is specified (analytics, app functionality, etc.)
- [ ] Third-party SDK data collection is included
- [ ] Firebase Analytics data is declared if using Firebase
- [ ] AdMob advertising data is declared if showing ads
- [ ] If using AI services: data shared with AI providers is declared

## Permissions

### Info.plist Usage Descriptions
- [ ] Every `NS*UsageDescription` key has a specific, user-facing explanation
- [ ] Descriptions explain WHY the app needs the permission
- [ ] No generic descriptions ("This app needs access to your camera")
- [ ] Descriptions are in the app's primary language
- [ ] Only permissions actually used are declared (no leftover keys)

Common keys to check:
| Key | When needed |
|-----|------------|
| `NSCameraUsageDescription` | Camera access |
| `NSPhotoLibraryUsageDescription` | Photo library read |
| `NSPhotoLibraryAddUsageDescription` | Photo library write |
| `NSLocationWhenInUseUsageDescription` | Foreground location |
| `NSLocationAlwaysUsageDescription` | Background location |
| `NSBluetoothAlwaysUsageDescription` | Bluetooth |
| `NFCReaderUsageDescription` | NFC |
| `NSMicrophoneUsageDescription` | Microphone |
| `NSContactsUsageDescription` | Contacts |
| `NSFaceIDUsageDescription` | Face ID |
| `NSCalendarsUsageDescription` | Calendar |
| `NSUserTrackingUsageDescription` | ATT tracking |

## Build & SDK

### iOS 26 SDK Requirement (Deadline: April 28, 2026)
- [ ] Built with Xcode 26 or later
- [ ] Built with iOS 26 SDK
- [ ] macOS Sequoia 15.6+ on build machine
- [ ] Minimum deployment target is compatible with Flutter's requirements
- [ ] Tested Liquid Glass UI appearance (iOS 26 default for native components)
- [ ] No deprecated API usage that iOS 26 removes

### Build Configuration
- [ ] `flutter build ios --release` succeeds without errors
- [ ] Code signing configured correctly (automatic or manual)
- [ ] Provisioning profile matches bundle ID
- [ ] Team ID is correct in Xcode project settings
- [ ] Bitcode setting matches requirements (disabled for Flutter)
- [ ] Archive exports correctly as `.ipa` via `xcodebuild -exportArchive`

## Authentication & Accounts

### Sign in with Apple
- [ ] If app offers ANY third-party login → Sign in with Apple is REQUIRED
- [ ] `sign_in_with_apple` package in `pubspec.yaml` (or equivalent)
- [ ] Sign in with Apple entitlement enabled in Xcode
- [ ] Apple Sign In capability enabled in App Store Connect
- [ ] Sign In button follows Apple HIG (size, style, placement)

### Account Deletion
- [ ] If app supports account creation → account deletion is REQUIRED
- [ ] Delete option accessible in-app (typically account settings)
- [ ] Deletion actually removes account + associated data (not just deactivation)
- [ ] If using Sign in with Apple: token revocation via REST API on deletion
- [ ] Confirmation dialog before deletion
- [ ] User informed of what data will be deleted
- [ ] Data retained only if legally required, with explanation

## In-App Purchases

- [ ] Digital goods/services use Apple's IAP (not Stripe/PayPal)
- [ ] Restore Purchases button exists and works
- [ ] Subscription terms clearly displayed before purchase
- [ ] Free trial duration explicitly stated
- [ ] Auto-renewal terms visible
- [ ] Price displayed matches App Store Connect configuration
- [ ] StoreKit 2 or `in_app_purchase` plugin properly configured
- [ ] Receipt validation (server-side recommended)
- [ ] Subscription management link available (to Apple's subscription settings)

## Age Rating

- [ ] Updated age rating questionnaire completed in App Store Connect (deadline: Jan 31, 2026)
- [ ] Correct category selected (4+, 9+, 13+, 16+, 18+)
- [ ] Content matches declared age rating
- [ ] If app has user-generated content: content moderation system in place
- [ ] If app has AI features: age-appropriate safeguards

## Metadata

### App Store Connect
- [ ] App name: max 30 characters, no keyword stuffing
- [ ] Subtitle: max 30 characters
- [ ] Keywords: max 100 characters per locale, comma-separated
- [ ] Description: accurate, matches app functionality
- [ ] What's New (release notes): filled for ALL localized versions
- [ ] Screenshots: correct dimensions for each device class
  - iPhone 6.9" (1320 x 2868 or 2868 x 1320)
  - iPhone 6.7" (1290 x 2796 or 2796 x 1290)
  - iPad 13" (2064 x 2752 or 2752 x 2064)
- [ ] App icon: 1024x1024 PNG, no alpha channel, no transparency
- [ ] Support URL: valid and accessible
- [ ] Privacy Policy URL: valid, accessible, comprehensive
- [ ] Category: correct primary and secondary
- [ ] No mentions of competing platforms ("also on Android")

### Export Compliance
- [ ] `ITSAppUsesNonExemptEncryption` in Info.plist
  - HTTPS only → set to `NO`
  - Custom encryption → proper documentation needed
- [ ] If exempt: `ITSEncryptionExportComplianceCode` may be needed

### AI/Automation (NEW 2025+)
- [ ] If using AI: disclosed to users how AI is used in the app
- [ ] If generating content: indicated when content is AI-generated
- [ ] If sharing data with AI providers: explicit user consent modal before any data sharing
- [ ] Consent modal specifies: AI provider name + data types shared

## Fastlane Integration

### Setup
- [ ] `ios/fastlane/Fastfile` configured with correct lanes
- [ ] API key authentication (recommended over password+2FA)
- [ ] `app_store_connect_api_key()` configured with valid key_id, issuer_id, key_filepath
- [ ] `.p8` key file exists at configured path (NOT committed to git)

### Metadata Upload (`deliver`)
- [ ] `fastlane/metadata/` directory structure exists
- [ ] All target languages have directories
- [ ] Each language has: `name.txt`, `subtitle.txt`, `description.txt`, `keywords.txt`, `release_notes.txt`
- [ ] `release_notes.txt` updated for current version in ALL languages
- [ ] No emojis in descriptions (rejected by App Store Connect)
- [ ] Subtitles max 30 characters per language
- [ ] `bundle exec fastlane upload_metadata` runs without errors
- [ ] `skip_binary_upload: true` used when uploading metadata only
- [ ] `precheck_include_in_app_purchases: false` if IAP not configured

### App Upload (`pilot` / TestFlight)
- [ ] IPA file built and exported correctly
- [ ] `pilot` lane configured with API key auth
- [ ] `skip_waiting_for_build_processing: true` for faster execution
- [ ] Build number incremented since last upload
- [ ] Upload succeeds to TestFlight

### Full Deploy
- [ ] `deploy` lane chains `upload_metadata` → `upload_app`
- [ ] Both steps succeed without manual intervention

## Common Rejection Reasons to Double-Check

1. **Guideline 2.1 (Performance)**: App crashes, broken flows, ATT before UI visible
2. **Guideline 2.3 (Metadata)**: Misleading descriptions, wrong screenshots
3. **Guideline 3.1.1 (IAP)**: Not using Apple's IAP for digital goods
4. **Guideline 4.0 (Design)**: Not following HIG, broken layout on certain devices
5. **Guideline 5.1.1 (Privacy)**: Missing privacy manifest, incomplete data declarations
6. **Guideline 5.1.2 (ATT)**: Tracking without ATT prompt, or ATT timing issues