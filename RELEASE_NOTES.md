# Aura Transcribe v0.3.1 — Lateral Pill Docking and Smart Keyboard Avoidance

`versionCode 43` (`v0.3.1`) — Release update introducing lateral edge pill docking for the floating overlay and intelligent virtual keyboard avoidance across all Android applications.

Aura Transcribe 0.3.1 introduces lateral edge docking for the floating overlay and intelligent virtual keyboard avoidance across all Android applications.

### Lateral Edge Docking
The floating bubble now docks seamlessly into the nearest screen bezel when released. Upon snapping, the bubble embeds against the display boundary leaving one-third of its surface visible as a tactile pill. Rounded interior corners and a flat exterior bezel edge preserve screen real estate while remaining readily accessible. Tapping the docked pill expands the dictation interface and starts recording in a single motion, enabling instantaneous voice capture. Dragging the pill immediately restores the full circular bubble with natural physics.

### Intelligent Keyboard Avoidance
The overlay dynamically responds to on-screen keyboard (IME) visibility. When the virtual keyboard opens in any foreground application, the floating pill or bubble smoothly translates vertically above the keyboard boundary to prevent visual obstruction. When the keyboard is dismissed, the overlay gracefully returns to its user-defined vertical position. Furthermore, when the dictation panel is expanded while a keyboard is active, oversized bottom controls collapse automatically to provide an unobstructed, compact transcription view.

This update enhances multitasking productivity with fluid gestures, responsive window insets, and zero screen obstruction.

---

# Aura Transcribe v0.3.0-pre — Material 3 Expressive, Bubble Physics & Advanced Dictation

`versionCode 42` (`v0.3.0-pre`) — Pre-release update introducing the Material 3 Expressive token system, refined floating overlay physics, and an updated real-time thought-dictation post-processing engine.

### Material 3 Expressive & Interface Modernization
- **Shape & Elevation Scale Tokens:** Added comprehensive M3 shape corner tokens (`dimens.xml`: none through extra-extra-large 48dp) and elevation level tokens (Level 0 through Level 5).
- **M3 Motion & Interpolators:** Introduced standard and emphasized motion durations (`integers.xml`) alongside canonical cubic-bezier path interpolators in `res/interpolator/` (Emphasized, Emphasized Decelerate/Accelerate, Standard).
- **Component Standardization:** Replaced legacy `<Button>` widgets across all activities (Main, Models, Custom Words, Post-Processing Settings, File Transcribe, Floating Overlay) with `com.google.android.material.button.MaterialButton` using appropriate styles (Filled, Outlined, Tonal, Text, Icon).
- **Consistent Surfaces:** Connected drawable corners (`bg_card.xml`, `bg_voice_panel.xml`, `bg_result.xml`, `bg_ime_key.xml`) to the unified token scale.
- **IME Streaming Display:** Added the M3 result surface (`bg_result.xml`) to the live transcription hypothesis window in the keyboard.

### Floating Bubble Overlay Physics
- **Dynamic Snap-to-Edge:** Replaced linear/decelerating stops with an `OvershootInterpolator(0.8f)` (300ms duration) creating a natural spring settle when releasing the bubble near screen boundaries.
- **Tactile Haptic Feedback:** Integrated subtle haptic responses (`KEYBOARD_TAP`) upon breaking the touch-slop threshold to begin dragging, and upon completing the snap dock against screen edges.

### Advanced Thought-Dictation AI Post-Processing
- **New Real-Time Default Prompt:** Updated the default post-processing system prompt modeled after conversational thought-dictation engines:
  - Strict language fidelity preserving code-switching without unsolicited translations.
  - Granular verbal tic, hesitation, and colloquial filler stripping across supported languages.
  - Smooth resolution of mid-sentence false starts and self-corrections.
  - Contextual formatting with intelligent punctuation, capitalization, and logical paragraph breaks.
  - Full content integrity without artificial summaries or hallucinations.

---

### Platform: Android 17 / API 37

- **`compileSdk` / `targetSdk` raised to 37.** The project previously described itself as "Android 17 Ready (API 35+)" — API 35 is Android 15 and API 37 is Android 17. `minSdk` stays 26 (Android 8.0).
- **Toolchain moved to the API 37 line:** Android Gradle Plugin **9.4.1**, Gradle wrapper **9.8.0**, Android NDK **28.2.13676358**. AGP 8.13.x cannot resolve the API 37 platform (it is tested up to compile SDK 36.1 and looks for `android-37` while the SDK installer creates `android-37.0`), which is why the AGP upgrade is required.

### Release build

- **R8 minification and resource shrinking are enabled for release** (`isMinifyEnabled = true`, `isShrinkResources = true`). Keep rules are scoped in `app/proguard-rules.pro` covering JNI entry points, Java callbacks invoked by native code, framework components referenced from resource XML, and reflective dependencies (ML Kit, Tink, OkHttp).
- A **`benchmark` build type** and a new **`:benchmark` module** (Macrobenchmark + Baseline Profile generation) were added. It is excluded from CI as it requires a connected arm64 device.
- **Mutual exclusion between APK and bundle tasks in a single invocation:** The APK carries the built-in speech model in the base module; the AAB carries it only in the `:model_assets` install-time asset pack. Ambiguous invocations that request both layouts fail at configuration time with a clear error naming the tasks involved. Both release artifacts are verified by opening them: the model must be present exactly once with matching size. Build the two artifacts in separate invocations.

### Material 3 & Visual Coherence

- **Theme driven by Material 3 attributes:** Replaced hardcoded color values with Material tonal schemes, supporting dynamic color and consistent dark mode styling across surfaces.
- **Adaptive content width on large screens:** Settings, models, custom-words, live-subtitle, and disclosure screens inset content following window size classes (16dp on phone, 48dp on tablet/landscape, 140dp on wide screens). Single source via `@dimen/content_horizontal_margin`.
- **48dp minimum touch targets** across the IME key row, voice popup close button, floating overlay controls, and live-subtitle close button.
- The IME record area was resized to keyboard surface proportions, preventing clipping of streaming partials.

### Accessibility

- **Prominent in-app disclosure screen for the optional Accessibility service**, localized across all seven supported locales. Explains scope (focused text field only), purpose, and revocation steps.
- Accessibility service configuration narrowed to focus and window-state events without interactive-window enumeration.
- **Dictation insertion respects cursor and selection:** Inserts at current selection and restores the caret position appropriately.
- **`READ_USER_DICTIONARY` removed:** The platform excludes this permission from the public SDK on modern API levels.

### Fixes

- **Live subtitles foreground service sequence on Android 14+:** Adheres to platform order (`createScreenCaptureIntent` consent -> `startForeground` with `mediaProjection` type -> `MediaProjectionManager.getMediaProjection()`).
- **NPU/GPU options removed from models screen:** The engine executes via CPU (ARM NEON + dotprod + fp16). Outdated UI toggles were removed to reflect the actual runtime execution accurately.
- Audio focus uses `AUDIOFOCUS_GAIN_TRANSIENT` to cooperate with concurrent media playback.
- Debug-only StrictMode diagnostics enabled for main-thread disk/network I/O and leaked resources.
- **Audio-file transcription chunking:** Decodes and transcribes in 30-second bounded chunks, maintaining constant peak memory regardless of total file length.
- **`MainActivity` static leak resolved:** Replaced strong static instance reference with `WeakReference`.
- **Startup disk I/O moved off main thread:** Asset verification and marker checks run on a background thread during cold start.
- **Accessibility click events:** Dictation bubble, subtitle overlay, and custom keys report clicks to accessibility services.
- **Free-space checks:** Account for reclaimable cache when validating storage availability for model downloads.
- **Release build configuration without local keystore:** Builds without a keystore produce unsigned artifacts cleanly instead of halting during configuration.
- **Foreground-service type compatibility on Android 10:** Added fallback to the two-argument `startForeground()` call where `FOREGROUND_SERVICE_TYPE_MICROPHONE` is not supported.
- **Repository metadata and licensing:** Corrected repository URLs and licenses across project descriptors.
- **Unreachable legacy SDK checks removed:** Cleaned up obsolete pre-Android 8.0 conditional paths.
- **Lint triage clean:** 0 errors, with remaining warnings documented and verified against runtime requirements.
- **Type-checked Intent handling for audio sharing:** Uses `IntentCompat.getParcelableExtra(..., Uri.class)` and handles zero-track or speech-free inputs gracefully.
- **File duration bounds tested:** Duration boundary checks covered by JVM unit tests across standard sampling rates.

### Hardening & Technical Invariants

- **Accessibility service declaration:** Explicitly marks `android:isAccessibilityTool="false"` in configuration.
- **RTL layout support:** Enabled `android:supportsRtl="true"` across all mirrored view definitions.
- **On-device S1-mini post-processing provider disabled:** Gated off in UI and preferences pending inclusion of a compatible LLM inference runtime.
- **Release artifact verification:** Release APKs and bundles pass strict verification gates (16 KB page-size alignment, signature validation, model presence and integrity checks).

### Release Integrity & CI

- **Pipeline error handling:** All piped CI gates run under `set -e -o pipefail`.
- **Signature verification gates:** Automated checks reject un-signed artifacts or artifacts signed with debug keys prior to release creation.
- **16 KB page alignment verification:** Uses `zipalign -c -P 16 -v 4` for Android 17 compliance.
- **Dual model validation:** Build tasks and release scripts verify model packaging and SHA-256 integrity independently.
- **Algorithmic benchmarking:** Correctness benchmarks assert parity against naive references and fail on numerical deviation.

---

# Aura Transcribe v0.2.2 — Bluetooth AudioRecord Bridge, Zero-Latency Pre-Warming & Diagnostics

`versionCode 41` — Major Audio & Hardware Release: Native `AudioRecordBridge` communication pipeline, zero-latency Bluetooth pre-warming handshake, dynamic headset visual indicators, real-time microphone diagnostics & sound test meter, poison-tolerant concurrency hardening, and 100% i18n parity.

### Key Changes in v0.2.2

- **Dedicated Native `AudioRecordBridge` Communication Pipeline:** Direct high-performance Java `AudioRecord` bridge operating in `VOICE_COMMUNICATION` mode (16 kHz, 16-bit mono PCM). Leverages direct `ByteBuffer` allocation and native JNI streaming (`pushAudioDirect`) directly into the Rust ASR engine, forcing active microphone capture on wireless Bluetooth earbuds (AirPods, Galaxy Buds, Pixel Buds, Sony, etc.) and headset SCO/BLE devices.
- **Zero-Latency Bluetooth Pre-Warming Handshake:** Automatically pre-warms and establishes the audio communication pipe in the background the moment the on-screen keyboard (`RustInputMethodService`) or floating overlay bubble (`FloatingOverlayService`) appears on screen. Eliminates the initial 300–500 ms Bluetooth connection handshake latency when the user taps record.
- **Dynamic Device Indicators:** Visual real-time indicator on both the keyboard record button and the floating dictation bubble. Dynamically switches between headset icon (`ic_headset`) when Bluetooth audio input is active and microphone icon (`ic_mic`) when recording via the internal microphone.
- **Interactive Microphone Diagnostics & Real-Time Sound Test:** Added a comprehensive sound test section in `MainActivity` Settings. Features a live RMS decibel audio level meter (`MicLevelView`), active recording device label, and one-tap test start/stop to verify microphone levels and active routing before dictating.
- **Multi-Agent Robustness & Concurrency Hardening:**
  - Eliminated memory leaks and ensured bounded direct `ByteBuffer` reuse.
  - Poison-tolerant mutex guards in Rust and atomic session generation validation to prevent race conditions during rapid start/stop cycles.
  - Safe, idempotent JNI pointer cleanup (`cleanupNative`) and lifecycle teardowns across all services and activities.
  - Comprehensive unit test suite `AudioRecordBridgeTest.java` (109/109 JVM tests passed).
- **100% i18n Parity (266 Strings across 6 Locales):** Complete translation coverage across English, German, Spanish, French, Italian, Portuguese, and Russian.

---

### Assets

- `Aura_Transcribe_v0.2.2.apk` (Release APK with bundled Nemotron 3.5 ASR Streaming 0.6B Q8_0 model)
