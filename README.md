# VISION 1.5.0 — Android APK

This repository builds the VISION Android application from the source archive uploaded to this repository.

## Upload the source archive

1. Download `VISION-Android-1.5.0-source.zip` from the ChatGPT conversation.
2. Open this repository and choose **Add file → Upload files**.
3. Upload the ZIP into the `source/` folder with this exact filename:
   `VISION-Android-1.5.0-source.zip`
4. Commit the upload to `main`.

The archive must contain the `apps/android/` directory at its root. Uploading it to the expected path automatically starts **VISION Android APK** in GitHub Actions. You can also start the workflow manually from **Actions → VISION Android APK → Run workflow**.

## Build output

Open **Actions → VISION Android APK → latest run → Artifacts** and download:

- `VISION-Android-1.5.0-debug-APK`
- APK: `VISION-Android-1.5.0-debug.apk`
- SHA-256 checksum: `VISION-Android-1.5.0-debug.apk.sha256`

The workflow runs the source/security verifier, Android unit tests, and Gradle debug build. This is a debug APK for sideloading and testing, not a Play Store-signed release.

## Build configuration

- Application ID: `com.vision.mind` (debug package: `com.vision.mind.debug`)
- Version: `1.5.0` (versionCode 150)
- Minimum Android: API 26 (Android 8.0)
- Compile/target SDK: 35
- JDK: 17
- Gradle: 8.11.1

## EVREN API setup and security

The EVREN portal used to create API keys is `https://evren.ssyz.org.tr`. The OpenAI-compatible API base URL is:

`https://evren-llmapi.ssyz.org.tr/v1`

Install the APK, open VISION **Settings**, enter your own EVREN API key, read and accept the current EVREN terms, then test the connection. The key is stored encrypted with Android Keystore and is not embedded in the source archive or APK build configuration.

**Never commit an EVREN API key, signing key, user database, or model weights to this public repository.** GitHub Actions does not need your personal EVREN key because the app asks for it after installation.

## What is and is not verified

The CI run verifies source checks, unit tests, and Android compilation. It does not prove live EVREN requests, microphone/camera behavior, background execution, driving-mode behavior, or permissions on a physical phone. Those still require device testing. See `apps/android/TEST_MATRIX_TR.md`, `apps/android/OPEN_SOURCE_REVIEW_TR.md`, and `apps/android/THIRD_PARTY_NOTICES.md`.
