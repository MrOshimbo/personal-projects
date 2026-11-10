# AI Dunny Project

## 1. Project Goal

Build a physical conversational AI character inside a customized Dunny. The project will be developed incrementally, starting with a standalone voice-capable AI hardware prototype before adding the physical character, movement, portability, and autonomy.

## 2. Version Roadmap

### Version 0.1 — Core AI Hardware Prototype
**Goal:** Build and test the core hardware/software system without the Dunny body.

Target flow:

Microphone → Microcontroller → Wi-Fi → OpenAI API → Microcontroller → Audio output

Initial scope:
- Microphone
- Microcontroller
- Wi-Fi connectivity
- OpenAI API connectivity
- Audio output/speaker
- External power is acceptable for this version

Definition of done:
- The components are physically connected.
- The controller communicates reliably over Wi-Fi.
- Audio input can be captured.
- The system can communicate with the OpenAI API.
- An AI response can be received and played as audio.
- The complete system can perform a basic voice interaction reliably.

### Version 0.2 — Portable Power
**Goal:** Remove dependence on a wall outlet.

Potential additions:
- Battery
- Charging circuit
- Power management
- Battery monitoring
- Battery-life testing

### Version 0.3 — Physical Dunny
**Goal:** Integrate the electronics into the Dunny and give it physical movement.

Potential additions:
- Dunny body
- Motors/servos
- Motor controller/driver
- Mechanical mounting
- Wiring and power distribution

### Version 0.4 — AI-Controlled Movement
**Goal:** Connect the AI's conversational behavior to physical movement.

Potential additions:
- Command/intent representation
- Motor-control software
- Speech + movement coordination
- Movement behaviors triggered by the AI

### Future Versions — Character & Autonomy
Potential additions:
- Personality
- Expressions
- LEDs/display
- More sophisticated movement
- Memory/context
- Autonomous behaviors

## 3. Current Architecture

The project has moved from planning into a working software/audio proof-of-concept.

### Target hardware architecture

```text
Adafruit ICS-43434 I²S microphone + second Adafruit I²S microphone breakout
             │
             ▼
      Adafruit Metro ESP32-S3 Rev B (N16R8)
             │
          Wi-Fi
             │
             ▼
     OpenAI Realtime API
             │
          audio out
             ▼
      ESP32-S3 I²S TX
             │
             ▼
     MAX98357A I²S amplifier
             │
             ▼
        8 Ω speaker (start with one)
```

The purchased speaker set contains four 2 W, 8 Ω speakers. The MAX98357A is a mono amplifier; use one speaker for initial testing and determine a safe configuration before connecting additional speakers.

The eventual system is intended to use manual push-to-talk / turn-on-to-listen interaction.

### Current software-lab architecture

The Raspberry Pi Zero W is being used as a development and audio laboratory before the ESP32-S3 hardware is brought up.

```text
PCM audio file → Raspberry Pi Zero W → Wi-Fi/WebSocket → OpenAI Realtime API
                                                        ↓
                                      streamed PCM audio back to Pi
                                                        ↓
                                                   HDMI speaker
```

The Pi is a temporary development platform, not a replacement for the selected ESP32-S3 architecture.

## 4. Confirmed Hardware Architecture & Bill of Materials

| Component | Purpose | Selected hardware | Status |
|---|---|---|---|
| Main controller | AI, networking, audio, future control | **Adafruit Metro ESP32-S3 Rev B, N16R8 (16 MB flash, 8 MB PSRAM)** | Acquired |
| Microphone 1 | Digital voice capture | **Adafruit ICS-43434 I²S microphone breakout** | Acquired |
| Microphone 2 | Second digital voice input | **Adafruit I²S Microphone Breakout; exact model to identify** | Acquired; identify before wiring |
| Audio amplifier | Digital I²S speaker output | **Adafruit MAX98357A I²S Class-D mono amplifier** | Acquired |
| Speakers | Dunny voice output | **4 × 2 W, 8 Ω speakers** | Acquired; test one first |
| Motor controller | Six future motors | **PCA9685 I²C PWM driver** | Selected |
| Voice interaction | Server-side voice activity detection (VAD) | **Realtime server VAD for initial interaction; physical button optional for later evaluation** | Selected for initial test |
| LEDs | Response/activity indication | Optional | Deferred if GPIO becomes constrained |
| Power | Bench prototype | External power acceptable | Current approach |
| Battery | Portable operation | TBD | Version 0.2 |

### GPIO / interface planning

The earlier GPIO 4/5/6 assignment was proposed for the ESP32-S3-DevKitC-1 and is not yet validated on the Adafruit Metro ESP32-S3 Rev B. Re-map and verify pins against the Metro Rev B pinout before wiring.

The two acquired microphone boards may not share the same channel-selection or wiring behavior. Identify the second breakout and validate each microphone separately before attempting simultaneous capture.

The six future motors will be driven through the PCA9685 over I²C rather than consuming six dedicated ESP32 GPIOs.

These GPIO assignments remain provisional until the complete simultaneous microphone + speaker + control interface is validated on the actual ESP32-S3 hardware.

## 5. Software Architecture

### Confirmed AI approach: OpenAI Realtime API

The project initially explored a conventional pipeline:

```text
Microphone → Speech-to-Text → GPT → Text-to-Speech → Speaker
```

Testing demonstrated that the Realtime API can instead provide a direct audio-to-audio interaction:

```text
Microphone/audio stream ⇄ OpenAI Realtime API ⇄ Speaker/audio stream
```

This is now the preferred architecture for Dunny because it removes separate STT and TTS application stages and supports incremental audio streaming.

### Confirmed Realtime behavior

The Raspberry Pi Zero W successfully demonstrated:
1. Connecting to `gpt-realtime` over WebSocket.
2. Sending PCM16 audio in small chunks.
3. Using explicit/manual turn control rather than server VAD.
4. Committing the input audio.
5. Requesting a response.
6. Receiving streamed `response.output_audio.delta` events.
7. Reassembling the returned PCM audio.
8. Playing the returned speech successfully.

Validated laboratory audio format:
- PCM16
- 24 kHz
- mono
- 20 ms input chunks in the test

The test produced approximately 381,600 bytes of returned PCM audio for one response, and the user successfully heard Dunny's generated voice through the Pi's HDMI audio output.

### Turn detection: start with server VAD

The initial ESP32-to-Realtime audio test and preferred interaction path use server-side voice activity detection (VAD). The ESP32 streams audio while listening; Realtime detects speech and the end-of-speech pause, then commits the input and starts a response automatically.

Manual turn control remains a fallback if server VAD timing or sensitivity does not work well. A physical push-to-talk control can be evaluated later if needed.

## 6. Completed Software / Integration Milestones

### Milestone 1 — OpenAI API connectivity: COMPLETE
The Pi Zero W successfully connected to the OpenAI API and received a GPT response.

### Milestone 2 — Interactive Dunny text interface: COMPLETE
A Python interactive loop was built that accepts user input and produces Dunny responses.

### Milestone 3 — Short-term conversation memory: COMPLETE
The interactive prototype maintains conversation history during a running session.

### Milestone 4 — Text-to-speech output: COMPLETE
The Pi successfully generated Dunny speech using OpenAI TTS and played it through HDMI. Raw PCM output was preferred over streamed WAV for the laboratory setup because it avoided the incomplete/placeholder WAV duration header observed during streaming.

### Milestone 5A — Realtime text over WebSocket: COMPLETE
The Pi successfully connected to `gpt-realtime` and exchanged text using the Realtime WebSocket interface.

### Milestone 5B — Realtime audio round trip: COMPLETE
**COMPLETE — September 28, 2026**

A prerecorded PCM audio file was used as a stand-in for a microphone. The Pi streamed the PCM audio to the Realtime API and received incremental speech audio back. The returned PCM was converted to a Pi-compatible HDMI WAV format and played successfully.

**Result: The user heard Dunny's generated voice from the Realtime API.**

This proves the core streaming voice path at the software/network level before physical microphone and speaker hardware are connected.

## 7. Testing & Current Results

| Test | Result |
|---|---|
| Pi Wi-Fi connectivity | **PASS** |
| OpenAI API connectivity | **PASS** |
| GPT text response | **PASS** |
| Text conversation memory | **PASS** |
| OpenAI TTS generation | **PASS** |
| Pi HDMI audio playback | **PASS** |
| Realtime WebSocket connection | **PASS** |
| Realtime text response | **PASS** |
| Realtime streamed audio output | **PASS** |
| Realtime audio playback | **PASS** |
| Waveshare ESP32-S3-DEV-KIT-N8R8 selected correctly and sketch upload | **PASS** |
| SPH0645 power and wired I²S connections (3.3 V; BCLK GPIO4, WS GPIO5, DOUT GPIO7) | **PASS** |
| Standalone ESP32-S3 I²S microphone capture; samples respond to voice | **PASS** |
| Short WAV capture over USB and voice playback check | **PASS** |
| On-device voice activity detection (VAD) and quiet/speech transitions | **PASS** |
| Two-microphone simultaneous capture on separate I²S channels | **PASS** |
| Stereo WAV capture and separate left/right microphone recordings | **PASS** |
| ESP32-S3 Wi-Fi connectivity; obtained an IP address | **PASS** |
| ESP32-S3 verified TLS/WebSocket upgrade to Realtime endpoint | **PASS** (HTTP 101) |
| ESP32-S3 Realtime authentication and session creation | **PASS** (`session.created`) |
| ESP32-S3 → Realtime microphone audio streaming | **PASS** — left and right mic channels each sent as 4-second mono streams in 200 × 20 ms chunks; Realtime detected speech, committed it, and completed responses |
| Realtime server VAD turn detection with ESP32 microphone audio | **PASS** — speech started/stopped, input committed, and response completed on both mic channels |
| ESP32 receipt of complete Realtime output audio | **PASS** — decoded 326,400 PCM bytes into PSRAM |
| Realtime → ESP32-S3 → MAX98357A playback | **PASS** — direct I²S playback through one 8 Ω speaker for both mic-channel tests; no WAV file saved |
| Push-to-talk hardware | **Not yet tested** |
| Six-motor control | **Future** |
| Battery operation | **Future** |

**Bench prototype note:** The microphone tests above use the Waveshare ESP32-S3-DEV-KIT-N8R8 and SPH0645. Section 8 records the earlier planned Metro/ICS-43434 hardware; that setup has not been validated by these tests.

## 8. Decisions & Rationale

### Decision: Use Adafruit Metro ESP32-S3 Rev B, N16R8
This is the acquired prototype controller, with ESP32-S3, 16 MB flash, and 8 MB PSRAM. It replaces the previously planned ESP32-S3-DevKitC-1-N8R8. Recheck board-specific pin assignments before wiring.

### Decision: Use the acquired Adafruit I²S microphone boards
The acquired first microphone is an ICS-43434. The second is listed as an Adafruit I²S Microphone Breakout; its exact model and channel configuration still need to be identified. Two microphones remain a hard requirement for the finished Dunny. Validate the first microphone, then identify and test the second before simultaneous capture.

### Decision: Start speaker testing with one 8 Ω speaker
Four 2 W, 8 Ω speakers are available, but the MAX98357A is a mono amplifier. Begin with one speaker and verify the amplifier and wiring before deciding whether or how additional speakers can be used.

### Decision: Use a dedicated motor controller
The final Dunny concept requires six motors. A separate motor controller is preferred so the ESP32-S3 can focus on AI, audio, networking, and high-level control rather than directly driving six motors. This also preserves GPIO/interface headroom.

### Decision: Reserve I/O for future expansion
The controller should not be selected or wired so tightly that adding the second microphone, speaker, button, LEDs, or motor controller requires replacing the main board. GPIO assignments therefore remain provisional until the complete interface architecture is mapped.

### Decision: Build the core system before the Dunny
The project will begin as a bench-top hardware/software prototype. This separates the core AI/audio/network engineering from mechanical design.

### Decision: Use incremental versions
Each version should add a meaningful capability without unnecessarily combining multiple difficult engineering problems.

### Decision: Design for future expansion
Version 0.1 component selection should consider the eventual battery-powered, motorized Dunny so that the core controller and architecture can be extended rather than replaced.

## 9. Remaining Engineering Questions

1. Validate ESP32-S3 WebSocket/Realtime audio operation, buffering, and memory/CPU headroom.
2. Identify the exact model and pinout of the second Adafruit I²S microphone breakout.
3. Validate ICS-43434 I²S capture on the Metro, then test the second microphone and determine whether simultaneous capture is supported with these two boards.
4. Determine how microphone input should be reduced to the mono PCM16 stream expected by the Realtime input path.
5. Validate Metro I²S TX → MAX98357A → one 8 Ω speaker and required sample-format conversion; assess the four-speaker set only after the mono path works.
6. Confirm stable simultaneous audio RX/TX.
7. Evaluate whether a physical push-to-talk button is needed alongside server VAD; keep it optional unless testing shows a need.
8. **Design secure production authentication.** Do not embed a long-lived OpenAI API key in firmware intended for distribution: someone with physical access to a device may be able to extract the key and use the API account, potentially creating unauthorized usage costs. For a bench prototype, keep the key private and out of shared source code and firmware wherever practical. For a distributed device, use a small backend that stores the long-lived key and issues the ESP32 a short-lived Realtime client secret for each session. Define the session limits and credential rotation/revocation process as part of that design.
9. Complete the Metro-specific GPIO and power map including I²S, I²C/PCA9685, button, optional LEDs, and future motor requirements.

## 10. Project Status

**Current phase:** Version 0.1 — standalone ESP32-S3 left and right microphones have each passed Realtime server-VAD streaming and direct speaker playback; testing a combined mono signal from both microphones is next.

**Major milestone:** Realtime voice round trip has been proven on the Raspberry Pi Zero W. On the standalone Waveshare ESP32-S3-DEV-KIT-N8R8, both I²S microphone channels are validated locally and have each passed a 4-second Realtime server-VAD stream in 200 × 20 ms chunks. Realtime detected and committed speech, generated responses, and returned PCM audio. The ESP32 played the responses directly through the MAX98357A and one 8 Ω speaker without saving WAV files.

**Immediate next tasks:** Test a combined mono signal from both I²S microphone channels through the server-VAD Realtime and direct speaker playback path, then compare it with the individual-channel results. Continue converting 48 kHz stereo I²S to mono PCM16 at 24 kHz in 20 ms chunks. Evaluate manual turn control only if server VAD timing or sensitivity proves unsuitable. The Metro/ICS-43434 setup and speaker output path in the earlier hardware plan remain unvalidated; test the MAX98357A speaker path independently before adding more speakers. Do not connect all four speakers to the mono amplifier until a suitable configuration is verified.

**Last updated:** November 9, 2026
