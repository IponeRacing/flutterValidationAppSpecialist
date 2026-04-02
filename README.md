# Flutter App Store Validator

**Your pre-submission compliance co-pilot for Apple App Store & Google Play.**

Stop getting rejected. Ship with confidence.

---

## What is this?

This is a **Claude Code specialized agent** that audits your Flutter app for App Store and Google Play compliance **before you submit**. It catches the issues that cause 80% of rejections — privacy manifests, missing permissions rationale, ATT timing, metadata gaps, API level mismatches — so you don't waste days in review limbo.

Built from hard-won experience shipping Flutter apps through both stores, updated for the **April 2026 deadlines**.

## What it checks

### Apple App Store
- Privacy Manifest (`PrivacyInfo.xcprivacy`) — required reason APIs, data collection declarations
- App Tracking Transparency (ATT) — timing, UI visibility, `NSUserTrackingUsageDescription`
- Third-party SDK signatures and privacy manifests
- iOS 26 SDK / Xcode 26 build requirement (April 28, 2026 deadline)
- Age rating questionnaire (updated 13+/16+/18+ categories)
- Sign in with Apple + account deletion flow
- In-App Purchase configuration and StoreKit 2
- App Privacy labels accuracy vs actual data collection
- Info.plist permission descriptions (camera, location, photos, NFC, Bluetooth...)
- Metadata validation (screenshots dimensions, keyword limits, subtitle length)
- AI/automation disclosure requirements
- Export compliance (encryption)

### Google Play Store
- Data Safety section completeness and accuracy
- Target API level (Android 15 / API 35+ as of Aug 2025)
- AAB format (APK no longer accepted)
- Photo & Video permissions policy (READ_MEDIA_IMAGES/VIDEO declarations)
- Play Integrity API (SafetyNet replacement)
- Age-Restricted Content policy
- Privacy policy hosted online (not PDF)
- AndroidManifest.xml permission audit
- Proguard/R8 rules validation
- App signing configuration

### Flutter-Specific
- `PrivacyInfo.xcprivacy` in `ios/Runner/` — auto-generated vs custom
- Plugin permission injection audit (`AndroidManifest.xml` merged permissions)
- `pubspec.yaml` version/build number validation
- Localization completeness (`lib/l10n/*.arb` files)
- Release build configuration (obfuscation, tree-shaking)
- Fastlane integration checks (metadata, screenshots, delivery)

## Quick Start

### As a Claude Code agent

Add this to your project's `CLAUDE.md`:

```markdown
Use the flutter-app-store-validator agent from https://github.com/IponeRacing/flutterValidationAppSpecialist
for pre-submission compliance checks.
```

Or reference the CLAUDE.md directly:

```bash
# In your Flutter project root
claude --agent https://raw.githubusercontent.com/IponeRacing/flutterValidationAppSpecialist/main/CLAUDE.md
```

### Manual checklist

Browse the checklists directly:
- [Apple App Store Checklist](checklists/apple-app-store.md)
- [Google Play Store Checklist](checklists/google-play-store.md)
- [Flutter-Specific Checklist](checklists/flutter-specific.md)

## Key deadlines (2026)

| Deadline | Requirement |
|----------|------------|
| **Jan 31, 2026** | Apple age rating questionnaire must be updated |
| **Mar 4, 2026** | Google Play age-restricted content policy compliance |
| **Apr 28, 2026** | Apple requires iOS 26 SDK / Xcode 26 for submissions |
| **Aug 31, 2026** | Google Play likely requires Android 16 (API 36) target |

## Why this exists

Every Flutter developer knows the pain:
1. You build your app
2. You submit to the stores
3. You get rejected for something you didn't know about
4. You fix it, resubmit, wait 24-48h
5. You get rejected for something *else*
6. Repeat 3-8 times

This agent runs the full compliance gauntlet locally in seconds, catching issues before they cost you days.

## Contributing

Found a new rejection reason? A policy change we missed? PRs welcome.

## License

MIT

---

*Built with frustration and determination by developers who got rejected one too many times.*