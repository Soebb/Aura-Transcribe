# Aura Transcribe v0.3.2 — Optimized 5-bit Quantization (Nemotron 3.5 Q5_K_M)

`versionCode 47` (`v0.3.2`) — Major model efficiency update upgrading the default bundled neural speech recognition model from 8-bit (`Q8_0`) to 5-bit medium k-quant (`Q5_K_M`). Delivers a 25.5% reduction in package and storage size, approximately 210 MB lower runtime memory consumption, and faster streaming inference with full transcription accuracy.

### Key Highlights
- **25.5% Storage Reduction:** Download and disk footprint drops from 751 MB down to 560 MB.
- **Lower Memory & Energy Consumption:** Peak RAM usage is reduced by approximately 210 MB, improving battery life during continuous streaming dictation and preventing memory pressure on mid-range devices.
- **Full Multilingual Accuracy:** Benchmark evaluations across multilingual speech (English, Spanish, French, Portuguese) demonstrate near-identical Word Error Rate (WER) and Character Error Rate (CER) compared to the 8-bit baseline.
- **Sub-200ms Responsiveness:** Preserves instantaneous live transcription with the native 80ms chunk cadence in both the voice keyboard and floating overlay.
- **Reproducible Benchmark Suite:** Integrated evaluation tooling validating model accuracy and resource trade-offs on the FLEURS corpus.
