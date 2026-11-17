# VISION 1.5.0 — Android APK

This repository builds the VISION Android application from the source archive uploaded to this repository.

## Upload the source archive

1. Download `VISION-Android-1.5.0-source.zip` from the ChatGPT conversation.
2. Open the `source/` folder and choose **Add file → Upload files**.
3. Upload the ZIP with this exact filename: `VISION-Android-1.5.0-source.zip`.
4. Commit the upload to `main`.

The archive must contain the `apps/android/` directory at its root. Uploading it to the expected path automatically starts **VISION Android APK** in GitHub Actions. You can also start the workflow manually from **Actions → VISION Android APK → Run workflow**.

## What is in 1.5.0

- Existing VISION Live Mode, Driving Mode, CameraX analysis, Turkish voice input, and EVREN speech/chat integration are retained.
- The agent council uses the MIT-licensed Android Agent Runtime 0.4.0 with EVREN's OpenAI-compatible endpoint.
- JARVIS, FRIDAY, EDITH, and ULTRON run as bounded specialist subagents with separate local workspaces. Device actions and native web fetch are disabled in this agent integration.
- The previous EVREN direct-call path remains as a compatibility fallback.
- The API key is entered after installation and is never committed to this public repository.

## Build output

Open **Actions → VISION Android APK → latest run → Artifacts** and download:

- Artifact: `VISION-Android-1.5.0-debug-APK`
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

## Verification limits

CI verifies source checks, unit tests, and Android compilation. It does not prove live EVREN requests, microphone/camera behavior, background execution, driving-mode behavior, or permissions on a physical phone. Those still require device testing. See `apps/android/TEST_MATRIX_TR.md`, `apps/android/OPEN_SOURCE_REVIEW_TR.md`, and `apps/android/THIRD_PARTY_NOTICES.md`.
