# VISION — Rootless GSM Companion Architecture

VISION is being designed as one assistant across Android and Windows, with one identity, shared memory, and an EVREN-backed intelligence layer. LIVE, DRIVE, AGENT, and PHONE are capabilities inside the same product.

## Current repository status — 17 November 2026

**This repository is currently a build scaffold, not a complete Android application.** The actual Android source tree is not present on the main branch, and the referenced 1.5.1 source ZIP has not been uploaded. The existing GitHub Actions runs fail at the source-archive existence check, before Android compilation starts. No installable APK has been produced by this repository.

Earlier README text describing 1.5.1 features as integrated should not be treated as proof that those features exist in a buildable app. The implementation must be recovered or recreated, then compiled and tested before any release claim.

## Approved phone architecture

The Tecno Camon 20 Pro (CK7n) will remain **rootless**. No bootloader unlock, Magisk, privileged system app, or hidden telephony API will be used. The existing physical GSM SIM remains in the phone.

The architecture separates two cases:

1. **Native SIM companion:** Android Telecom APIs, with user-granted default-dialer role where required, manage call UI and supported call controls. VISION can identify callers, apply contact rules, prepare response suggestions, and launch normal SIM calls. An ordinary Android app cannot capture the cellular call's two-way audio, so it cannot autonomously speak to the other party inside an ordinary SIM call.
2. **AI receptionist:** When supported by the carrier, conditional/unconditional call forwarding sends incoming calls from the existing GSM number to a SIP endpoint on the VDS. LiveKit and the VISION Call Agent can then hold a real two-way AI conversation. Outbound AI calls displaying the existing GSM number require an operator-authorized SIP/API provider that verifies that caller ID. No caller-ID spoofing is permitted.

Read the detailed decision, boundaries, safeguards, integration references, and acceptance tests in [Rootless GSM Companion Architecture](docs/ARCHITECTURE_ROOTLESS_GSM.md).

## Target system

- **Android:** one VISION app; SIM companion, LIVE voice, DRIVE, AGENT and secure device integrations.
- **VDS:** LiveKit, TURN/TLS fallback, VISION gateway, EVREN adapter, call agent, memory/sync and push services.
- **Windows:** companion client using the same account and encrypted sync.
- **AI:** EVREN API remains the intelligence provider. API keys and LiveKit server secrets stay server-side or in Android Keystore as appropriate; never commit them to this public repository.

## Open-source integration direction

- [LiveKit Agents](https://github.com/livekit/agents) — VDS real-time voice agents and SIP call handling.
- [LiveKit Android SDK](https://github.com/livekit/client-sdk-android) — Android WebRTC client; it is not a GSM call-audio capture mechanism.
- Android Telecom public APIs — native SIM call UI/control within the platform's documented limits.
- [PocketDaemon](https://github.com/HyperCoherence/PocketDaemon) — architecture reference only; its root-dependent audio bridge is not used.
- [AgentCall](https://github.com/sidinsearch/AgentCall) — research reference only; AGPL-3.0 and device-specific/root-dependent constraints mean no direct code incorporation without a separate license and hardware review.

## Build status

The current workflow at [VISION Android APK](.github/workflows/vision-android-apk.yml) is a placeholder build pipeline expecting a source archive at `source/VISION-Android-1.5.1-source.zip`. It cannot produce an APK until the real Android source tree is added and the build configuration is verified.

Release gate:
- Android unit and integration tests pass.
- GitHub Actions produces an APK successfully.
- The APK installs and is tested on the actual CK7n.
- LIVE is tested on both Wi-Fi and mobile data, including TURN/TLS fallback and interruption.
- SIM companion behavior is tested without claiming cellular call-audio access.
- AI receptionist is tested end-to-end with the carrier's call forwarding/SIP route.
- Windows sync, permissions, security, privacy, and recovery behavior are tested.

A debug APK is for testing only. A Play Store or production release requires a separately managed signing process.
