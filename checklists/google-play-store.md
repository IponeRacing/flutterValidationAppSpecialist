# Google Play Store Compliance Checklist

Pre-submission checklist for Flutter apps targeting the Google Play Store.
Last updated: April 2026.

---

## Build & API Level

### Target SDK
- [ ] `compileSdk` >= 35 in `android/app/build.gradle.kts`
- [ ] `targetSdk` >= 35 (Android 15 requirement since Aug 31, 2025)
- [ ] `minSdk` set appropriately (21-24 recommended for Flutter)
- [ ] Aware of upcoming Android 16 (API 36) requirement (expected Aug 2026)

### Build Format
- [ ] App built as AAB (Android App Bundle), NOT APK
- [ ] `flutter build appbundle --release` succeeds
- [ ] AAB file under 150MB (Play Store limit)
- [ ] If over 150MB: using Play Asset Delivery or Play Feature Delivery

### App Signing
- [ ] Google Play App Signing enabled
- [ ] Upload keystore configured (`upload-keystore.jks` or `upload-keystore.p12`)
- [ ] `key.properties` file exists locally (NOT committed to git)
- [ ] `.gitignore` excludes `key.properties`, `*.jks`, `*.keystore`
- [ ] Signing config in `build.gradle.kts` references `key.properties`

### Proguard / R8
- [ ] `proguard-rules.pro` contains necessary keep rules
- [ ] `minifyEnabled true` for release builds
- [ ] `shrinkResources true` for release builds
- [ ] Keep rules for: Firebase, Gson, Retrofit, OkHttp (as applicable)
- [ ] Flutter-specific keep rules present
- [ ] Test release build thoroughly (R8 can strip needed classes)

## Privacy & Data

### Data Safety Section
- [ ] Completed in Google Play Console
- [ ] Accurately reflects ALL data collected, shared, and stored
- [ ] Includes data from third-party SDKs (Firebase, AdMob, analytics)
- [ ] Data encryption in transit declared
- [ ] Data deletion mechanism declared (if applicable)
- [ ] Updated whenever app's data practices change

### Privacy Policy
- [ ] Valid URL provided in Play Console
- [ ] Hosted online (website, GitHub Pages) — NOT a PDF
- [ ] Accessible from within the app
- [ ] Covers all data types collected
- [ ] Covers third-party SDKs and their data practices
- [ ] Available in the app's primary language

### Account Deletion
- [ ] If app supports account creation → must support deletion
- [ ] Accessible from within the app
- [ ] Actually deletes account data (not just deactivation)
- [ ] User informed of deletion scope and timeline
- [ ] Data retention only if legally required

## Permissions

### AndroidManifest.xml Audit
- [ ] Review ALL permissions (including those merged from plugins)
- [ ] Remove unused permissions with `tools:node="remove"`:
  ```xml
  <uses-permission android:name="android.permission.READ_PHONE_STATE" tools:node="remove"/>
  ```
- [ ] Each permission has a clear justification

### Critical Permissions to Review
| Permission | Notes |
|-----------|-------|
| `READ_MEDIA_IMAGES` | Needs declaration form or use photo picker instead |
| `READ_MEDIA_VIDEO` | Needs declaration form or use photo picker instead |
| `ACCESS_FINE_LOCATION` | Must justify in Data Safety |
| `ACCESS_BACKGROUND_LOCATION` | Needs strong justification, separate approval |
| `CAMERA` | Must justify in Data Safety |
| `RECORD_AUDIO` | Must justify in Data Safety |
| `READ_CONTACTS` | Must justify in Data Safety |
| `READ_PHONE_STATE` | Often injected by plugins — remove if not needed |
| `QUERY_ALL_PACKAGES` | Restricted since Android 11 |
| `REQUEST_INSTALL_PACKAGES` | Restricted |
| `MANAGE_EXTERNAL_STORAGE` | Restricted, needs approval |
| `SYSTEM_ALERT_WINDOW` | Needs strong justification |

### Photo & Video Permissions Policy
- [ ] If `READ_MEDIA_IMAGES` or `READ_MEDIA_VIDEO` present:
  - [ ] Submit declaration form in Play Console OR
  - [ ] Migrate to `ACTION_PICK_IMAGES` (system photo picker)
- [ ] If using `image_picker` plugin: check injected permissions
- [ ] If only occasional photo access: use photo picker, remove broad permissions

### Runtime Permissions
- [ ] All dangerous permissions requested at runtime (not just in manifest)
- [ ] Permission request happens in context (when feature is about to be used)
- [ ] App handles permission denial gracefully
- [ ] No repeated permission requests without user action

## Content & Ratings

### Content Rating
- [ ] Content rating questionnaire completed in Play Console
- [ ] Rating matches actual app content
- [ ] Re-submitted if content has changed

### Age-Restricted Content (Deadline: March 4, 2026)
- [ ] If app has matchmaking/dating: minors blocked via Play Console features
- [ ] If app has real money gambling: minors blocked
- [ ] If app has age-restricted content: appropriate safeguards in place

### Families Policy (if targeting children)
- [ ] If Designed for Families: complies with all family program requirements
- [ ] No behavioral advertising for children
- [ ] Age-appropriate content only
- [ ] Teacher Approved badge requirements (if applicable)

## Security

### Play Integrity API
- [ ] No references to deprecated SafetyNet (removed May 2025)
- [ ] Play Integrity API implemented for sensitive operations (if applicable)
- [ ] Server-side validation of integrity verdicts
- [ ] Handles all verdict levels (basic, device, strong)

### Sensitive Data
- [ ] No hardcoded API keys, secrets, or credentials in source
- [ ] `.gitignore` properly configured for sensitive files
- [ ] No debug endpoints in release builds
- [ ] SSL pinning for sensitive API calls (if applicable)
- [ ] No logging of sensitive user data in release builds

## Metadata

### Store Listing
- [ ] App title: max 30 characters
- [ ] Short description: max 80 characters
- [ ] Full description: max 4000 characters, accurate
- [ ] No keyword stuffing in title or descriptions
- [ ] No misleading claims
- [ ] No mentions of competing platforms' advantages
- [ ] Feature graphic: 1024x500 PNG or JPG
- [ ] App icon: 512x512 PNG
- [ ] Screenshots: min 2, max 8 per device type
  - Phone: 16:9 or 9:16 ratio
  - Tablet (7"): if supporting tablets
  - Tablet (10"): if supporting tablets
- [ ] Category correctly selected
- [ ] Contact email valid
- [ ] Content rating completed

### Release Notes
- [ ] What's new text provided for current release
- [ ] Translated for all target languages
- [ ] Accurately describes changes

## Fastlane Integration

### Setup
- [ ] `Fastfile` has `platform :android` section
- [ ] Google Play Service Account JSON key configured
- [ ] `SHARED_GOOGLE_JSON_KEY` or equivalent path set
- [ ] Service account has correct permissions in Google Play Console:
  - Release manager
  - Edit store listing
  - Manage production/alpha/beta releases
- [ ] JSON key file NOT committed to git

### App Upload (`supply`)
- [ ] AAB file path configured correctly in `upload_to_play_store`
- [ ] Correct `package_name` specified
- [ ] Track configured (`internal`, `alpha`, `beta`, `production`)
- [ ] `skip_upload_metadata: true` if uploading binary only
- [ ] `skip_upload_images: true` / `skip_upload_screenshots: true` if not needed
- [ ] Upload succeeds to target track

### Metadata Upload
- [ ] If using Fastlane for metadata:
  - [ ] `supply` configured with `--metadata_path`
  - [ ] All target languages have directories
  - [ ] `title.txt`, `short_description.txt`, `full_description.txt` per language
  - [ ] `changelogs/{versionCode}.txt` for release notes

### Full Deploy
- [ ] Lane chains metadata + binary upload
- [ ] Both steps succeed

## Common Rejection Reasons to Double-Check

1. **Permissions**: Over-requested permissions, missing justification for broad media access
2. **Data Safety**: Incomplete or inaccurate Data Safety section
3. **Privacy Policy**: Missing, broken URL, or insufficient coverage
4. **Metadata**: Misleading description, wrong screenshots, keyword stuffing
5. **Target API**: Not targeting required API level
6. **Content Rating**: Missing or inaccurate questionnaire
7. **Deceptive Behavior**: Hidden features, undisclosed data collection
8. **Impersonation**: Using another app's branding or name