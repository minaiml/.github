# Hypothesis: what flash-moe teaches CUDA Rust, and what it doesn't

**Status: hypothesis, not result.** Nothing below has been run. Every claim is tagged
**[measured]** — taken from a source that published a number — or **[inferred]** — reasoned from
hardware architecture and not yet tested. The point of writing it down is to be wrong in public and
find out which.

Written 2026-09-18, ten days after NVIDIA made Rust a first-class CUDA language.

---

## The claim

`flash-moe` runs a 397B-parameter model on a 48 GB laptop. Its four winning techniques are usually
described as one achievement. They are not. **Two of them are properties of the model, and travel to
any hardware. Two are properties of Apple's unified memory, and may invert on a discrete GPU.**

If that split is real, then porting flash-moe to NVIDIA is not a port. It is keeping half and
rewriting the other half against opposite constraints — and `cutile-rs` is unusually well shaped for
the half that has to be rewritten.

## What travels

**Sparse activation.** [measured] Qwen3.5-35B-A3B: 40 layers, 256 experts each, a router picks 8.
~3B of 35B parameters work per token. This is arithmetic about the model. No chip changes it.

**Tiered quantization.** [measured] ~25% of experts do ~80% of the work; hot stay 4-bit, cold drop to
2-bit, the file falls 34% (19 GB → 13 GB). A property of the weight distribution, not the runtime.
[measured] It has a hard floor: at 2-bit throughout, flash-moe emits `\name\` instead of `"name"` and
tool calling breaks. Speed that costs correctness is not delivery.

**The FMA dequant rearrangement.** [measured] Rewriting `(nibble * scale + bias) * x` as
`fma(nibble, scale*x, bias*x)` — precomputing `scale*x` and `bias*x` so one instruction does dequant
and multiply — gave +12%. [inferred] The technique ports; the kernel does not. NVIDIA offers more to
aim at than Metal did: DP4A, tensor cores, `mma`.

## What may invert

This is the part worth testing first, because it is where confidence is highest and evidence is
weakest.

flash-moe's own README is explicit that its pipeline is shaped by unified memory:

> On Apple Silicon, SSD DMA and GPU compute share the same memory controller and cannot be profitably
> overlapped. … Even small background SSD DMA causes disproportionate GPU latency spikes through
> memory controller arbitration. The serial pipeline (GPU → SSD → GPU) is hardware-optimal.

**Prefetching.** [measured] `F_RDADVISE` prefetch scored *net 0%*, logged as "unified memory: SSD DMA
slows GPU −73%". [inferred] On a discrete GPU, VRAM sits across PCIe with its own copy engines, and
overlapping transfer with compute is the entire reason CUDA streams exist. The finding should not
merely weaken — it should **reverse**. Prefetch is expected to be the win.

**Trusting the OS page cache.** [measured] Deleting a 9.8 GB custom cache made flash-moe **38%
faster**, logged as "foundational"; the page cache reaches ~71% hit rate doing ordinary LRU.
[inferred] This one splits. The page cache is *host* RAM, so on a discrete GPU it still helps, but it
no longer ends the story — you owe a host→device copy Apple never charged you for. NVIDIA's own
answer, GPUDirect Storage, goes NVMe→VRAM directly and **bypasses** the page cache. "Trust the OS"
and "use GPUDirect" are opposite instructions, and which wins is an experiment, not an opinion.

**The prediction this makes:** a naive port that keeps flash-moe's serial pipeline on an RTX card
will underperform its own hardware, and the fix will be the thing flash-moe measured as harmful.

## Two tiers become three

[inferred] Apple gave flash-moe one pool and a disk. A discrete GPU gives **three**: VRAM, host RAM,
NVMe. Tiered quantization stops being only a size trick and becomes a **placement policy** —

- **hot 4-bit experts** pinned resident in VRAM,
- **cold 2-bit experts** in the host page cache,
- **the tail** on NVMe.

The hot/cold split flash-moe computed to shrink a file is exactly the split needed to decide what
lives where. It fits NVIDIA's memory hierarchy better than it ever fit Apple's.

[measured] The resident-set arithmetic is not prohibitive: ~6 GB on the M3 Max, 1.4 GB on the iPhone.
[inferred] ~6 GB resident fits an 8 GB laptop RTX, so the shape is viable on hardware people own.

## Why cutile-rs specifically

Not because it is Rust. Because of what its own README says it does:

> extends Rust's ownership discipline across the GPU launch boundary … generated launchers preserve
> ownership while GPU work is in flight … supports synchronous launches, asynchronous pipelines, and
> CUDA graph replay.

Line that up against flash-moe's actual pipeline:

| flash-moe does this by hand | cutile-rs offers |
|---|---|
| **Deferred CMD3** — submit expert compute without waiting so the GPU runs while the CPU prepares the next layer | ownership held across an in-flight launch, enforced by the type system |
| A **fixed per-layer sequence repeated 60×**, re-encoded every layer (0.04 ms encode each) | **CUDA graph replay** — Metal has no equivalent |
| Kernels that are "tiled, SIMD-reduced, shared input cache" | a tile model, natively |

**The safety argument is concrete, not decorative.** The bug that shipped in the iOS port —
`async_preread_weight` checked each expert's read against the wrong size, so every 2-bit cold expert
silently failed the check and was skipped, leaving the model running on half its experts and looping
after ten words — is a partition-with-carried-size bug. It is the exact class that partitioning a
tensor into disjoint typed pieces before launch makes unrepresentable. A model that quietly runs at
half strength is the worst failure mode there is, because it still answers.

## What would falsify this

1. Prefetch on discrete NVIDIA fails to beat the serial pipeline → the unified-memory story was not
   the explanation, and flash-moe found something more general than it claimed.
2. The hot/cold split does not predict VRAM residency well → tiered quantization is a compression
   result only, and the three-tier design has no basis.
3. `cutile-rs` cannot express the deferred-launch pattern without dropping to `unsafe` → the safety
   argument collapses to ergonomics.

## Honest state of the tooling

[measured] `cutile-rs` describes itself as "early stage and under active development: you should
expect bugs, incomplete features, and API breakage." `cuda-oxide` is alpha, on a pinned nightly
toolchain with a dedicated LLVM build. **This is a direction to prototype in, not to port a working
engine onto.**

Prior art to read before writing anything: [grout](https://github.com/minaiml/grout), Hugging Face's
LLM inference testbed already built on cutile-rs, and [mistral.rs](https://github.com/minaiml/mistral.rs),
which uses cutile-rs and is already in this catalogue.

## Method, which is the part actually worth copying

flash-moe's `results.tsv` logs **58 experiments**, and its README gives the twelve that made things
worse as much room as the ones that worked — `mmap` at −5×, `dispatch_io` at −70%, speculative early
routing at −38%.

That is the transferable asset. Not the kernels.

---

*Corrections welcome as issues. A hypothesis that nobody can attack is not a hypothesis.*
