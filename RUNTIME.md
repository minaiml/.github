# Why a runtime, not another app

**Status: analysis.** The benchmark figures below are other people's published measurements, cited as
such. Nothing here is a BROBOT result. Written 2026-09-19.

---

## The Android field, as measured by others

Published 2026 testing on a Samsung S25 Ultra running Phi-4 Mini:

| app | engine | tok/s | models | what it costs you |
|---|---|---|---|---|
| MLC Chat | MLC, NPU path | **~22** | curated, precompiled only | no GGUF import; models are not portable to any other app |
| Maid | llama.cpp + Vulkan | ~18 | any GGUF | no NPU; rougher UI |
| PocketPal AI | llama.rn + Vulkan | ~16 | any GGUF, Hugging Face built in | slower than the NPU path |
| Layla | CPU | ~14 | curated | CPU only, no import |
| Private AI | CPU | ~13 | curated | CPU only, no configuration |
| Ollama in Termux | CPU | ~10 | the whole Ollama library | 20–30 minutes of terminal setup; killed in the background |

Read the table sideways and the category's real shape appears: **speed and freedom are sold
separately.** The fastest app cannot load your model. The apps that can load your model leave a third
of the speed on the table. And the *only* route to a local OpenAI-compatible API — the thing that lets
another program use the phone as an inference source — is the slowest option, inside a terminal
emulator, and Android kills it when you look away.

Every one of those apps made the same decision: **pick one engine, inherit its ceiling.**

## What reviewers say is still missing

1. **Speed requires lock-in; flexibility requires GGUF.** Nobody offers both.
2. **Background death.** Android vendors kill long-running inference, each in its own way.
3. **Storage fragmentation.** Every app downloads the same multi-gigabyte file into its own private
   storage. Four apps, four copies of the same weights.
4. **The Tensor NPU is closed.** Google reserves it for Google.

The fourth is not ours to fix. The first three are design decisions nobody has made yet.

## The decision BROBOT makes differently

**Hold a registry of engines, not an engine.** For each model and each device, pick the fastest engine
that can actually play it, and *say which one was used*. MLC's speed when a compiled model exists for
this SoC; GGUF's freedom when it does not. The rank order is configuration, never code — a bad engine
is demoted without a rebuild, the same discipline this estate already applies to its speech engines.

That single inversion is what the word *runtime* is doing in the title. The rest follows from it:

| gap | BROBOT's answer | state |
|---|---|---|
| one engine per app | engine cascade with an honest registry | **built** as pure logic; only `llama.rn` is wired |
| no native local API | OpenAI-compatible protocol layer, loopback by default, bearer token, size caps | **built** as a pure protocol layer; the socket server is a native seam, **not built** |
| background death | foreground-service policy, per-vendor exemption guidance, thermal governor with hysteresis | **built** as policy; needs native modules to act |
| four copies of one model | content-addressed shared store, reference counted, hash-verified, Wi-Fi by default | **built** as policy; not wired to the downloader |
| estimates everywhere | measured telemetry that back-solves real bandwidth and replaces the model's guesses | **built**; has never run on a device |
| an island | presents itself to [mindX](https://huggingface.co/spaces/PYTHAI/mindX) as an edge node — charging, on Wi-Fi, cool, opted in, or not at all | **built** as a contract; no live handshake |

**The honest reading of that last column:** this is a decision layer, fully written and executed as
pure logic, sitting on top of one wired engine. It is not yet an APK anyone can install, and it has
never generated a token on a phone. The reasoning is sound and tested; the claim "fastest on Android"
is not one BROBOT can make, and it does not make it.

## What was deliberately not used

- **`timmyy123/LLM-Hub`** is the most ambitious project in the space — text, image, video and music
  generation, RAG, an on-device agent. It is licensed **PolyForm Noncommercial 1.0.0**, which is not an
  open-source licence. Nothing was taken from it and it is not forked here. It is cited because it
  deserves to be.
- **MediaPipe LLM Inference** is in maintenance-only mode by Google's own statement.
  [LiteRT-LM](https://github.com/minaiml/LiteRT-LM) (Apache-2.0) replaces it in the cascade.
- **koboldcpp** is AGPL-3.0; **vLLM** is a datacentre engine. Both covered in
  [PLAYER.md](PLAYER.md).

Forked as clean references: [LiteRT-LM](https://github.com/minaiml/LiteRT-LM) and
[LiteRT](https://github.com/minaiml/LiteRT) (Apache-2.0), and
[local-llms-on-android](https://github.com/minaiml/local-llms-on-android) (MIT — a Kotlin reference
for LiteRT and ONNX Runtime with on-device tokenizers).

---

*Specification: [PLAYER.md](PLAYER.md) · Reasoning: [HYPOTHESIS.md](HYPOTHESIS.md) · Licences:
[LICENSES.md](LICENSES.md) · The build: [minaiml/brobot](https://github.com/minaiml/brobot)*
