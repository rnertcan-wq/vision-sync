# VISION — Android APK source

This repository is being prepared for the Android-only VISION APK. The source archive is available separately and must be placed at:

`source/VISION-Android-1.4.0-source.zip`

## Upload the source archive

1. Download the prepared archive from the ChatGPT conversation.
2. In this repository, choose **Add file → Upload files**.
3. Upload it to the `source/` directory with the exact filename `VISION-Android-1.4.0-source.zip`.
4. Commit the upload to `main`.

That push automatically starts **VISION Android APK** in GitHub Actions.

## Build output

Open **Actions → VISION Android APK → latest run → Artifacts** and download `VISION-Android-debug-APK`. The artifact contains the debug APK and its SHA-256 checksum.

## Build configuration

- Application ID: `com.vision.mind`
- Version: `1.4.0` (versionCode 140)
- Minimum Android: API 26
- Compile/target SDK: 35
- JDK: 17
- Gradle: 8.11.1

The workflow runs the Android source/security verifier and unit tests before building. This is a debug APK for sideloading and testing, not a Play Store release.

## EVREN security

No EVREN API key, signing key, user database, or model weights belong in this public repository. Enter your own EVREN API key inside the app after installation. Base URL: `https://evren-llmapi.ssyz.org.tr/v1`.

The CI build proves compilation and unit tests only. Live EVREN calls and physical-device checks for camera, microphone, background behavior and permissions still require device testing.
