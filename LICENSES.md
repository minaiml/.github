# Licence register — minaiml

Every repository in this organization, with the licence that actually governs it. Read from the
GitHub API on **2026-09-18**; where GitHub reported `NOASSERTION`, the `LICENSE` file itself was read
and the result is marked *(read)*.

Per-repo provenance — upstream, the exact commit forked, the date, the licence — lives in each
repository's own `FORK.json`. This file is the roll-up.

**Nothing here is relicensed.** A licence travels with its code. The only file added to a fork is
`FORK.json`.

## Distribution

- **Apache-2.0** — 32
- **MIT** — 27
- **NONE** — 6
- **LGPL-3.0** — 2
- **AGPL-3.0** — 2
- **GPL-3.0** — 2
- **BSD-3-Clause** — 2
- **MIT OR NCSA** — 1

Total: 74 repositories.

## ⚠ No licence declared upstream

Default copyright applies: all rights reserved by the author. Forking within GitHub is permitted by
[GitHub's Terms of Service](https://docs.github.com/site-policy/github-terms/github-terms-of-service#5-license-grant-to-other-users).
**Redistribution, repackaging and binary releases outside GitHub are not**, and none happen.

| repo | upstream | licence request |
|---|---|---|
| `flash-moe` | `danveloper/flash-moe` | [issue #19](https://github.com/danveloper/flash-moe/issues/19) — asked 2026-09-18 |
| `Flash-iOS` | `Anemll/Flash-iOS` | [issue #1](https://github.com/Anemll/Flash-iOS/issues/1) — opened 2026-09-18 |
| `flash-pi-dsv4` | `danveloper/flash-pi-dsv4` | same author as flash-moe; covered by the #19 thread |
| `Anemll` | `Anemll/Anemll` | not pursued — held under the same terms, no request made |
| `discord-ai-bot` | 2023 fork, not part of the delivery catalogue | not pursued — outside the delivery catalogue |

`.github` is this organization's own configuration repository, not a fork.

## ⚠ Copyleft — obligations attach on distribution

These are fine to hold and to run. They are **not** fine to link into estate software without
accepting their terms, so each is named here rather than discovered later.

| repo | licence | what it means here |
|---|---|---|
| `ChatterUI` | **AGPL-3.0** | Network copyleft. Running a *modified* version as a service obliges you to offer that source to its users. Unmodified fork: no obligation. |
| `mimic3` | **AGPL-3.0** | Same, and it is a **TTS server** — the shape most likely to be wired into a served endpoint by accident. The estate's speech path is audiocpp (Apache-2.0) and espeak-ng; keep it that way unless AGPL is accepted deliberately. |
| `exo` | **GPL-3.0** | Cluster inference. Integrating it into a distributed-node story makes the combined work GPL-3.0. |
| `ellama` | **GPL-3.0** | Emacs client; standalone, low contact surface. |
| `enclosure-picroft` | **LGPL-3.0** | Weak copyleft — dynamic linking against an unmodified library does not infect the caller. |
| `AgentSpeak-speak-agent` | **LGPL-3.0** | Same. |

**No GPL-2.0-only repository is present**, which matters: GPL-2.0-only cannot absorb Apache-2.0 code,
while GPL-3.0 can. That incompatibility does not arise here.

## Resolved from the LICENSE file

GitHub could not map these to a single SPDX identifier and reported `NOASSERTION`. Each was read:

| repo | actual licence | note |
|---|---|---|
| `llamafile` | Apache-2.0 | Mozilla Foundation. Vendors llama.cpp (MIT); vendored parts keep their own terms. |
| `executorch` | BSD-3-Clause | Meta Platforms and Arm Limited. |
| `ncnn` | BSD-3-Clause | Tencent — **except** listed third-party components under other terms. Check before vendoring a subtree. |
| `emscripten` | MIT **or** NCSA | Dual-licensed, both permissive. |
| `python-fire` | Apache-2.0 | Google Inc.; the header format is why GitHub declined to classify it. |
| `cutile-python` | Apache-2.0 | NVIDIA. REUSE-style layout — `LICENSE` points at `LICENSES/` and declares `SPDX-License-Identifier: Apache-2.0`. |

## CUDA Rust (added 2026-09-18)

NVIDIA made Rust a first-class CUDA language on 2026-09-08. All five repositories in this group are
**Apache-2.0** — the cleanest-licensed group in the catalogue.

| repo | upstream | note |
|---|---|---|
| `cuda-oxide` | `NVlabs/cuda-oxide` | **NVlabs, not NVIDIA.** An unrelated 2021 project (`Protryon/cuda-oxide`) shares the name — check the owner. |
| `cutile-rs` | `NVlabs/cutile-rs` | Stable Rust 1.89 + CUDA 13.3, on crates.io. |
| `cutile-python` | `NVIDIA/cutile-python` | The cuTile programming model itself. |
| `grout` | `huggingface/grout` | Hugging Face's LLM inference testbed on cutile-rs. |
| `rust-cuda` | `Rust-GPU/rust-cuda` | Community project since 2021, not an NVIDIA release. |

## Full register

| repo | licence |
|---|---|
| [AgentSpeak-speak-agent](https://github.com/minaiml/AgentSpeak-speak-agent) | `LGPL-3.0` |
| [airllm](https://github.com/minaiml/airllm) | `Apache-2.0` |
| [Anemll](https://github.com/minaiml/Anemll) | **none declared** |
| [BitNet](https://github.com/minaiml/BitNet) | `MIT` |
| [candle](https://github.com/minaiml/candle) | `Apache-2.0` |
| [ChatterUI](https://github.com/minaiml/ChatterUI) | `AGPL-3.0` |
| [cuda-oxide](https://github.com/minaiml/cuda-oxide) | `Apache-2.0` |
| [cutile-python](https://github.com/minaiml/cutile-python) | `Apache-2.0` *(read)* |
| [cutile-rs](https://github.com/minaiml/cutile-rs) | `Apache-2.0` |
| [discord-ai-bot](https://github.com/minaiml/discord-ai-bot) | **none declared** |
| [distributed-llama](https://github.com/minaiml/distributed-llama) | `MIT` |
| [ellama](https://github.com/minaiml/ellama) | `GPL-3.0` |
| [emscripten](https://github.com/minaiml/emscripten) | `MIT OR NCSA` *(read)* |
| [enchanted](https://github.com/minaiml/enchanted) | `Apache-2.0` |
| [enclosure-picroft](https://github.com/minaiml/enclosure-picroft) | `LGPL-3.0` |
| [executorch](https://github.com/minaiml/executorch) | `BSD-3-Clause` *(read)* |
| [exo](https://github.com/minaiml/exo) | `GPL-3.0` |
| [faiss](https://github.com/minaiml/faiss) | `MIT` |
| [Flash-iOS](https://github.com/minaiml/Flash-iOS) | **none declared** |
| [flash-moe](https://github.com/minaiml/flash-moe) | **none declared** |
| [flash-pi-dsv4](https://github.com/minaiml/flash-pi-dsv4) | **none declared** |
| [gallery](https://github.com/minaiml/gallery) | `Apache-2.0` |
| [ggml](https://github.com/minaiml/ggml) | `MIT` |
| [.github](https://github.com/minaiml/.github) | **none declared** |
| [gorilla](https://github.com/minaiml/gorilla) | `Apache-2.0` |
| [grout](https://github.com/minaiml/grout) | `Apache-2.0` |
| [KittenTTS](https://github.com/minaiml/KittenTTS) | `Apache-2.0` |
| [kokoro](https://github.com/minaiml/kokoro) | `Apache-2.0` |
| [ktransformers](https://github.com/minaiml/ktransformers) | `Apache-2.0` |
| [langroid](https://github.com/minaiml/langroid) | `MIT` |
| [Llama-2-Open-Source-LLM-CPU-Inference](https://github.com/minaiml/Llama-2-Open-Source-LLM-CPU-Inference) | `MIT` |
| [llama.cpp](https://github.com/minaiml/llama.cpp) | `MIT` |
| [llamafile](https://github.com/minaiml/llamafile) | `Apache-2.0` *(read)* |
| [LocalAI](https://github.com/minaiml/LocalAI) | `MIT` |
| [maid](https://github.com/minaiml/maid) | `MIT` |
| [mediapipe](https://github.com/minaiml/mediapipe) | `Apache-2.0` |
| [milvus](https://github.com/minaiml/milvus) | `Apache-2.0` |
| [mimic3](https://github.com/minaiml/mimic3) | `AGPL-3.0` |
| [minimal-llm-ui](https://github.com/minaiml/minimal-llm-ui) | `MIT` |
| [mistral.rs](https://github.com/minaiml/mistral.rs) | `MIT` |
| [mlc-llm](https://github.com/minaiml/mlc-llm) | `Apache-2.0` |
| [mlx-lm](https://github.com/minaiml/mlx-lm) | `MIT` |
| [mlx](https://github.com/minaiml/mlx) | `MIT` |
| [MNN](https://github.com/minaiml/MNN) | `Apache-2.0` |
| [models](https://github.com/minaiml/models) | `Apache-2.0` |
| [mycroft-core](https://github.com/minaiml/mycroft-core) | `Apache-2.0` |
| [mycroft-gui](https://github.com/minaiml/mycroft-gui) | `Apache-2.0` |
| [ncnn](https://github.com/minaiml/ncnn) | `BSD-3-Clause` *(read)* |
| [obsidian-bmo-chatbot](https://github.com/minaiml/obsidian-bmo-chatbot) | `MIT` |
| [obsidian-ollama](https://github.com/minaiml/obsidian-ollama) | `MIT` |
| [ollama](https://github.com/minaiml/ollama) | `MIT` |
| [ollama-telegram](https://github.com/minaiml/ollama-telegram) | `MIT` |
| [onnxruntime-genai](https://github.com/minaiml/onnxruntime-genai) | `MIT` |
| [onnxruntime](https://github.com/minaiml/onnxruntime) | `MIT` |
| [open-llms](https://github.com/minaiml/open-llms) | `Apache-2.0` |
| [PALM-E](https://github.com/minaiml/PALM-E) | `Apache-2.0` |
| [peft](https://github.com/minaiml/peft) | `Apache-2.0` |
| [petals](https://github.com/minaiml/petals) | `MIT` |
| [picollm](https://github.com/minaiml/picollm) | `Apache-2.0` |
| [pinecone-python-client](https://github.com/minaiml/pinecone-python-client) | `Apache-2.0` |
| [piper](https://github.com/minaiml/piper) | `MIT` |
| [pocketpal-ai](https://github.com/minaiml/pocketpal-ai) | `MIT` |
| [poetry](https://github.com/minaiml/poetry) | `MIT` |
| [PowerInfer](https://github.com/minaiml/PowerInfer) | `MIT` |
| [python-fire](https://github.com/minaiml/python-fire) | `Apache-2.0` *(read)* |
| [ramalama](https://github.com/minaiml/ramalama) | `MIT` |
| [rust-cuda](https://github.com/minaiml/rust-cuda) | `Apache-2.0` |
| [sherpa-onnx](https://github.com/minaiml/sherpa-onnx) | `Apache-2.0` |
| [transformers.js](https://github.com/minaiml/transformers.js) | `Apache-2.0` |
| [unsloth](https://github.com/minaiml/unsloth) | `Apache-2.0` |
| [vespa](https://github.com/minaiml/vespa) | `Apache-2.0` |
| [web-llm](https://github.com/minaiml/web-llm) | `Apache-2.0` |
| [whisper.cpp](https://github.com/minaiml/whisper.cpp) | `MIT` |
| [wllama](https://github.com/minaiml/wllama) | `MIT` |

---

*Generated 2026-09-18 from the GitHub API. Regenerate after any fork is added, synced or removed —
a licence register that is not re-read is a guess.*
