# minaiml

### min ai ml — delivery software for models that run on hardware you already own

> **A mixture-of-experts model is mostly idle.** Delivery is the art of leaving the idle part on disk.

Intelligence doesn't require a data center. It requires knowing which parameters are awake.

---

## What runs where

Every number below was measured by the project that reports it, on the hardware named. Nothing is
rounded up, and the slowest row is ours.

| device | model | active / total | tok/s | resident RAM | engine |
|---|---|---|---|---|---|
| MacBook Pro M3 Max, 48 GB | Qwen3.5-397B-A17B | 17B / 397B | **4.36** | ~6 GB (209 GB on SSD) | [flash-moe](https://github.com/minaiml/flash-moe) |
| iPhone 17 | Qwen3.5-35B-A3B | 3B / 35B | **11** | 1.4 GB (12 GB on flash) | [Flash-iOS](https://github.com/minaiml/Flash-iOS) |
| Raspberry Pi 5, 8 GB | DeepSeek-V4-Flash | — | — | — | [flash-pi-dsv4](https://github.com/minaiml/flash-pi-dsv4) |
| 2-core CPU VPS, no GPU | [PYTHAI/mindXtrain39](https://huggingface.co/PYTHAI/mindXtrain39) | 135M, dense | **0.19** | — | Ollama |

That last row is a 135-million-parameter dense model running three hundred times slower than a
397-billion-parameter one. It stays on the page. It is the clearest argument on it: **sparsity, not
size, decides what a small machine can run.**

## How it works

**Sparse activation.** Qwen3.5-35B-A3B has 40 layers, each holding 256 small experts plus one shared
expert. A router picks eight per token. About 3B of 35B parameters do work for any given token; the
other 32B sit still. The 397B model is the same shape at another scale — 60 layers, 512 experts each,
K=4 active.

**Streaming experts off storage.** What every token needs — embeddings, attention, the routers, the
shared expert — is loaded once and stays in RAM. On the phone that is 1.4 GB. The experts, 40 files
of roughly 300 MB, stay on flash and are read on demand through parallel `pread`. Eight experts per
layer, forty layers: 320 small reads per token.

**Trusting the OS.** The flash-moe authors built a 9.8 GB expert cache and then deleted it, and the
engine got **38% faster**. On unified memory, every byte the app holds is a byte taken from the GPU
and from the page cache — and the page cache, doing ordinary LRU, reaches a ~71% hit rate on its own.
At 11 tok/s an iPhone would need over 5 GB/s of reads if every expert came off flash; the flash does
about 1.6 GB/s. The OS is serving most of them from RAM it manages better than we would.

**Tiered quantization.** About 25% of experts do around 80% of the work. Hot experts stay at 4-bit,
cold ones drop to 2-bit and get 44% smaller — the whole file falls 34%, from ~19 GB to 13 GB, so more
of it fits in the cache that is doing the real work. Push it too far and the model stops being
useful: at 2-bit across the board, flash-moe emits `\name\` instead of `"name"` and tool calling
breaks. 4-bit is the production configuration. Speed that costs correctness is not delivery.

Credit where it belongs: the flash family is [danveloper](https://github.com/danveloper)'s work,
building on Apple's *LLM in a Flash*. `results.tsv` in the repo logs 58 experiments, and the README
gives as much room to the approaches that made things worse as to the ones that worked. We fork
that honesty along with the code.

## The catalogue

| class | what it delivers |
|---|---|
| **laptop engines** | [flash-moe](https://github.com/minaiml/flash-moe) · [llama.cpp](https://github.com/minaiml/llama.cpp) · [ollama](https://github.com/minaiml/ollama) · [llamafile](https://github.com/minaiml/llamafile) · [mlx](https://github.com/minaiml/mlx) · [mlx-lm](https://github.com/minaiml/mlx-lm) · [candle](https://github.com/minaiml/candle) · [mistral.rs](https://github.com/minaiml/mistral.rs) · [ramalama](https://github.com/minaiml/ramalama) · [LocalAI](https://github.com/minaiml/LocalAI) |
| **MoE streaming** | [ktransformers](https://github.com/minaiml/ktransformers) · [PowerInfer](https://github.com/minaiml/PowerInfer) · [airllm](https://github.com/minaiml/airllm) · [BitNet](https://github.com/minaiml/BitNet) |
| **phone and edge** | [Flash-iOS](https://github.com/minaiml/Flash-iOS) · [flash-pi-dsv4](https://github.com/minaiml/flash-pi-dsv4) · [Anemll](https://github.com/minaiml/Anemll) · [executorch](https://github.com/minaiml/executorch) · [MNN](https://github.com/minaiml/MNN) · [ncnn](https://github.com/minaiml/ncnn) · [mediapipe](https://github.com/minaiml/mediapipe) · [picollm](https://github.com/minaiml/picollm) |
| **phone apps** | [pocketpal-ai](https://github.com/minaiml/pocketpal-ai) · [ChatterUI](https://github.com/minaiml/ChatterUI) · [enchanted](https://github.com/minaiml/enchanted) · [maid](https://github.com/minaiml/maid) · [gallery](https://github.com/minaiml/gallery) |
| **browser** | [web-llm](https://github.com/minaiml/web-llm) · [transformers.js](https://github.com/minaiml/transformers.js) · [wllama](https://github.com/minaiml/wllama) · [emscripten](https://github.com/minaiml/emscripten) |
| **cluster** | [exo](https://github.com/minaiml/exo) · [distributed-llama](https://github.com/minaiml/distributed-llama) · [petals](https://github.com/minaiml/petals) |
| **on-device speech** | [whisper.cpp](https://github.com/minaiml/whisper.cpp) · [sherpa-onnx](https://github.com/minaiml/sherpa-onnx) · [piper](https://github.com/minaiml/piper) · [kokoro](https://github.com/minaiml/kokoro) · [KittenTTS](https://github.com/minaiml/KittenTTS) |
| **runtime and format** | [ggml](https://github.com/minaiml/ggml) · [onnxruntime](https://github.com/minaiml/onnxruntime) · [onnxruntime-genai](https://github.com/minaiml/onnxruntime-genai) · [mlc-llm](https://github.com/minaiml/mlc-llm) |
| **Rust GPU kernels** | [cuda-oxide](https://github.com/minaiml/cuda-oxide) · [cutile-rs](https://github.com/minaiml/cutile-rs) · [cutile-python](https://github.com/minaiml/cutile-python) · [grout](https://github.com/minaiml/grout) · [rust-cuda](https://github.com/minaiml/rust-cuda) |
| **adaptation** | [unsloth](https://github.com/minaiml/unsloth) · [peft](https://github.com/minaiml/peft) |

## CUDA Rust — the other end of the same problem

NVIDIA made Rust a first-class CUDA language on **2026-09-08**, in two tracks:
[cuda-oxide](https://github.com/minaiml/cuda-oxide) compiles ordinary Rust straight to PTX for the SIMT model
(early alpha, pinned nightly toolchain, CUDA 12.x), and [cutile-rs](https://github.com/minaiml/cutile-rs) is a
safe tile-based kernel DSL that runs on **stable Rust 1.89** with CUDA 13.3 and ships on crates.io. The
programming model itself is [cutile-python](https://github.com/minaiml/cutile-python);
[grout](https://github.com/minaiml/grout) is Hugging Face's LLM inference testbed built on cutile-rs;
[rust-cuda](https://github.com/minaiml/rust-cuda) is the community project that got there first, in 2021.

This is the opposite end of the catalogue from flash-moe, and deliberately so. The flash family wins by
*avoiding* compute — leave the idle experts on disk and let the page cache work. CUDA Rust wins by making the
compute you do keep memory-safe at compile time. A laptop with an RTX card is still a laptop, and
[mistral.rs](https://github.com/minaiml/mistral.rs) in this catalogue already uses cutile-rs, so the two ends
meet in software we already fork.

Worth knowing: `cutile-rs` and `cuda-oxide` live under **NVlabs**, not NVIDIA, and there is an unrelated 2021
project also called `cuda-oxide` (`Protryon/cuda-oxide`, a CUDA wrapper). Check the owner, not the name.

The models these run are forked and licence-pinned at [PYTHAI on Hugging
Face](https://huggingface.co/PYTHAI) — Qwen, GLM, Kimi, Granite, each at a pinned commit with its
licence recorded.

## Every fork says where it came from

Each repository here carries a **`FORK.json`** at its root: upstream, the exact commit we forked at,
the date, the upstream licence, and what the project does in this catalogue. It is the same
discipline PYTHAI applies to model weights, applied to software.

**Four of these have no licence upstream** — [flash-moe](https://github.com/minaiml/flash-moe),
[Flash-iOS](https://github.com/minaiml/Flash-iOS),
[flash-pi-dsv4](https://github.com/minaiml/flash-pi-dsv4) and
[Anemll](https://github.com/minaiml/Anemll). No licence means all rights reserved by the author.
Forking inside GitHub is permitted by GitHub's own terms; redistributing, repackaging or shipping
binaries from them is not, and we don't. We are asking upstream to declare one. Three more
([llamafile](https://github.com/minaiml/llamafile), [executorch](https://github.com/minaiml/executorch),
[ncnn](https://github.com/minaiml/ncnn)) carry a LICENSE that GitHub cannot resolve to a single SPDX
identifier — read the file, don't assume. [ChatterUI](https://github.com/minaiml/ChatterUI) is
**AGPL-3.0**: fine as an unmodified fork, but network-hosting a derivative carries source obligations.
[exo](https://github.com/minaiml/exo) is GPL-3.0.

Nothing here is relicensed. A licence travels with its code. The full roll-up — every repository, the licence that governs it, and the copyleft obligations worth knowing before linking any of it — is in [LICENSES.md](https://github.com/minaiml/.github/blob/main/LICENSES.md).

## The organizations we run

[pythaiml](https://github.com/pythaiml) · [minaiml](https://github.com/minaiml) ·
[mastermindML](https://github.com/mastermindml) · [GATERAGE](https://github.com/GATERAGE) ·
[autoGLM](https://github.com/autoGLM) · [cryptoAGI](https://github.com/cryptoAGI) ·
[cypherpunk4096](https://github.com/cypherpunk4096) · [AgenticPlace](https://github.com/AgenticPlace) ·
[parsec-wallet](https://github.com/parsec-wallet) · [OpenBDK](https://github.com/OpenBDK) ·
[DeltaVML](https://github.com/DeltaVML) · [augml](https://github.com/augml) ·
[trainair](https://github.com/trainair)

On Hugging Face: **[PYTHAI](https://huggingface.co/PYTHAI)** — the models, the training lineage, and
[mindX](https://huggingface.co/spaces/PYTHAI/mindX), the cognitive architecture that trains its own
weights on two CPU cores and publishes every result, including the ones that don't flatter it.

---

<div align="center">

*Part of [PYTHAI](https://pythai.net) — python augmented intelligence*

Built by [Professor Codephreak](https://github.com/Professor-Codephreak)

</div>
