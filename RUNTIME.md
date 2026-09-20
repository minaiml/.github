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
| Ollama in Termux | CPU | ~10 | the whole Ollama library | 20–30 minutes of terminal setup; killed in the background |

Every row above is open source — MLC LLM (Apache-2.0), Maid and PocketPal AI (MIT), Ollama (MIT). The closed-source apps the same review covered are left out on purpose; see *Open source or go away* below.

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
| one engine per app | engine cascade with an honest registry and a licence guard | **written and executed** (86 assertions); only `llama.rn` is wired; not yet independently reviewed |
| no native local API | OpenAI-compatible protocol layer, loopback by default, bearer token, size caps | **written and executed** (97 assertions) as a pure protocol layer; the socket server is a native seam and is **not built** |
| estimates everywhere | measured telemetry that back-solves real bandwidth and replaces the model's guesses | **written and executed** (67 assertions); has never run on a device |
| an island | presents itself to [mindX](https://huggingface.co/spaces/PYTHAI/mindX) as an edge node — charging, on Wi-Fi, cool, opted in, or not at all | **written and executed** (69 assertions) as a contract; no live handshake |
| background death | foreground-service policy, per-vendor exemption guidance, thermal governor | **not built yet** |
| four copies of one model | content-addressed shared store, reference counted, hash-verified | **not built yet** |
| the plan never reaches a user | bridge from the handheld plan to the app's real context parameters | **not built yet** |

**The honest reading of that last column:** this is a decision layer, fully written and executed as
pure logic, sitting on top of one wired engine. It is not yet an APK anyone can install, and it has
never generated a token on a phone. The reasoning is sound and tested; the claim "fastest on Android"
is not one BROBOT can make, and it does not make it.

## Open source or go away

A standing rule of this estate, recorded here as an addendum to the cypherpunk2048 standard: **if it
is not open source, it is ignored.** Not forked, not benchmarked against, not used as a data source,
not cited as prior art to build on. A licence that forbids commercial use, withholds source, or is
simply absent is not a smaller kind of open — it is closed.

In code, that is the engine registry's licence guard: an engine with no SPDX identifier, or one that
is not on the permissive allow-list, is refused at registration with the reason stated. In this
document, it is why the table above has four rows and not six.

## What was deliberately not used

- **`timmyy123/LLM-Hub`** is licensed **PolyForm Noncommercial 1.0.0**, which is not an open-source
  licence. Ignored: not forked, nothing taken.
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
