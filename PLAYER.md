# The model player — specification for 1.0.0

**Status: specification, not shipped code.** Nothing here is built yet. Numbers are cited from
measurements others published; where something is a design decision rather than a measurement, it says
so. Written 2026-09-19.

**Target: a modest Android phone.** Not a flagship. The whole point is the phone people already have.

---

## What a model player is

A media player does not care who encoded the file. You hand it something, it works out the codec,
and it plays. **A model player is the same contract for weights.** You hand it a model; it works out
which engine this device can run it with, which quantization fits the RAM it actually has, and it
plays.

That inversion is the product. Every runner in the catalogue asks the user to already know the
answer — which engine, which build, which quantization, which backend. A player asks the device.

## The device decides, not the user

Measured figures from published mid-range Android testing (2026):

| phone RAM | what actually runs | expected |
|---|---|---|
| **4 GB** | ~1B class only (Gemma 3 1B) | slow but real |
| **6 GB** | 1.7B class at Q4_K_M | 5–15 tok/s |
| **8 GB** | 3–4B class at Q4_K_M | 5–15 tok/s |
| **12 GB+** | 7–8B with Vulkan GPU offload | double-digit tok/s |

Two rules that come out of that and belong in code, not documentation:

- **RAM needed ≈ model file size × 1.5** at runtime. A 4 GB file wants ~6 GB free.
- **The OS takes 3–4 GB.** An "8 GB phone" is a ~4 GB phone for this purpose. The player must budget
  against *available* memory, never advertised memory.

**Q4_K_M is the 2026 mobile default** — roughly 95% of quality at a quarter of the size. The player
picks it unless the device proves it can afford better.

**The floor is ours.** [`PYTHAI/mindXtrain39`](https://huggingface.co/PYTHAI/mindXtrain39) is 135M
parameters and runs on anything with a CPU. It is slow — 0.19 tok/s measured on a two-core VPS — and
it is the model that guarantees the player is never a blank screen on a 4 GB device.

## The engine cascade

Precedent in this estate: voaice ranks native speech engines and takes the first that answers, with
**the ranking as configuration, not code**, so a bad engine is demoted without a rebuild. The player
does the same for inference.

| rank | engine | licence | why |
|---|---|---|---|
| 1 | [MNN](https://github.com/minaiml/MNN) | Apache-2.0 | ships `apps/Android`; consistently strong on mobile silicon |
| 2 | [llama.cpp](https://github.com/minaiml/llama.cpp) | MIT | ships `examples/llama.android`, with **ggml-vulkan** and **ggml-opencl** for Adreno and Mali. Runs any GGUF on the Hub. **The floor that always works** |
| 3 | [MediaPipe LLM Inference](https://github.com/minaiml/mediapipe) | Apache-2.0 | Google's Android path, `.task` bundles, GPU delegate |
| 4 | [ExecuTorch](https://github.com/minaiml/executorch) | BSD-3-Clause | PyTorch on-device, for models shipped that way |

Every engine in the cascade is permissive. That is not an accident — see below.

**Voice, because a phone is not a keyboard.**
[sherpa-onnx](https://github.com/minaiml/sherpa-onnx) (Apache-2.0) for on-device ASR and TTS, with
[KittenTTS](https://github.com/minaiml/KittenTTS) (Apache-2.0, under 25 MB) as the small-footprint
voice. Speaking to a phone is the natural interface; typing to it is the fallback.

## The two suggestions that don't survive contact

**vLLM: no.** It is a server engine — CUDA-first, Python, built for continuous batching across
concurrent requests on datacentre GPUs. Its own strength is throughput under load, which is the
opposite of one person on one phone. This estate already has the evidence: vLLM 0.19.0 is installed
on the mindX node and **has never served**; Ollama carries everything. Apache-2.0 and irrelevant.

**koboldcpp: not for a shipped app.** It is capable and it does run under Termux, but it is
**AGPL-3.0**. Shipping a player built on it makes the player AGPL, and network-serving a modified
version obliges source disclosure to its users. Given the standing rule that *software with the wrong
licence does not belong in this estate's namespaces*, the AGPL path is a deliberate cost, not a
shortcut. llama.cpp is MIT and does the same job.

Neither is a criticism of either project. They are the wrong tool for this device and this product.

**What is also out of scope for 1.0.0:** the flash-moe expert-streaming trick. It is the right idea
on the right hardware — a 48 GB laptop, or an iPhone 17 with 12 GB of experts on fast flash — but a
modest Android has neither the storage headroom nor the flash bandwidth. Sparse-MoE streaming is a
2.0 target, and [the hypothesis](HYPOTHESIS.md) covers what would have to be true first.

## What 1.0.0 ships

1. **Device probe** — available RAM (not advertised), SoC, GPU backend (Vulkan / OpenCL / none),
   free storage. Produces a tier.
2. **Engine cascade** — ranked in config, first engine that loads the model wins, and the player
   **names the engine it used** in the UI. Never a silent fallback.
3. **A shelf, not a model picker** — models the device can actually play, with the ones it cannot
   shown as such and why. Sourced from [PYTHAI](https://huggingface.co/PYTHAI).
4. **Q4_K_M by default**, with the quantization stated rather than hidden.
5. **`mindXtrain39` preloaded** so the player works before any download.
6. **Honest telemetry, locally** — tok/s, time-to-first-token, RAM high-water, thermal state. The
   same numbers [devicebench](https://huggingface.co/PYTHAI) wants.
7. **Voice in and out**, optional, off by default.

## What would make 1.0.0 a failure

- It runs only on the author's phone.
- It reports a model as available and then OOMs — the RAM budget must be conservative and *stated*.
- It hides which engine or quantization was used. A player that lies about what it played is a worse
  product than one that refuses.
- It ships AGPL code without the obligations being understood and accepted.

## Open questions

- **Distribution.** F-Droid fits the licence posture and the audience; Play Store reaches people who
  will never hear of F-Droid. Both is more work than it sounds.
- **Model delivery.** Multi-gigabyte downloads over mobile data need resumability and an explicit
  Wi-Fi-only default.
- **Which shell.** [PocketPal AI](https://github.com/minaiml/pocketpal-ai) (MIT, React Native +
  llama.cpp) and [Google AI Edge Gallery](https://github.com/minaiml/gallery) (Apache-2.0, Kotlin)
  are both in the catalogue and both close to this. **Forking one is probably faster than starting
  from an empty Android project, and 1.0.0 should decide that before writing code.**
- **Thermals.** The iPhone running a 35B model got hot enough to be alarming. A modest Android will
  throttle sooner. Sustained tok/s, not peak, is the number that matters.

---

*A specification is a claim about what is worth building. Issues welcome.*
