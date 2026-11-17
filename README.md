# VISION 1.5.1 — Android APK

This repository builds the VISION Android application from the source archive uploaded to this repository.

## Upload the source archive

1. Download `VISION-Android-1.5.1-source.zip` from the ChatGPT conversation.
2. Open the `source/` folder and choose **Add file → Upload files**.
3. Upload the ZIP with this exact filename: `VISION-Android-1.5.1-source.zip`.
4. Commit the upload to `main`.

The archive must contain the `apps/android/` directory at its root. Uploading it to the expected path automatically starts **VISION Android APK** in GitHub Actions. You can also start the workflow manually from **Actions → VISION Android APK → Run workflow** after the archive has been uploaded.

## What is in 1.5.1

- Existing VISION Live Mode, Driving Mode, CameraX analysis, Turkish voice input, and EVREN speech/chat integration are retained.
- **New continuous LIVE Voice Mode:** partial Turkish speech recognition, EVREN streamed LLM responses, sentence-by-sentence PCM TTS, VAD barge-in, and Android TTS fallback.
- The live voice engine uses the Apache-2.0 FluxVoice Android runtime 1.0.0 with custom VISION providers. No Deepgram or Cartesia API key is required.
- JARVIS, FRIDAY, EDITH, and ULTRON run as bounded specialist subagents with separate local workspaces. Device actions and native web fetch are disabled in this agent integration.
- Live voice turns use the existing VISION repository, chat history, memory rules, agent privacy boundaries, and approval flow.
- The API key is entered after installation and is never committed to this public repository.

### Live voice architecture note

EVREN's documented API provides completed-file transcription and streamed speech output, but does not document a Realtime WebRTC/WebSocket microphone session. Therefore LIVE mode uses Android SpeechRecognizer for partial/final Turkish STT, EVREN for the streamed LLM response and PCM TTS, and FluxVoice/WebRTC VAD for interruption. It is a streaming conversational voice pipeline with barge-in, not a claim of a direct EVREN WebRTC session.

## Build output

Open **Actions → VISION Android APK → latest run → Artifacts** and download:

- Artifact: `VISION-Android-1.5.1-debug-APK`
- APK: `VISION-Android-1.5.1-debug.apk`
- SHA-256 checksum: `VISION-Android-1.5.1-debug.apk.sha256`

The workflow runs source/security checks, Android unit tests, and Gradle debug build. This is a debug APK for sideloading and testing, not a Play Store-signed release.

## Build configuration

- Application ID: `com.vision.mind` (debug package: `com.vision.mind.debug`)
- Version: `1.5.1` (versionCode 151)
- Minimum Android: API 26 (Android 8.0)
- Compile/target SDK: 35
- JDK: 17
- Gradle: 8.11.1
- Android Gradle Plugin: 8.10.0
- Kotlin: 2.2.10

## EVREN API setup and security

The EVREN portal used to create API keys is `https://evren.ssyz.org.tr`. The OpenAI-compatible API base URL is:

`https://evren-llmapi.ssyz.org.tr/v1`

Install the APK, open VISION **Settings**, enter your own EVREN API key, read and accept the current EVREN terms, then test the connection. The key is stored encrypted with Android Keystore and is not embedded in the source archive or APK build configuration.

**Never commit an EVREN API key, signing key, user database, or model weights to this public repository.** GitHub Actions does not need your personal EVREN key because the app asks for it after installation.

## Verification limits

Static source checks and targeted Kotlin provider smoke-compilation passed in the preparation environment. Full Android Gradle compilation, EVREN live API tests, microphone/camera behavior, Bluetooth routing, and physical-phone tests still need to run in GitHub Actions and on a real device. See `apps/android/TEST_MATRIX_TR.md`, `apps/android/LIVE_VOICE_TR.md`, `apps/android/OPEN_SOURCE_REVIEW_TR.md`, and `apps/android/THIRD_PARTY_NOTICES.md`.
