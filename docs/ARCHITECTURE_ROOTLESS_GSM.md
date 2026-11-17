# VISION Rootless GSM Companion Architecture

Status: approved architecture decision; implementation not yet present in this repository.
Target handset: Tecno Camon 20 Pro (CK7n), stock/rootless Android, existing physical SIM retained.
Decision: no bootloader unlock, no Magisk, no privileged/system app, no hidden call recording.

## 1. Product contract

VISION remains one Android app, one Windows companion, one user identity, one shared memory and one EVREN-backed assistant. LIVE, DRIVE, AGENT and PHONE are capabilities inside the same VISION product, not separate apps or separate assistant identities.

The handset keeps the user's existing GSM SIM and normal carrier subscription. A VDS may host LiveKit, the VISION agent gateway, memory/sync services and (only where carrier routing permits) a SIP receptionist. Mobile data and Wi-Fi clients connect outbound to the VDS over TLS/WebRTC; the phone does not need a public IP.

## 2. Hard Android platform boundary

On an unrooted third-party Android app, public APIs can support call metadata/state and call control when the user grants the relevant Android role and permissions. A default dialer can implement InCallService and present/answer/reject/manage calls. CallScreeningService can screen incoming calls and allow/block/silence according to its documented contract.

These APIs do not grant an ordinary app the cellular call's two-way uplink/downlink PCM. CAPTURE_AUDIO_OUTPUT is a privileged permission and cannot be obtained by an ordinary sideloaded app. Do not use hidden APIs, accessibility hacks, speakerphone acoustic loopback, or claims of SIM audio capture as a substitute.

Consequently, VISION must never promise that the unrooted handset alone can let EVREN hear and speak to the remote party inside an ordinary GSM call.

## 3. Supported call modes

### A. Native SIM companion (works without carrier forwarding)

- VISION can request a normal outgoing call through Android Telecom using the user's selected SIM.
- With the user's explicit choice to grant the default-dialer role, VISION can provide an InCallService UI and manage answer/reject/hold/mute/speaker/DTMF where supported by Android and the OEM.
- VISION can show caller identity, contact-specific instructions, call state, and user-approved response suggestions.
- During a normal cellular call, the user speaks. EVREN can prepare talking points before the call and summarize user-entered notes or permitted call metadata afterward. No live remote-party transcription or AI voice injection is claimed.
- Notification access, contacts and call-log access are optional, separately explained, least-privilege grants. Call-log access is not required for basic call control.

### B. AI receptionist for inbound calls (requires carrier feature)

- User configures conditional or unconditional call forwarding from the existing GSM number to a SIP/DID endpoint supplied by a supported carrier/voice provider.
- The VDS receives the forwarded call using LiveKit SIP, starts the VISION Call Agent and streams two-way audio to EVREN-compatible STT/LLM/TTS services.
- The agent announces that it is VISION, an AI assistant, follows a user-authored per-contact policy, handles only the permitted task, and sends a concise summary to the user's VISION app.
- The user must explicitly enable forwarding and choose conditions (always, busy, no-answer, unreachable). Forwarding charges and caller-ID presentation are carrier-dependent.
- The handset's SIM remains installed and unchanged. With unconditional forwarding the handset will not ring for forwarded calls; conditional forwarding is preferable when supported.

### C. AI-originated outbound call showing the existing GSM number (conditional)

- This is supported only if the mobile operator or an authorized voice provider offers an outbound SIP/API service and verifies/authorizes the user's existing number as caller ID.
- VISION must not spoof caller ID or use an unverified arbitrary CLI.
- If no authorized provider supports the existing number, a fully autonomous AI outbound call from that SIM number is not available in the rootless architecture. VISION falls back to launching a normal SIM call for the user, with optional on-screen/speech coaching.
- Do not silently substitute a new number. Any separate number must be explicitly disclosed and accepted by the user.

## 4. VDS components

- LiveKit server: WebRTC rooms and SIP integration.
- TURN (TLS/TCP fallback): connectivity on restrictive mobile networks.
- VISION gateway: authentication, short-lived room tokens, policy enforcement and rate limits.
- Call Agent: per-contact scripts, explicit disclosure, turn-taking, interruption and handoff.
- EVREN adapter: OpenAI-compatible API client; API key stored server-side in a secret manager/environment, never in the public APK or Git repository.
- Memory service: minimum necessary contact preferences, call instructions and summaries; user-controlled retention.
- Push gateway: call state and summaries to Android.
- Observability: redacted technical metrics; no raw audio retention by default.

Mobile clients initiate outbound TLS/WebRTC connections. Never expose EVREN credentials or LiveKit API secrets in the APK. TURN is a relay fallback, not a way to bypass carrier call-audio restrictions.

## 5. Safety, consent and privacy

- The assistant identifies itself as an AI assistant at the start of every AI-handled call.
- No voice cloning or impersonation of the user.
- No recording or transcript retention by default. If recording/transcription is enabled, show a clear user setting and follow applicable Turkish law and the other participant's consent requirements.
- Per-contact allowlist and per-task limits; emergency and sensitive calls are transferred to the user or declined.
- Require confirmation for payments, account changes, commitments, disclosure of sensitive information, and other consequential actions.
- User can interrupt, stop the agent, disable forwarding, revoke Android roles/permissions, and delete retained summaries.
- Use TLS, short-lived credentials, encrypted storage, least privilege, audit events and abuse/rate limits.

## 6. Open-source integration decisions

- LiveKit Agents (livekit/agents, Apache-2.0): VDS real-time agent runtime and SIP call handling.
- LiveKit Android client SDK (livekit/client-sdk-android, Apache-2.0): Android WebRTC client for VISION LIVE sessions; not a GSM audio capture mechanism.
- Android Telecom public APIs: implement a native call companion/default-dialer experience; follow Android SDK contracts instead of copying root-only call-audio code.
- PocketDaemon (HyperCoherence/PocketDaemon): architecture reference only for receptionist/contact-policy concepts; do not copy its root-dependent audio bridge into this rootless app.
- AgentCall (sidinsearch/AgentCall): research reference only. Its AGPL-3.0 license and device-specific/root-dependent implementation make direct code incorporation unsuitable without a separate license review and hardware validation.
- Do not include SIP/telephony code from repositories whose license or device requirements have not been verified.

## 7. Delivery phases and acceptance tests

1. Repository recovery: obtain the actual Android source tree. The current vision-sync repository is only a small build scaffold; it does not currently contain apps/android/ or the referenced 1.5.1 source archive.
2. Android shell: build a clean app, EVREN settings, encrypted credentials, login/session, and shared VISION UI.
3. Native SIM companion: verify default-dialer role, incoming/outgoing call UI, answer/reject and SIM selection on the CK7n. Do not test or claim raw call audio.
4. LIVE: LiveKit WebRTC voice over Wi-Fi and mobile data, TURN/TLS fallback, Turkish STT/TTS, barge-in and background-service behavior.
5. DRIVE: driving-safe voice-only UI, navigation intents and notification handling; no unsafe interaction while moving.
6. AGENT: accessibility-based UI automation only after explicit user enablement, with per-action confirmation and app-specific permission boundaries.
7. AI receptionist: validate conditional call forwarding with the carrier, inbound SIP, two-way speech, AI disclosure, contact policies, transfer/stop, and call summary.
8. Outbound AI: only after an authorized provider confirms caller-ID verification for the existing GSM number. Otherwise keep user-initiated SIM calls as the supported fallback.
9. Windows companion and encrypted sync.
10. Unit/integration tests, physical-device tests, security review, then debug APK and separately signed release APK.

### Release gate

Do not label any feature complete based only on a README, a source archive existing, or a successful static check. The release gate requires a successful GitHub Actions Android build, unit tests, and physical CK7n validation. AI receptionist requires a separate carrier/SIP end-to-end test. A debug APK is not a Play Store-signed release.
